## THE SMALL TOOLS **MANIFESTO** FOR BIOINFORMATICS

This **MANIFESTO** describes motives, rules and recommendations for designing
software and pipelines for current day biological and biomedical research.

Large scale data acquisition in research has led to fundamental challenges in
(1) scaling of calculations, (2) full data integration and (3) data interaction
and visualisation. We think that, because of researchers reaching out to
turn-key solutions, the research community is losing sight of the importance of building
software on the shoulders of giants and providing solutions in a modular,
flexible and open way.

This **MANIFESTO** counters current trends in bioinformatics where institutes and
companies are creating monolithic software solutions aimed mostly at end-users.
This **MANIFESTO** builds on the Unix computer tradition of providing small tools
that can be used in a modular and pluggable way to create efficient
computational solutions where individual parts can be easily replaced.  The
manifesto also counters current trends in software licensing which are not
truly free and open source (FOSS). We think such a **MANIFESTO** is necessary,
even though history suggests that software created with true FOSS licenses will
ultimately prevail over less open licenses, including those licenses
that are for
academic use only.

*Small tools for bioinformatics* consists of researchers, software developers,
statisticians and system administrators who work on biological and biomedical
data

*Small tools for bioinformatics* is concerned about the reinvention of the
wheel across research projects and, for example, the lack of reproducible 
scripted solutions in sequencing pipelines.

*Small tools for bioinformatics* is about small collaborating tools and
software solutions in the Unix tradition.

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

* Source code should be easy to read and understand by any competent software
    developer
  
* Error conditions and exceptions should be descriptive and handled in 
    a clear way

* When possible tools should support (Unix) pipes

* When possible tools provide a useful command line interface 

* Tools should avoid system dependencies, such as named (absolute) file paths

* Every single tool comes with a simple build protocol, at least for Linux

* Software installation and deployment dependencies are handled through 
    external package management systems

The following are recommendations:

* Use distributed revision control &mdash; and sites promoting social coding &mdash; for hosting source code, such as Github.

