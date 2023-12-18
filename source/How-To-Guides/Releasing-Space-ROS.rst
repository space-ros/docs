Releasing Space ROS
===================

Before the release window
-------------------------

- Determine the release tag of the form rosdist-year.month.point, for example ``humble-2024.01.0``
- Open a new issue with a title of the form "year-month Release", for example  "2024-01 Release"
- Create a milestone that matches the release tag in each repo under the Space ROS organization
- Create a new view in the Space ROS Project filtered by milestone

During the release window
-------------------------

- Tag issues intended for the release with the correct milestone
- Update issue status on the project view for the release

At the end of the release window
--------------------------------

- Create release/distro branches on `docker`, `demos`, `simulation`, `docs`, and `process_sarif`.
- Update the .repos files to the new versions for the new distro sync.
- Re-run the tests, creating issues for any that failed.
- Docker images are already built but only tagged `latest`, must tag release as release name.
- Add announcements to Website, Docs, and Discourse
- Create next release milestone and tracking issue

After release window
--------------------

- Open issues based on release experience
- Document lessons learned
