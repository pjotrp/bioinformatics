# Workflow management

## Introduction

As part of the 'small tools initiative' the goal is to create a useful and
descriptive workflow language for developing and maintaining
pipelines.

What if we had a way of describing a bioinformatics pipeline
in a visual manner and make it really easy to execute that pipeline?

### Objective:

The idea here is to provide a light-weight syntax that describes the workflow
in a pipeline well. The workflow should be clear and easy to read,
describing how command execute and how they depend on each other. Individual
workflow, tools and components are abstracted away, but in such a way that it
is not too far removed from shell scripting and for every step the input and
output parameters should be explicitly defined, so as to clearly visualise how
these tools are run.

### Background:

In most pipelines there are only a limit number of building blocks that make
up a workflow. First there are
commands that do not depend on each other and can be run in parallel. Then there
commands that do depend on each other, so one should start on completion
of the other. Then there are 'map/reduce' style commands where a run gets
split over multiple jobs and the results are gathered for further processing.
These three workflow 'modules' are the essential building blocks of any pipeline.

Simple bash scripting, at this point pretty much the default in bioinformatics
pipelines, allows for running tools in parallel (sometimes through helpers, such
as make) and on a cluster. What is missing in bash scripting is abstraction
of these actions and the easy handling of dependencies between tasks.

Make has support for dependency handling, implicit error handling, and has some
parallelisation support.  Make can be used to replace bash. The downside of
make is that it represents a very simple language which makes frustratingly
hard to write and read code. The worst aspect of make is that the dependency logic
(rules) are implicit and require the reader to disentangle logic every time.

More recent attempts at merging a modern computer language with make style
logic have evolved, including rake (Ruby) and snakemake (Python). These allow
for easier programming but do not remove the implicitness of logic/rules. With
large projects also these solutions show non-linear growth in complexity,
making systems hard to read and understand.

One other build system which is actually a work flow description, not used in
bioinformatics today, should be mentioned. This system 'Nix' is used for
creating software packages and deploying software in a correct, consistent and
reproducible manner. Nix, and its GNU derivative GUIX, support linear and
parallel building of systems using either Nix, a special language for software
configuration, or GUIX, a full blown scheme lisp implementation. Nix/GUIX can be
considered for building pipelines, even if it has no direct PBS support. The main
reason I am not proposing Nix/GUIX for pipelines is that it is too far removed
from pipeline building today. We need smaller steps in moving forward as a
community.

### Solution:

Here we describe a workflow model that allows bolting a pipeline together by
adding components in an easy and flexible manner, thereby making pipelines
easy to build and maintain.

This workflow management model is language independent and can be implemented
elegantly by any programming language that supports:

1. Anonymous functions
2. Key-value parameter lists
3. Non-blocking forking of commands

and optionally 

* Futures/continuations
* Composition

Examples of such languages are JavaScript, Coffeescript, Lisp, Python and Ruby. Here
we give Ruby examples (note: add JS and Python later).

## Commands

A command simply represents invocation of one or more (small) chained tools.
Such a command is normally a method defined in a workflow module. An example
for invocation of blastp would be

    blastp

Special invocation, when not controlled by parameters, could be descriptive
names, such as

    blastp_to_xml

Or better (to avoid name space pollution and to make routines pluggable) a
specialisation within the BlastP module

    BlastP::to_xml

With support of composition it could be

    blastp.to_xml

Sometimes there are chained tools in one command, e.g. 'samtools view in.bam |grep chr11', which could be named

    samtools_view_into_grep

or modular

    Samtools::view_into_grep

(Note for implementer: view_into_grep could actually be implemented by
meta programming)

With composition support which allows unlimited chaining of commands,
it could be written as

    samtools_view.grep

## Passing parameters to commands

Parameters are passed as descriptive blocks of key-value pairs. The key can be a 
switch to the underlying tool

```ruby
    blastp(infile: 'file.fa')
```

Actually this is not that descriptive, as it hides a number of underlying
assumptions.  What is the database, for example? A better description would be

```ruby
    blastp(infile: 'file.fa', db: 'nr')
```

And to turn the output into XML

```ruby
    blastp(infile: 'file.fa', db: 'nr', '--out': 'xml')
```

If tools are chained an extra descriptive layer can be introduced. E.g.,

```ruby
    Samtools::view_into_grep({ 
                             samtools: { infile: in.bam },
                             grep:     'chr11' 
                          })
```

Which translates to the shell invocation of

```sh
    samtools view in.bam|grep chr11
```

