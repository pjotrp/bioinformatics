## THE SMALL TOOLS MANIFESTO FOR BIOINFORMATICS

This **MANIFESTO** describes motives, rules and recommendations for designing
software and pipelines for current day biological and biomedical research.

Large scale data acquisition in research has led to fundamental challenges in
(1) scaling of calculations, (2) full data integration and (3) data interaction
and visualisation. We think that, because of researchers reaching out to
turn-key solutions, they are losing sight of the importance of building
software on the shoulders of giants and providing solutions in a modular,
flexible and open way.

This manifesto counters current trends in bioinformatics where institutes and
companies are creating monolithic software solutions aimed mostly at end-users.
This manifesto builds on the Unix computer tradition of providing small tools
that can be used in a modular and pluggable way to create efficient
computational solutions where individual parts can be easily replaced.  The
manifesto also counters current trends in software licensing which are not
truly free and open source (FOSS). We think such a manifesto is necessary,
even if history shows that software create with true FOSS licenses will
ultimately prevail over less open licenses, including those licenses for
academic use only.

*Small tools for bioinformatics* consists of researchers, software developers,
statisticians and system administrators who work on biological and biomedical
data

*Small tools for bioinformatics* is concerned about the reinvention of the
wheel across research projects and, for example, the lack of reproducible 
scripted solutions in sequencing pipelines.

*Small tools for bioinformatics* is about small collaborating tools and
software solutions (from here on 'tools') in the Unix tradition.

*Small tools for bioinformatics* creates free and open source software, shares
software solutions, and encourages transparency and reproducibility of results.

*Small tools for bioinformatics* allows the building of modular solutions where
individual tools can easily be understood, tested and replaced.

*Small tools for bioinformatics* is a rescue plan and forward looking central
effort to bring together solutions suitable for big data analysis and create
ways of having these tools interact with each other.

The following simple rules apply to anyone signing up to *Small tools for
bioinformatics*

* Every single tool should do the smallest possible task really well

* Every single tool lives in its own public source code repository

* All tools are free and open source software (FOSS) and come with a license
    approved by the Free Software Foundation (FSF).

* Source code should be easy to read and understand for a competent software
    developer
  
* Error conditions and exceptions should be descriptive and handled in 
    a clear way

* When possible tools should support (Unix) pipes

* When possible tools provide a useful command line interface 

* Tools should avoid system dependencies, such as named (absolute) file paths

* Every single tool comes with a simple build protocol, at least for Linux

* Software installation and deployment dependencies are handled through 
    external package management systems

The following are recommendations 

* Use github or similar for hosting source code

* Use travis or similar for automatic testing

* Provide a package definition for the GNU package system (GUIX)

* All tools that match the criteria of the manifesto will be part of
    standard bioinformatics pipelines across the world

This **MANIFESTO** *Small tools for bioinformatics* is signed by 

1. [Pjotr Prins](http://thebird.nl/)
2. [Raoul Bonnal](https://github.com/helios)
3. [Francesco Strozzi](https://github.com/fstrozzi)
4. [Artem Tarasov](https://github.com/lomereiter)

NOTES

At this point bioinformatics is thriving because of the sequencing bonanza.
Arguably, bioinformatics is in a crisis because existing software engineering
efforts are not really matching the requirements of big data. Not so long ago,
programmers got by writing specialised (small) tools in Perl, Python, R or C.
Today, bioinformatics software engineering has to deal multi-core programming,
with IO bottlenecks, RAM constraints, and demanding users. The overall
challenge has become too large a job for the isolated PhD.D. student trying to
write the next great all-inclusive software solution. We are increasingly
depending on collaborative efforts involving researchers with a background in
biology, statistics, software development and system administration. This is
where the manifesto kicks in - by encouraging these Phi.D. students to come out
of isolation and write small tools that can be bolted together with other
tools.

Also, there is a lack of good work flow management solutions in bioinformatics
pipelines, a gap that sits somewhere between generic cluster management
software, such as Sun Grid Engine, on the one end, and user oriented work flow
solutions, such as Galaxy, on the other.  This gap has led to sequencing
centres around the world creating their own scripted and often fragile
pipelines, thereby reinventing the wheel and ending with a semi-optimal and
hard to maintain work flow solution. This  manifesto calls for a collaborative
effort in changing the way we write software for pipelines and making
such bioinformatics software pipelines simpler, flexible and more manageable.

The manifesto accentuates command line tools with a clear input/output stream
supporting (Unix) pipes, which make the components of a (sequencing) pipeline.
But also for web based tools, and even GUI programs, most of above rules and
recommendations are applicable. By making software solutions self contained and
modular they become pluggable and can be easily replaced by a new generation of
tools. Software is software. Software should be easy to change, replace and
improve. The manifesto champions that philosophy.