* Use a [Continuous Integration](http://en.wikipedia.org/wiki/Continuous_integration) tool for automatic testing, such as [Travis](http://travis-ci.org).

* Provide a package definition for the [GNU package system](http://www.gnu.org/software/guix/) (GUIX).

* All tools that match the criteria of the **MANIFESTO** will be part of
    standard bioinformatics pipelines across the world.

This **MANIFESTO** *Small tools for bioinformatics* is signed by 

1. [Pjotr Prins](http://thebird.nl/)
2. [Raoul Bonnal](https://github.com/helios)
3. [Francesco Strozzi](https://github.com/fstrozzi)
4. [Artem Tarasov](https://github.com/lomereiter)
5. [Richard Smith-Unna](https://github.com/Blahah)
6. [John Prince](https://github.com/jtprince)
7. [Francisco Pina-Martins](https://github.com/StuntsPT)
8. [Yannick Wurm](http://yannick.poulet.org)
9. [Karl Broman](http://www.biostat.wisc.edu/~kbroman)
10. [Vince buffalo](http://vincebuffalo.com)
11. [Daniel Standage](http://standage.github.io/)
12. [Felipe da Veiga Leprevost](http://www.leprevost.com.br)
13. [Matt Shirley](https://github.com/mdshw5)
14. [Melissa Gymrek](https://github.com/mgymrek)
15. [Kevin Murray](https://github.com/kdmurray91)
16. [Joep de Ligt](https://github.com/jdeligt)
17. [Morris Swertz](https://github.com/mswertz)
18. [C. Titus Brown](https://github.com/ctb/)
19. [Surya Saha](https://github.com/suryasaha)
20. [Josh Herr](https://github.com/jrherr)
21. [Dan MacLean](https://github.com/danmaclean)
22. [Rob Davey](https://github.com/froggleston)
23. [Hugh French](https://github.com/hughfrench)
24. [Qingpeng Zhang](https://github.com/qingpeng)
25. [Botond Sipos](https://sbotond.github.io)
26. [George Githinji](https://github.com/georgeG)
27. [Ryan Taylor](https://github.com/ryanmt)
28. [Bruno Vieira](http://bmpvieira.com)
29. [Bruno P. Kinoshita](https://github.com/kinow)
30. [Ward Vandewege](https://github.com/cure)
31. [Nathan Weeks](https://github.com/nathanweeks)
32. [Chris Fields](https://github.com/cjfields)
33. [Ilya Sytchev](https://github.com/hackdna)
34. [Eric Talevich](https://github.com/etal)
35. [Daniel Swan](https://github.com/dswan)
36. [Wibowo Arindrarto](https://github.com/bow)
37. [Hilmar Lapp](http://lappland.io)
38. [Peter Cock](https://github.com/peterjc/)
39. [Joachim Baran](https://github.com/joejimbo/)
40. [Konrad Förstner](https://github.com/konrad/)
41. [Paolo Di Tommaso](https://github.com/nextflow-io)
42. [Wim Spee](https://github.com/WimS83)
43. [Wai Yi Leung](http://geno.mics.io/)
44. [Norman Warthmann](https://github.com/warthmann)
45. [Daisie Huang](https://github.com/daisieh)
46. [Saulius Gražulis](https://github.com/sauliusg)
47. [Laurent Gatto](https://github.com/lgatto)
48. [Mateusz Kuzak](https://github.com/mkuzak)
49. [Daniel Mietchen](https://github.com/Daniel-Mietchen)
50. [Ivars Silamiķelis] (https://github.com/ivars-silamikelis)

Please clone, copy, BLOG this document. The original can be found at 
https://github.com/pjotrp/bioinformatics. Also check out the
[wiki](https://github.com/pjotrp/bioinformatics/wiki) which contains
background information and a road map.

## NOTES

At this point bioinformatics is thriving because of the sequencing bonanza.
Arguably, bioinformatics is in a crisis because existing software engineering
efforts are not really matching the requirements of big data. Not so long ago,
programmers got by writing specialised (small) tools in Perl, Python, R or C.
Today, bioinformatics software engineering has to deal with multi-core programming,
IO bottlenecks, RAM constraints and demanding users. The overall
challenge has become too large a job for the isolated student trying to
write the next great all-inclusive software solution. We are increasingly
depending on collaborative efforts involving researchers with a background in
biology, statistics, software development and system administration. This is
where the **MANIFESTO** kicks in - by encouraging researchers and students to come out
of isolation and write small tools that can be bolted together with other
tools.

Also, there is a lack of good workflow management solutions in bioinformatics
pipelines, a gap that sits somewhere between generic cluster management
software, such as Sun Grid Engine, on the one end, and user oriented workflow
solutions, such as Galaxy, on the other.  This gap has led to sequencing
centres around the world creating their own scripted and often fragile
pipelines, thereby reinventing the wheel and ending with a semi-optimal and
hard to maintain workflow solution. This  **MANIFESTO** calls for a collaborative
effort in changing the way we write software for pipelines and making
such bioinformatics software pipelines simpler, flexible,
'antifragile' and more manageable.

The **MANIFESTO** accentuates command line tools with a clear input/output stream
supporting (Unix) pipes, which make the components of a (sequencing) pipeline.
Also for web based tools and GUI programs, most of above rules and
recommendations are applicable. By making software solutions self contained and
modular they become pluggable and can be easily replaced by a new generation of
tools. Software is software. Software should be easy to change, replace and
improve. The **MANIFESTO** champions that philosophy.

## Cite

[![DOI](https://zenodo.org/badge/5591/pjotrp/bioinformatics.png)](http://dx.doi.org/10.5281/zenodo.11321)

To cite the **MANIFESTO** use the following BibTex or equivalent

```ruby
    @MISC{MANIFESTO,
      author        = {Pjotr Prins},
      title         = {Small tools MANIFESTO for Bioinformatics},
      month         = {Feb.},
      year          = {2014},
      doi           = "{10.5281/zenodo.11321}",
      url           = "{http://dx.doi.org/10.5281/zenodo.11321}",
      howpublished  = {https://github.com/pjotrp/bioinformatics/blob/master/README.md}
    }
```

## License

The content of the site (i.e., blog posts and page content) is
licensed under a [Creative Commons Attribution License](http://creativecommons.org/licenses/by/4.0/), i.e., you may use it,
but you must give attribution.


