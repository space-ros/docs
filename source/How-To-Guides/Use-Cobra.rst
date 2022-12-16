###############################
Using the Cobra Static Analyzer
###############################

This guide explains how to use the Cobra static analyzer to check C and C++ source code for potential issues.

.. contents:: Table of Contents
   :depth: 1
   :local:

Using Cobra directly
====================

Cobra is included in the Space ROS Docker image and can be invoked directly using the ``cobra`` command-line tool.
There are several additional resources listed on this site's :doc:`Related Projects page for Cobra<../Related-Projects/Cobra>` that will help you to learn to use Cobra and its many command-line options.

Using Cobra via ament_cobra
===========================

An easy way to use Cobra on your source files is via the ``ament_cobra`` command-line tool.
``ament_cobra`` is a wrapper program for the Cobra tool that simplifies the interface to Cobra and helps to integrate the tool into the `ament <https://github.com/ament>`_-based build system.
``ament_cobra`` provides the following command-line interface:

.. code-block::

    usage: ament_cobra [-h] [--include_dirs [INCLUDE_DIRS ...]] [--exclude [EXCLUDE ...]]
                    [--ruleset RULESET] [--compile_cmds COMPILE_CMDS] [--xunit-file XUNIT_FILE]
                    [--sarif-file SARIF_FILE] [--cobra-version] [--verbose]
                    [paths ...]

    Analyze source code using the cobra static analyzer.

    positional arguments:
    paths                 Files and/or directories to be checked. Directories are searched
                            recursively for files ending in one of '.c', '.cc', '.cpp', '.cxx'.
                            (default: ['.'])

    options:
    -h, --help            show this help message and exit
    --include_dirs [INCLUDE_DIRS ...]
                            Include directories for C/C++ files being checked.Each directory is passed
                            to cobra as '-I<include_dir>' (default: None)
    --exclude [EXCLUDE ...]
                            Exclude C/C++ files from being checked. (default: [])
    --ruleset RULESET     The cobra rule set to use to analyze the code: basic, cwe, p10, jpl,
                            misra2012, C++/autosar. (default: basic)
    --compile_cmds COMPILE_CMDS
                            The compile_commands.json file from which to gather preprocessor
                            directives. This option will take precedence over the --include_dirs
                            options and any directories specified using --include_dirs will be ignored.
                            Instead, ament_cobra will gather all preprocessor options from the
                            compile_commands.json file. (default: None)
    --xunit-file XUNIT_FILE
                            Generate a xunit compliant XML file (default: None)
    --sarif-file SARIF_FILE
                            Generate a SARIF file (default: None)
    --cobra-version       Get the cobra version, print it, and then exit (default: False)
    --verbose             Display verbose output (default: False)

Basic operation
---------------

The ``ament_cobra`` tool:

