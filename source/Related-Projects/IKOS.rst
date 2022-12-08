IKOS
====

The `RTCA <https://www.rtca.org/>`_ standard, `DO-178C <https://en.wikipedia.org/wiki/DO-178C>`_, includes an extension, `DO-333 <https://my.rtca.org/productdetails?id=a1B36000001IcfeEAC>`_ , that describes how developers can use static analysis in certification.
DO-333 provides guidance on how formal mathematical methods may be used for the purpose of producing verification evidence suitable for use in certification, listing `Abstract Interpretation <https://en.wikipedia.org/wiki/Abstract_interpretation#:~:text=In%20computer%20science%2C%20abstract%20interpretation,over%20ordered%20sets%2C%20especially%20lattices.>`_ as a possibility to obtain certification credits.

IKOS is a static analysis framework based on the theory of abstract interpretation, and is used to develop static analyses that are both precise and scalable.
IKOS separates concerns such as code parsing, model development, abstract domain management, results management, and analysis strategy, making certification-worthy static analysis accessible to a larger class of static analysis developers.

IKOS uses special compiler and linker settings in order to analyze source code.
To run IKOS on a large project, one uses the ``ikos-scan`` utility, a command line tool that runs the static analyzer over a codebase after performing a regular build.
The ``ikos-scan`` command works by overriding the environment variables CC and CXX to intercept any compiler commands.
Behind the scenes, ``ikos-scan`` builds the original program as well as a special LLVM bitcode (``.bc``) file that is used by the analyzer.
The ``ikos-report`` tool then reads the ``.bc`` file generated in the previous step and carries out various analyses.

IKOS is able to perform many different analyses, including:

* **buffer overflow analysis** - checks for buffer overflows and out-of-bound array accesses
* **division by zero analysis** - checks for integer divisions by zero
* **null pointer analysis** - checks for null pointer dereferences
* **unaligned pointer analysis** - checks for unaligned pointer dereferences
* **uninitialized variable analysis** - checks for read of uninitialized variables
* **signed integer overflow analysis** - checks for signed integer overflows
* **unsigned integer overflow analysis** - checks for unsigned integer overflows
* **shift count analysis** - checks for invalid shifts, where the amount shifted is greater or equal to the bit-width of the left operand, or less than zero
* **pointer overflow analysis** - checks for pointer arithmetic overflows
* **pointer comparison analysis** - checks for pointer comparisons between pointers referring to different objects
* **soundness analysis** - checks for instructions that could make the analysis unsound, i.e miss bugs
* **function call analysis** - checks for function calls through function pointers of the wrong type
* **dead code analysis** - checks for unreachable statements
* **double free analysis** - checks for double free, invalid free, use after free and use after return

How is IKOS used with Space ROS?
--------------------------------

The Space ROS Docker image incorporates the ``ikos`` command-line tool.
In addition, there is a Python-based wrapper tool, ``ament_ikos``, that is used to integrate IKOS into the ament-based build and test system.
Space ROS adds ikos to the common set of linters and analysis tools used by most of the Space ROS core by including it in its forked version of ``ament_lint_common``.
Then, when building and executing tests for Space ROS, along with the other source code analysis tools, ``ament_ikos`` is invoked to analyze any test binaries.

Additional References
---------------------

The following resources are available to learn more about IKOS:

* `IKOS: A Framework for Static Analysis based on Abstract Interpretation <https://jorgenavas.github.io/papers/ikos-sefm14.pdf>`_
* `The IKOS github repository <https://github.com/NASA-SW-VnV/ikos>`_