Direct shell invocations are also possible, but use a wrapper to deal
with error conditions and completion

```ruby
    shell('samtools view in.bam|grep chr11')
```

which writes to STDOUT, which is used for pipes (see below). If writing to
another file, we could write directly

```ruby
    shell('samtools view in.bam|grep chr11 > test.out')
```

But, if the method supported it, this is probably the best way to write it

```ruby
    Samtools::view_into_grep({ 
                             samtools: { infile: in.bam },
                             grep:     'chr11',
                             outfile:  'test.out'
                          })
```

## Non blocking commands

In principle commands that do not have dependencies and have a callback are non
blocking.  So, if you run

```ruby
    blastp_to_xml(-> {} )
    samtools_view_into_grep(-> {})
```

they will run in parallel, or get submitted to PBS in sequence without 
waiting for a result. These commands are non-blocking. The version without
a callback is actually blocking:

    blastp_to_xml
    samtools_view_into_grep

this is to make the transition from shell programming somewhat smooth.

To run non-blocking commands only once write

```ruby
    once blastp_to_xml( -> {} )
    once samtools_view_into_grep ( -> {} )
```

Once (or once-only) makes sure that if the inputs to the command have not
changed, and the results have been calculated already, they are not calculated
again. If the inputs change (e.g. the input FASTA file) the command is forced
to run again as well as the commands that depend on it.

To submit jobs to a cluster we also have the submit method and the (optional)
once method

    submit once blastp_to_xml
    submit once samtools_view_into_grep

which do not require a callback. If you supply a callback it will be 
called on completion of the submitted job.

## Chained commands (blocking dependencies)

When commands are chained through callbacks, i.e., they have dependencies, they
wait for results to continue. The syntax is

```ruby
    blastp_to_xml( -> samtools_view_into_grep )
```

Basically, the method samtools_view_into_grep is called on completion of 
blastp_to_xml.

To run commands only once write

```ruby
    once blastp_to_xml( -> once samtools_view_into_grep )
```

as was shown earlier.  To submit them to PBS write

```ruby
    submit once -> blastp_to_xml( -> submit once samtools_view_into_grep )
```

Which guarantees that samtools_view_into_grep is submitted after completion of blastp_to_xml.

With the once function parameters are moved forward, so

```ruby
    once({ 
               samtools: { infile: 'in.bam' },
               grep:     'chr11' 
         }, -> samtools_view_into_grep
    )
```

which can be simplified to

```ruby
    once({ 
               samtools: 'in.bam',
               grep:     'chr11' 
         }, -> samtools_view_into_grep
    )
```

and, possibly, even further to

```ruby
    once( samtools_view_into_grep, samtools: 'in.bam', grep: 'chr11' )
```

here we suppose that passing in a string, rather than a key-value combination,
it is passed on literally to the command. Likewise an array will be passed
on as literal strings, e.g.,

```ruby
     pfff ['in1.bam','in2.bam']
```

would calculate a pfff checksum on each file.

## Map/reduce commands (parallelism/clusters)

Map/reduce is the concatenation of dependencies with 'when', so

```ruby
    when( submit(blastp('in1.fa'), submit(blastp('in2.fa'))), -> collect )
```

where the collect function is called after completion of the other jobs.
Arguably, naming-wise, we could replace 'when' with 'map' and the callback with
'reduce'. But at this point we propose the universal 'when' which is also
used for dependencies.

## Splitting (cluster) jobs

When the number of jobs is not know in advance, use a list of functions to
build up the dependencies. For example, to split a FASTA file for blasting

```ruby
    blasts = [] # Initialise list of functions
    split_fasta.each { |fn|
      blasts.push -> { submit(blastp(fn)) }
    }
    when (blasts, -> blastxmlparser)
```

## Using a scratch disk

When submitting jobs to worker nodes it sometimes pays to copy files
to a local disk. scratch copies a file or directory, ascertaining
that there is enough space and returning a (temporary) filename, e.g.,

```ruby
    scratch(bam, -> { |localbam| do_something } )
```

The callback gets run with the localbam file and the copied file also gets
removed after completion. If you want a more serial approach you can 
do

    localbam = scratch(bam)
    do_something

but then you have to remove the file yourself with a manual call to 

    scratch_cleanup

## Full example

Currently a bash script using once-only and error_exit checking could be

