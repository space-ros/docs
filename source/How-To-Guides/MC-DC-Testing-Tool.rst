Using the MC/DC Testing Tool
============================

The `MC/DC checker <https://gtd-gmbh.gitlab.io/mcdc-checker/mcdc-checker/>`_ analyzes C/C++ code and assesses whether decisions in the code are formulated in a way that enables ``gcov`` to show MC/DC coverage.

What does the MC/DC checker tool do?
-------------------------------------

The MC/DC checker does not actually assess test coverage itself. On a higher level, the tool is supposed be invoked before running coverage tools:

#. Run MC/DC checker.
#. Make code changes based on solutions recommended by the MC/DC checker.
#. Run coverage tool to get MC/DC coverage.


Here is an overview of what the MC/DC checker does:

#. Runs ``clang`` to preprocess the file - generates AST (Abstract Syntax Tree).
#. Walks the AST to find decisions.
#. Builds Binary Decision Diagram (BDD) from decisions.
#. Checks to see if BDD is tree-like. For tree-like BDD, it is proven Object Branch Coverage (OBC) implies MC/DC.
#. If BDD is non-tree-like, attempts to reorder the BDD.
#. After checking all files, it outputs to console a summary of all non-BDD decisions in files
#. Generates BDD .dot files showing before and after reordering.


Patch for the MC/DC checker
---------------------------

Currently the MC/DC checker works for simple C programs but crashes when run against code in several ROS packages. A `patch <https://gist.githubusercontent.com/iche033/ef0d23c85f4d810fa26c722027894c91/raw/f0a9f48abffff0101b9e7f2a2fd94f79cafe360e/mcdc_checker.patch>`_ with minor code changes to checker code is needed to avoid the crash. See instructions below for applying the patch to the checker.


How to run the MC/DC checker
-----------------------------

The recommended way of running the check is to use the upstream docker image as it requires a patched version of ``libclang``.


Pull their docker image

.. code-block:: bash

   docker pull registry.gitlab.com/gtd-gmbh/mcdc-checker/mcdc-checker

Launch the docker container and mount your source code that you would like to run MC/DC check on.

.. code-block:: bash

   cd <some_library>
   # override entrypoint and run bash:
   docker run -it -v $(pwd):/code --entrypoint /bin/bash registry.gitlab.com/gtd-gmbh/mcdc-checker/mcdc-checker


In a separate terminal, download the original ``mcdc_checker.py`` script

.. code-block:: bash

   wget https://gitlab.com/gtd-gmbh/mcdc-checker/mcdc-checker/-/raw/master/src/mcdc_checker.py

Download and apply the patch

.. code-block:: bash

   wget https://gist.githubusercontent.com/iche033/ef0d23c85f4d810fa26c722027894c91/raw/f0a9f48abffff0101b9e7f2a2fd94f79cafe360e/mcdc_checker.patch

   patch mcdc_checker.py < mcdc_checker.patch

Copy the modified ``mcdc_checker.py`` script into the container

.. code-block:: bash

   docker cp mcdc_checker.py `docker ps | grep mcdc-checker | awk '{print $1;}'`:/app/src/mcdc_checker.py


Go back to the docker container and run the ``mcdc_checker`` cmd

.. code-block:: bash

   mcdc_checker -a

The ``-a`` flag tells the check to run recursively on all files in this directory. You should see the script start processing the source code and generating output. See notes below for more info about possible error messages.


Notes about the MC/DC checker
-----------------------------

* When running the ``mcdc_checker`` script, you may encounter error messages about include files not found.

  The checker invokes ``clang`` to preprocess the input files. If include files are not found, it will not be able to do ``#include`` file expansion. Underneath the hood, the checker runs:

  .. code-block:: bash

     clang input_file.c -E > output

  where ``-E`` means run preprocesor only.

  You can pass include directories as arguments to the mcdc_checker tool, e.g

  .. code-block:: bash

     mcdc_checker -a -I/usr/include -I<some_other_dir>

* Some non-tree BDDs do not have solutions

  Looking at the console output produced by the ``mcdc_checker`` tool, the ``bdd_is_not_tree_like occurred in:`` section lists issues with non-tree BDDs. However, it is possible that not all of them contain solutions.


How to get coverage data for a specific ROS package
---------------------------------------------------

Here are instructions for generating coverage data with the ``gcov`` and ``lcov`` coverage tools, using the ``rcpputils`` pacakge as an example.

In your colcon workspace, build the package:

.. code-block:: bash

   colcon build --packages-select rcpputils --cmake-args -DCMAKE_BUILD_TYPE=Debug -DCMAKE_CXX_FLAGS="--coverage"

Run the tests

.. code-block:: bash

   colcon test --event-handlers console_direct+ --packages-select rcpputils


You should now see gcov ``.gcna`` and ``.dcno`` data files generated in the ``build`` directory

Use ``lcov`` to capture coverage data

.. code-block:: bash

   lcov --capture --directory build/rcpputils/ --output-file rcpputils_report.info

Generate html report

.. code-block:: bash

   genhtml rcpputils_report.info --output-directory rcpputils_coverage_report

Open ``rcpputils_coverage_report/index.html`` to see coverage data for the ``rcpputils`` pacakge
