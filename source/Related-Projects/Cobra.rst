Cobra
=====

Developed by `Gerard Holzmann <https://en.wikipedia.org/wiki/Gerard_J._Holzmann>`_, a computer scientist and researcher at Bell Labs and NASA, Cobra is a static analysis tool that works well for large code bases.
Cobra provides software developers, peer reviewers, testers, and quality assurance personnel with a tool that facilitates searching for patterns in source code to confirm compliance with rulesets, which are typically conformance checks for coding guidelines and standards.

Cobra first performs a lexical analysis of source code to generate a stream of language-level tokens, storing the information in a simple data structure.
Using a token-level approach is fast and allows Cobra to scale up to very large code bases, where using static analyzers that perform a full parse of the source code would be impractical.
Cobra then applies a rule set across the data structure to search for patterns in the code that indicate flaws or issues, such as violations of coding rules.
Cobra is able to evaluate code according to the following rule sets:

* `Common Weakness Enumeration (CWE) <https://cwe.mitre.org/>`_
* `Power of 10 <https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code>`_
* `JPL Coding Standard <https://web.archive.org/web/20111015064908/http://lars-lab.jpl.nasa.gov/JPL_Coding_Standard_C.pdf>`_
* `MISRA C 2012 <https://en.wikipedia.org/wiki/MISRA_C#MISRA_C:2012>`_
* `AUTOSAR C++ 14 <https://www.autosar.org/fileadmin/user_upload/standards/adaptive/17-03/AUTOSAR_RS_CPP14Guidelines.pdf>`_ (subset)


Cobra can be used in one of three modes:

* **Interactive** -- Cobra can be used interactively to load the token-based representation of a complete code base and query patterns in the code using a simple query language
* **Program-based** -- Cobra can execute programs that can contain arbitrary branching and iteration over the token stream to identify more complex types of patterns
* **Toolkit** -- Cobra can be used as an infrastructure for building more elaborate standalone checkers that are compiled separately and linked with the Cobra code that builds the central data structure

How is Cobra used in Space ROS?
-------------------------------

The Space ROS Docker image incorporates the *cobra* command-line tool.
In addition, there is a Python-based wrapper tool, *ament_cobra*, that is used to integrate Cobra into the ament-based build and test system.
That is, when building and executing tests for Space ROS, along with the other source code analysis tools, ament_cobra is invoked to analyze the code using the AUTOSAR C++ 14 ruleset.
For more detailed information about how to use Cobra when developing code for Space ROS, see the :doc:`Using the Cobra Static Analyzer <../How-To-Guides/Use-Cobra>` How-To Guide.

Additional resources
--------------------

If you are interested to learn more about Cobra, there are several resources available:

* `The NASA Cobra site <https://software.nasa.gov/software/NPO-50050-1>`_
* `The Cobra github repository <https://github.com/nimble-code/Cobra>`_
* `Move Fast and Fix Things <http://spinroot.com/cobra/pdf/SMCIT_July_31_2019.pdf>`_ - Slides from the keynote presentation at SMC-IT, July 31, 2019
* `Interactive Code Checking with Cobra <http://spinroot.com/cobra/pdf/Tutorial_July_2019.pdf>`_ - A Cobra Version 3.0 tutorial covering the use of pattern expressions, query commands, inline programs, and the construction of standalone checkers linked with the Cobra front-end
* `Cobra: Fast Structural Code Checking <http://spinroot.com/cobra/pdf/Spin2017.pdf>`_ - A presentation from 2017 that introduces the tool