```sh
    normal=${normal%.*}_rmdup.bam
    tumor=${tumor%.*}_rmdup.bam

    for x in $normal $tumor ; do 
      echo "==== Index with Samtools $x ..."
      echo "$sambamba index $x"| $onceonly --pfff -d . -v
      [ $? -ne 0 ] && exit 1
      echo "==== Index fasta with samtools ..."
      echo "$samtools faidx $refgenome"|$onceonly --pfff -d . -v
      [ $? -ne 0 ] && exit 1
      echo "==== Create samtools mpileup of $x"
      # check -E option
      # echo "$samtools mpileup -B -q $phred -f $refgenome -l $bed ../$x > $x.mpileup"|$onceonly --pfff -v -d varscan2 --skip $x.mpileup
      echo "$samtools mpileup -B -q $phred -f $refgenome -l $bed ../$x > $x.mpileup"|$onceonly --pfff -v -d varscan2 --skip $x.mpileup
      [ $? -ne 0 ] && exit 1
    done
```

This can be simplified with our workflow, including abstraction of 
echo statements, to

```ruby
    # inputs are normal, tumor, ref, bed and phred 
    normal=File.basename(normal,'.bam')+'_rmdup.bam'
    tumor=File.basename(tumor,'.bam')+'_rmdup.bam'
    [tumor,normal].each { |bam|
      once(sambamba_index fn: bam)
      once(sambamba_index_fasta fn: ref)
      once(samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam)
    }
```

Not only is this version more descriptive, but also it is much shorter and
less error prone. As the indexing of the bam and fasta file do not depend
on each other they can be run in parallel. The mpileup, however, depends on the
other two, so we can create the parallel version with the 'when' function:

```ruby
    normal=File.basename(normal,'.bam')+'_rmdup.bam'
    tumor=File.basename(tumor,'.bam')+'_rmdup.bam'
    [tumor,normal].each { |bam|
      when( once(sambamba_index fn: bam),
            once(sambamba_index_fasta fn: ref), 
            -> once(samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam)
    }
```

Cool, or what? Actually for the final version we decide the 'once' is implicit, as
this is the expected behaviour - we only want to run tools once. So, if 
a tool is to be run every time we say 'force' instead:

```ruby
    normal=File.basename(normal,'.bam')+'_rmdup.bam'
    tumor=File.basename(tumor,'.bam')+'_rmdup.bam'
    [tumor,normal].each { |bam|
      when( force(sambamba_index fn: bam),
            sambamba_index_fasta fn: ref, 
            -> samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam
    }
```

where the bam file is indexed on every run of the workflow. In this final
version the mpileup executes after indexing both files, which run in
parallel.

So, what happens when the next command has to run that depends on the previous.
The shell version looks like:

```sh
    echo "java -jar ~/opt/lib/VarScan.v2.3.6.jar processSomatic $normal-$tumor.varScan.output.snp"|$onceonly -v -d varscan2 --skip $normal-$tumor.varScan.output.snp
    [ $? -ne 0 ] && exit 1
```

which we make into a single call

    varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp'

The full dependency can be put in a callback

```ruby
    when( ->
      [tumor,normal].each { |bam|
        when( force(sambamba_index fn: bam),
              sambamba_index_fasta fn: ref, 
              -> samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam
      },
      -> varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp'
    )
```

This means workflow dependencies can be added in any combination in any depth.
To make the workflow easier to understand the tree of dependencies can be
flattened by calling into functions instead of blocks. Putting the indexing and
pileup into a method we can write:

```ruby
    def mpileup(tumor,normal)
      [tumor,normal].each { |bam|
        when( force(sambamba_index fn: bam),
              sambamba_index_fasta fn: ref, 
              -> samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam
      }
    end

    when( mpileup(tumor,normal), 
      -> varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp'
    )
```

but it may actually be nicer to create a 'future' which could read as

```ruby
    mpileup = future {
        [tumor,normal].each { |bam|
          when( force(sambamba_index fn: bam),
                sambamba_index_fasta fn: ref, 
                -> samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam
        }
      }

    mpileup.calc {
      varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp'
    }
```

so, that a deep tree actually turns into a flat list of linear commands, 
but essentially it does the same thing. 

A future is a kind of 'lazy' ahead-evaluation in another thread. Unlike a
'lazy' call, where a value gets realised at actual invocation, a future starts
processing as soon as the future is defined, i.e., the thread starts. When
realising the future value the process blocks until the calculation completes. See
http://tx.pignata.com/2012/11/concurrency-patterns-in-ruby-futures.html for
an example.

Flattening it out further we could write