* Gathers a list of C/C++ filenames to be analyzed from from a list directories and/or files specified on the command line. Each directory is recursively scanned for target files. By default, if no filenames or directories are specified, ``ament_cobra`` scans for target files starting in the current working directory.
* Analyzes the files using using one of a set of available rule sets (by default, ``ament_cobra`` uses Cobra's ``basic`` rule set)
* Saves detailed results to a file, the name of which is based on the ruleset used (``_Basic_.txt``, for example)
* Writes a summary of the issues discovered to stdout

For example, the following invocation of ``ament_cobra``, without any command-line arguments,

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros/src/rcutils/src$ ament_cobra

scans for all C and C++ files in the current working directory (``~/spaceros/src/rcutils/src``) and below, analyzes those files using Cobra's Basic rule set, and produces the following output:

.. code-block::

    1: Shadow, 9 patterns :: Fct names also used as variables
    2: Rval, 1 patterns :: Inconsistent checking return value of function
    3: Ifelseif, 9 patterns :: Missing else at end of if-else-if chain
    4: SideEffect, 3 patterns :: More than one side-effect in expression
    5: Macros, 46 patterns :: Macro argument not enclosed in parentheses
    detailed results are in file _Basic_.txt

    68 errors

The output includes a summary of the issues found and the number of occurances of each issue.
Cobra also saves a detailed summary of the analysis results in the file ``_Basic_.txt``, which is a JSON file that contains an entry for each issue.
For example, the first couple of entries in ``__Basic__.txt`` for this run are as follows:

.. code-block::

    [
    { "type"      :       "Fct names also used as variables",
        "message"   :       "lines 688..688",
        "file"      :       "/home/spaceros-user/spaceros/src/rcutils/src/logging.c",
        "line"      :       688,
        "cobra"     :       "1 10 0"
    }
    { "type"      :       "Fct names also used as variables",
        "message"   :       "lines 687..687",
        "file"      :       "/home/spaceros-user/spaceros/src/rcutils/src/logging.c",
        "line"      :       687,
        "cobra"     :       "1 10 0"
    }
    ...

Selecting files and directories to analyze
------------------------------------------

The positional (non-option) arguments passed to ``ament_cobra`` are expected to be files and/or directories to be analyzed.
Any directories specified are searched recursively for files ending in one of the following filename extensions: ``.c``, ``.cc``, ``.cpp``, ``.cxx``.
For example, to analyze a single file, one can specify the filename on the command line, as show below:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros/src/rcutils/src$ ament_cobra string_array.c
    1: Ifelseif, 2 patterns :: Missing else at end of if-else-if chain
    detailed results are in file _Basic_.txt

    2 errors

Selecting the ruleset to use
----------------------------

Cobra supports analyzing input source code according to multiple rule sets: Cobra's Basic rule set (basic), `Common Weakness Enumeration <https://cwe.mitre.org/>`_ (cwe), the `Power of 10 <https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code>`_ (p10), the `JPL Coding Standard <https://web.archive.org/web/20111015064908/http://lars-lab.jpl.nasa.gov/JPL_Coding_Standard_C.pdf>`_ (jpl), `MISRA C 2012 <https://en.wikipedia.org/wiki/MISRA_C#MISRA_C:2012>`_ (misra2012), and a subset of the `AUTOSAR C++ 14 <https://www.autosar.org/fileadmin/user_upload/standards/adaptive/17-03/AUTOSAR_RS_CPP14Guidelines.pdf>`_  rule set (C++/autosar).
You may specify one of these rulesets using the ``--ruleset`` command-line option.
If the ``--ruleset`` option is provided more than once on the command line, only the last one is respected.
The following example, run while in the ``~/spaceros/src/rcutils/src`` directory, shows how to process any C/C++ files in the current working directory and any subdirectories using the ``p10`` rule set:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros/src/rcutils/src$ ament_cobra --ruleset p10

Example output for the previous command is as follows:

.. code-block::

    Rule 5: counted 9 assertions; it should be > 32
    1: P10_1a, 1 patterns :: use of recursion
    2: P10_1b, 23 patterns :: use of goto setjmp or longjmp
    3: P10_1, 24 patterns :: Rule 1: restrict to simple control flow constructs
    4: P10_2a, 1 patterns :: loop condition must contain comparison
    5: P10_2, 1 patterns :: Rule 2: give all loops a fixed upper bound
    6: P10_3, 85 patterns :: Rule 3: do not use dynamic memory allocation after initialization
    7: P10_4, 6 patterns :: Rule 4: limit functions to no more than 75 lines of text
    8: P10_5, 174 patterns :: Rule 5: use minimally two assertions per function on average
    9: P10_pp, 82 patterns :: macro definitions
    10: P10_6b, 6 patterns :: Limit use of global data
    11: P10_6, 6 patterns :: Limit use of global data
    12: P10_7b, 93 patterns :: failure to check validity ptr param
    13: P10_7, 93 patterns :: failure to check validity ptr param
    14: P10_8a, 54 patterns :: use no more ifdefs than .h files
    15: P10_8b, 37 patterns :: use defines only in .h files
    16: P10_8, 91 patterns :: Rule 8: limit the use of the preprocessor to file inclusion and simple macros
    17: P10_9a, 7 patterns :: typedef hides a pointer dereference
    18: P10_9d, 3 patterns :: use no more than 2 levels of dereferencing (* -> ->)
    19: P10_9, 10 patterns :: Rule 9: limit the use of pointers, use no more than 2 levels of dereferencing
    detailed results are in file _P10_.txt

    797 errors

In this case, because the ``p10`` ruleset was used, detailed output is stored in the file, ``_P10_.txt``.

Generating output files
-----------------------

``ament_cobra`` is able to parse the output file generated by ``cobra`` and generate either JUnit XML and/or `SARIF <https://docs.oasis-open.org/sarif/sarif/v2.1.0/csprd01/sarif-v2.1.0-csprd01.html>`_ files.
JUnit XML files are typically used to interface to a CI system like Jenkins, while SARIF can be used by IDEs such as Visual Studio Code with a SARIF-enabled plugin to allow developers to interactively navigate the discovered issues.
The following command shows how one may process all C/C++ files in the current working directory or below using the ``p10`` rule set and generating both JUnit XML and SARIF output files:

.. code-block::

    $ ament_cobra . --ruleset p10 --xunit-file p10.xml --sarif-file p10.sarif

Specifying preprocessor options
-------------------------------

Cobra is able to invoke the C preprocessor before analyzing source code files.
``ament_cobra`` provides an ``--include-dirs`` option which allows the user to specify a list of directories in which to look for #include files.
``ament_cobra`` translates any directories specified with ``include-dirs`` to ``-I`` options when invoking the C preprocessor.

``ament_cobra`` also has a ``--compile_cmds`` command-line option.
The ``--compile_cmds`` option uses the ``compile_commands.json`` file output by the build system to incorporate any ``-I``, ``-D``, and ``-U`` options that were used for the original build.
The ``--compile_cmds`` option takes precedence over the ``--include_dirs`` options and any directories specified using ``--include_dirs`` will be ignored.
Instead, ament_cobra will gather all preprocessor options from the ``compile_commands.json`` file.
For example, if one first builds a particular package to produce the compile_commands.json output file, such as when using cmake's `-DCMAKE_EXPORT_COMPILE_COMMANDS option <https://cmake.org/cmake/help/latest/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html>`_:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon build --packages-select rcutils --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

One may then pass the compile_commands.json to ``ament_cobra`` using the ``--compile_cmds`` command-line option:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ ament_cobra src/rcutils/src --compile_cmds ~/spaceros/build/rcutils/compile_commands.json

Displaying verbose output
-------------------------

``ament_cobra`` has a ``--verbose`` command-line option that is handy when you want to see the exact command-line ``ament_cobra`` uses when invoking ``cobra``.
For example, adding ``--verbose`` to the previous command line:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros/src/rcutils/src$ ament_cobra --ruleset p10 --sarif-file 2.sarif --verbose

Shows the command-line used to invoke ``cobra`` in the first line of the output:

.. code-block::

    /home/spaceros-user/spaceros/install/cobra_vendor/bin/cobra -C++ -comments -json -f p10 /home/spaceros-user/spaceros/src/rcutils/src/allocator.c /home/spaceros-user/spaceros/src/rcutils/src/array_list.c /home/spaceros-user/spaceros/src/rcutils/src/char_array.c /home/spaceros-user/spaceros/src/rcutils/src/cmdline_parser.c /home/spaceros-user/spaceros/src/rcutils/src/env.c /home/spaceros-user/spaceros/src/rcutils/src/error_handling.c /home/spaceros-user/spaceros/src/rcutils/src/filesystem.c /home/spaceros-user/spaceros/src/rcutils/src/find.c /home/spaceros-user/spaceros/src/rcutils/src/format_string.c /home/spaceros-user/spaceros/src/rcutils/src/hash_map.c /home/spaceros-user/spaceros/src/rcutils/src/logging.c /home/spaceros-user/spaceros/src/rcutils/src/process.c /home/spaceros-user/spaceros/src/rcutils/src/qsort.c /home/spaceros-user/spaceros/src/rcutils/src/repl_str.c /home/spaceros-user/spaceros/src/rcutils/src/shared_library.c /home/spaceros-user/spaceros/src/rcutils/src/snprintf.c /home/spaceros-user/spaceros/src/rcutils/src/split.c /home/spaceros-user/spaceros/src/rcutils/src/strcasecmp.c /home/spaceros-user/spaceros/src/rcutils/src/strdup.c /home/spaceros-user/spaceros/src/rcutils/src/strerror.c /home/spaceros-user/spaceros/src/rcutils/src/string_array.c /home/spaceros-user/spaceros/src/rcutils/src/string_map.c /home/spaceros-user/spaceros/src/rcutils/src/time.c /home/spaceros-user/spaceros/src/rcutils/src/time_unix.c /home/spaceros-user/spaceros/src/rcutils/src/time_win32.c /home/spaceros-user/spaceros/src/rcutils/src/uint8_array.c /home/spaceros-user/spaceros/src/rcutils/src/testing/fault_injection.c
    Rule 5: counted 9 assertions; it should be > 32
    1: P10_1a, 1 patterns :: use of recursion
    2: P10_1b, 23 patterns :: use of goto setjmp or longjmp
    3: P10_1, 24 patterns :: Rule 1: restrict to simple control flow constructs
    4: P10_2a, 1 patterns :: loop condition must contain comparison
    5: P10_2, 1 patterns :: Rule 2: give all loops a fixed upper bound
    6: P10_3, 85 patterns :: Rule 3: do not use dynamic memory allocation after initialization
    7: P10_4, 6 patterns :: Rule 4: limit functions to no more than 75 lines of text
    8: P10_5, 174 patterns :: Rule 5: use minimally two assertions per function on average
    9: P10_pp, 82 patterns :: macro definitions
    10: P10_6b, 6 patterns :: Limit use of global data
    11: P10_6, 6 patterns :: Limit use of global data
    12: P10_7b, 93 patterns :: failure to check validity ptr param
    13: P10_7, 93 patterns :: failure to check validity ptr param
    14: P10_8a, 54 patterns :: use no more ifdefs than .h files
    15: P10_8b, 37 patterns :: use defines only in .h files
    16: P10_8, 91 patterns :: Rule 8: limit the use of the preprocessor to file inclusion and simple macros
    17: P10_9a, 7 patterns :: typedef hides a pointer dereference
    18: P10_9d, 3 patterns :: use no more than 2 levels of dereferencing (* -> ->)
    19: P10_9, 10 patterns :: Rule 9: limit the use of pointers, use no more than 2 levels of dereferencing
    detailed results are in file _P10_.txt

    797 errors

Using Cobra via colcon test
===========================

`colcon <https://colcon.readthedocs.io/en/released/>`_ is a command-line tool used to build and test packages in a workspace.
``colcon`` operates by traversing the directory hierarchy in a workspace, generating a graph of package dependencies, and performing an operation (such as ``build`` or ``test``) on one or more packages.

To use Cobra via colcon test on the Space ROS source code, first launch the Space ROS Docker image from your host system:

.. code-block::

    $ docker run -it --rm osrf/space-ros

The image starts with the user in the ``spaceros`` directory:

.. code-block::

    spaceros-user@5218e73f96c6:~/spaceros$

Here, you'll see the file, ``exact.repos``, which contains the exact versions of each package used when building the Space ROS image.

.. code-block::

    spaceros-user@5218e73f96c6:~/spaceros$ ll
    total 32
    drwxr-xr-x   1 spaceros-user spaceros-user  4096 Dec  9 16:39 ./
    drwxr-x---   1 spaceros-user spaceros-user  4096 Dec  9 16:24 ../
    -rw-r--r--   1 spaceros-user spaceros-user  8485 Apr 16  2020 exact.repos
    drwxr-xr-x 213 spaceros-user spaceros-user 12288 Dec  9 16:39 install/

In order to minimize the size of the Docker image, the Space ROS image doesn't include the source code.
However, you can pull the source code into a ``src`` directory using the following commands:

.. code-block::

    spaceros-user@5218e73f96c6:~/spaceros$ mkdir src
    spaceros-user@5218e73f96c6:~/spaceros$ vcs import src < exact.repos

Now we're ready to build and execute tests for Space ROS.

Running all tests
-----------------

To run all of the Space ROS tests, first perform a full build of the Space ROS source code.
For example,

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

.. note::

    When using Cobra via ``colcon test``, make sure to pass the CMAKE_EXPORT_COMPILE_COMMANDS=ON option to ``cmake`` when building the source, as shown in the above example.
    ``ament_cobra`` will use the resulting ``compile_commands.json`` file to gather the proper preprocessor options when analyzing each source file.

Then, to run all of the tests, which includes which includes executing the unit tests and running the static analyzers, run the following command:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon test

.. note::

    Both ``colcon build`` and ``colcon test`` will take a while complete in this case as they are operating on the entire code base for Space ROS.

Running tests for an individual package
---------------------------------------

It is also possible to target just a single package with ``colcon``.
For example, one may build just the ``rcutils`` package using the following command:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon build --packages-select rcutils --allow-overriding rcutils --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

Tests for ``rcutils`` can then be run using:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon test --packages-select rcutils

Running an individual test
--------------------------

It is also possible using ``colcon`` to target individual tests, such as running only the Cobra static analyzer.
This is accomplished using ``colcon``'s ``ctest-args`` option and specifying which test to run:

.. code-block::

    spaceros-user@c1b264aeb129:~/spaceros$ colcon test --packages-select rcutils --ctest-args -L cobra

Viewing test output
-------------------

In all cases, test output is in the directory associated with each package.
For example, for ``rcutils`` the output is in ``build/rcutils/test_results/rcutils``.
The output includes the SAIRF and JUnit XML files associated with each test.

.. code-block::

    spaceros-user@5218e73f96c6:~/spaceros/build/rcutils/test_results/rcutils$ ls -l *.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user  57622 Dec 16 19:41 clang_tidy.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user   3062 Dec 16 19:43 cobra-autosar.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user  20158 Dec 16 19:43 copyright.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user    835 Dec 16 19:41 cppcheck_logging_macros.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user 999106 Dec 16 19:46 cppcheck.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user    706 Dec 16 19:41 cpplint_logging_macros.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user  15950 Dec 16 19:46 cpplint.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user   1060 Dec 16 19:41 uncrustify_logging_macros.sarif
    -rw-r--r-- 1 spaceros-user spaceros-user  19203 Dec 16 19:46 uncrustify.sarif

You can inspect these files manually or open them using the :doc:`Space ROS Dashboard <Use-The-Dashboard>`

