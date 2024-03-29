Ogma
====

NASA's `Ogma <https://github.com/nasa/ogma>`_ is an open source tool to generate runtime monitoring applications.

The purpose of Runtime Verification is to monitor a system at runtime and detect anomalies or violations of expected properties. RV concerns itself with detecting the violations; it is the user's responsibility to determine how to address them.

Ogma facilitates the generation of correct monitors by translating high-level languages into self-contained monitoring applications.

Ogma supports a number of source languages, including `Copilot <https://github.com/copilot-language/copilot>`_, :doc:`FRET <FRET>` and Lustre. The applications can be generated for the Robot Operating System (Space ROS) and `NASA's Core Flight System (cFS) <https://github.com/nasa/cFS/>`_, among others.

Why use Ogma?
-------------

Runtime Verification is crucial especially for systems that are not amenable to full formal verification, or when testing all possible cases is not viable. It can also prove exceptionally useful to detect anomalies in the environment in which a system, such as robot or an aircraft, is operating.

A failure in a monitoring system could threaten the mission as a whole. The purpose of Ogma is to generate self-contained monitoring applications directly from high-level languages that are amenable to formal verification.

The core implementation of the monitors generated by Ogma is implemented in hard realtime C99 (produced via a tool called `Copilot <https://github.com/copilot-language/copilot>`_). Ogma wraps the monitors generated into a ROS 2 monitoring node that subscribes to the topics that provide the data needed by the monitors, re-evalutes the monitors when new data becomes available, and publishes any violations detected in separate ROS 2 topics.

By generating the complete monitoring node, we lower the risk that an error could take place in the monitoring system, and increase the reliability of the mission.

Additional References
---------------------

To learn more about Ogma, please see:

* `The Ogma repository <https://github.com/nasa/ogma>`_
* Paper: `Monitoring ROS 2: from Requirements to Autonomous Robots <https://arxiv.org/abs/2209.14030>`_
* Paper: `Integrating FRET with Copilot: Automated Translation of Natural Language Requirements to Runtime Monitors <https://ntrs.nasa.gov/citations/20220000049>`_
* Paper: `Automated Translation of Natural Language Requirements to Runtime Monitors <https://link.springer.com/chapter/10.1007/978-3-030-99524-9_21>`_
