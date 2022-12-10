FRET
====

`NASA Ames’ <https://www.nasa.gov/ames>`_ Formal Requirements Elicitation Tool (FRET) is an open source tool for writing, understanding, formalizing, and analyzing requirements.

.. image:: /images/fret_tool_dashboard.png

.. rst-class:: image-subtitle

    The FRET user interface

The purpose of FRET is to:

#. Make the writing, understanding, and debugging of formal requirements as natural and intuitive as possible,
#. Seamlessly connect to powerful external tools for analysis, and
#. Support the correction of requirements suggested by analysis results.

FRET supports a variety of forms for each requirement including natural language description, formal mathematical logic, diagrams, and interactive simulation.
Users can write requirements in an intuitive, restricted natural language, called "FRETish", with precise, unambiguous meaning.
For each FRETish requirement, FRET produces natural language and diagrammatic explanations of its exact meaning, formalizes the requirement in future-time and past-time temporal logic, and supports interactive simulation of produced logic formulas to ensure that they capture user intentions.
Once requirements are expressed in FRETish, FRET can then perform automatic model checking of requirements for consistency and conflicts.
FRET can also connect to external analysis tools by exporting requirements in a variety of formats for analysis tools such as `Cocosim <https://coco-team.github.io/cocosim/>`_, `Simulink Design Verifier <https://www.mathworks.com/products/simulink-design-verifier.html>`_, Kind, and SMV.

Why use FRET?
-------------

FRET and :doc:`Doorstop <Doorstop>` are two open source requirements tools that are used with Space ROS.
The intention is to integrate and leverage the strengths of each tool.
Doorstop, along with git, is used to ensure requirements tracking and traceability, while FRET is used for requirements analysis and consistency checking.
To facilitate interoperability, FRET is able to import and export the Markdown-based requirements file format used by Doorstop.
Requirements can be stored in a git repository and operated on by either FRET or Doorstop.

To learn more about how FRET is used in Space ROS, please refer to this tutorial on the `Requirements Tools and Processes <../Tutorials/Requirements-Tools-And-Processes>`_ used in Space ROS.

Additional References
---------------------

To learn more about FRET, the following resources are available:

* The NASA site, `FRET: Format Requirements Elicitation Tool <https://software.nasa.gov/software/ARC-18066-1>`_
* A paper, `Formal Requirements Elicitation with FRET <https://ntrs.nasa.gov/api/citations/20200001989/downloads/20200001989.pdf>`_
* `The FRET github repository <https://github.com/NASA-SW-VnV/fret>`_
* `A video presentation on FRET <https://tinyurl.com/fretForREFSQ>`_
* `NASA's press release for FRET 2.0 <https://www.nasa.gov/collection-asset/fret-team-releases-public-version-20-of-formal-requirements-engineering-software>`_
