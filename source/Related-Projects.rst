Related Projects
================

Space ROS makes use of several external projects that aren't part of standard ROS 2 distributions.
The first pair of tools, IKOS and Cobra, are static analysis tools that have been integrated into Space ROS using the same Python wrapper method used for other code analysis tools integrated into standard ROS 2 distributions, such as clang-tidy and cpplint.
The full set of code analysis tools are run automatically when building and running package tests ('colcon test'). 
The next set of tools, Doorstop and FRET, are requirements tools that are part of an :doc:`overall requirements methodology <Tutorials/Requirements-Tools-And-Processes>` used for Space ROS.

.. toctree::
   :titlesonly:
   :maxdepth: 2

   Related-Projects/IKOS.rst
   Related-Projects/Cobra.rst
   Related-Projects/Doorstop.rst
   Related-Projects/FRET.rst
