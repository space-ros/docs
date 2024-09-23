Building Space ROS from Source
==============================

There are multiple ways to acquire docker images within the Space ROS ecosystem.

As a prerequisite ensure that docker is `installed <https://docs.docker.com/desktop/>`_.

1. Local Dockerfiles
---------------------

Within the Space ROS organization there is repository full of `docker images <https://github.com/space-ros/docker>`_. There is also a repository containing the install of `Space ROS <https://github.com/space-ros/space-ros.git/>`_. To install either locally, clone the wanted repository.

For Example:

```
git clone https://github.com/space-ros/docker.git
```
or
```
git clone https://github.com/space-ros/space-ros.git
```

From there navigate to the wanted package, build, and run.

```
./build.sh && ./run.sh
```