```ruby
    indexes = future { 
        [tumor,normal].each { |bam|
          sambamba_index fn: bam
          sambamba_index_fasta fn: ref
        }
      }

    mpileup = future {
        indexes.calc {
          samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam
        }
      }

    mpileup.calc {
      varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp'
    }
```

Here tastes may differ. Using futures flattens the tree, which makes the
workflow easier on the eye and more readable. But the 'future' workflow logic
introduces another layer of indirection, which may not be the taste of
everyone. A little syntactic sugar, however, can come a long way:

```ruby
    indexes = run { 
        [tumor,normal].each { |bam|
          sambamba_index fn: bam
          sambamba_index_fasta fn: ref
        }
      }

    mpileup = run {
        after indexes do { samtools_mpileup quality: phred, ref: ref, bed: bed, fn: bam }
      }

    after mpileup do { varscan2_process_somatic normal: normal, tumor: tumor, output: normal+'-'+tumor+'.varScan.output.snp' }
```

and it becomes more digestible for the human eye and mind. For sure it has
become a lot easier to digest than the original shell script. Remember:

```sh
    normal=${normal%.*}_rmdup.bam
    tumor=${tumor%.*}_rmdup.bam

    for x in $normal $tumor ; do 
      echo "==== Index with Samtools $x ..."
      echo "$sambamba index $x"| $onceonly --pfff -d . -v
      [ $? -ne 0 ] && exit 1
      echo "==== Index fasta with samtools ..."
      echo "$samtools faidx $refgenome"|$onceonly --pfff -d . -v
      [ $? -ne 0 ] && exit 1
      echo "==== Create samtools mpileup of $x"
      echo "$samtools mpileup -B -q $phred -f $refgenome -l $bed ../$x > $x.mpileup"|$onceonly --pfff -v -d varscan2 --skip $x.mpileup
      [ $? -ne 0 ] && exit 1
    done
    echo "java -jar ~/opt/lib/VarScan.v2.3.6.jar processSomatic $normal-$tumor.varScan.output.snp"|$onceonly -v -d varscan2 --skip $normal-$tumor.varScan.output.snp
    [ $? -ne 0 ] && exit 1
```

Not only is this shell program hard to digest, it also actually does less work!

## Discussion

What we described here is a workflow engine. Workflow engines are usually
implemented in the form of a persistent state machine. This means state is
recorded in some database or tool somewhere on the network. Here, because of
the use of once-only, an external state machine is not required. The state is
recorded for each step in the pipeline (in fact, job executed or job not executed).
The dependencies between steps (the flow) are explicitly defined by the
callback structure above. This leads to 

1. a system that does not depend on an external state machine
2. a system that can be stopped and restarted at any point
3. a system that can be rerun to see where a problem occurred

one of the greatest assets of such a pipeline is that when a minor change
has to be made somewhere in the workflow, not all steps have to be rerun. This
makes it a great system for developing and tuning new pipelines.

The critical contribution of the workflow engine, in comparison to shell
scripting, is the handling of dependencies between jobs through callbacks. This 
is a model that is extensively used in, for example, nodejs and D3. All commands
are non-blocking, and if a command has to wait for another it will simply be
added as a callback on completion. This allows for complicated task dependency
trees in an elegant way.

The second critical contribution is the notion of explicit parameter lists.
Rather than building up standard command lines, all configuration parameters of
a tool are passed in the form of a descriptive JSON key-value hash. This is
more robust than shell scripting, not only by being more descriptive, but also
avoiding the escaping of quotes. It is also far clearer than passing a list of
arbitrary parameters to a function. For unanticipated use of command line
switches, these can be passed through by making the key contain the option,
as in '-c': 'test.conf'. Creating a mapping for '-c' in the form of config:
'test.conf', however, is more descriptive and is encouraged.

The third critical contribution is further abstraction of higher level pipeline
functionality, such as once-only, scratch-disk, software packaging,
distribution of binaries to nodes, and submission to cluster job management
systems and parallelisation tools.

Conceptually this work flow engine mostly uses the facilities of an existing
well-tested computer language. Creating a new 'language' for workflows is
seductive, but a new language will always lack the power and attraction of an
existing language. Similar successful DSLs based on Ruby, for example, are
Chef, Homebrew and RoR/ERB. Ruby allows for above syntax to be implemented. A
language that could have an even cleaner representation would be Coffeescript.

It may be possible to implement some syntactic sugar to remove some language
properties that interfere with readability, i.e.  move the code syntactically
towards shell scripting, but that is for later. Also a high-level tool could
be written that allowed 'drawing' pipelines, rather than scripting them. But 
that would be future work.


