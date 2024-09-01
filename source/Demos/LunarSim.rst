LunarSim Demo
==============

Space ROS Lunar Sim Demo Docker Image
-------------------------------------

The Space ROS Lunar Sim Demo docker image uses the spaceros docker image (*osrf/space-ros:latest*) as its base image.
The Dockerfile installs all of the prerequisite system dependencies along with the demo source code, then builds the Space ROS Lunar Sim Demo.

This demo includes a Gazebo simulation of the lunar environment (specfically around the Shackleton crater near the south pole). It uses
Digital Elevation Models (DEMs) from the Lunar Orbiter Laser Altimeter (LOLA) to accurately simulate the lunar surface in a specific region. It also contains a dynamic model of the Sun that moves according to Ephemeris data.

Building the Demo Docker
^^^^^^^^^^^^^^^^^^^^^^^^

The demo image builds on top of the spaceros image.
To build the docker image, first ensure the spaceros base image is available either by `building it locally <https://github.com/space-ros/space-ros>`_ or pulling it.

Then build lunar_sim demo images:

.. code-block:: bash

  cd lunarsim_demo
  ./build.sh


Running the Demo Docker
^^^^^^^^^^^^^^^^^^^^^^^

Run the following to allow GUI passthrough:

.. code-block:: bash

  xhost +local:docker


Then run:

.. code-block:: bash

  ./run.sh

Depending on the host computer, you might need to remove the ``--gpus all`` flag in ``run.sh``, which uses your GPUs.


Running the Demo
^^^^^^^^^^^^^^^^

Once the container is running, you can launch the demo by running:

.. code-block:: bash

  souce install/setup.bash
  ros2 launch lunar_sim_gz_worlds lunar_sim.launch

This will launch the gazebo lunar world, spawn the rover and start teleop in a new terminal window which enables you to control the rover as per the instructions in the terminal window.


Changing the Digital Elevation Model (DEM)
------------------------------------------

The variation in elevation of the Lunar surface within the simulation
environment is generated using a `Digital Elevation Model
(DEM) <https://en.wikipedia.org/wiki/Digital_elevation_model>`_, also
called a Heightmap, which is a 2D representation of the elevation of a
terrain. These are usually available in PNG or TIF format, where the
value of each pixel represents the elevation.

In this tutorial we will use the Shoemaker_final_adj_5mpp_surf.tif from
`High-Resolution LOLA Topography for Lunar South Pole
Sites <https://pgda.gsfc.nasa.gov/products/78>`_.

Gazebo using ogre2 as the render engine requires that height maps have a
size of 2^n x 2^n. In our case the Shoemaker_final_adj_5mpp_surf.tif
image is 4000x4000 which doesn’t meet this requirement, so we can either
downscale it using a library called gdal in order to load the whole
terrain, or crop it using the tool crop_dem.py script to use a part of
the terrain and preserve the resolution. Reducing the size of the DEM
will also make it quicker to render by Gazebo.

Downscaling
^^^^^^^^^^^
gdal can be installed using the commands below:

.. code:: bash

   sudo add-apt-repository ppa:ubuntugis/ppa
   sudo apt-get update
   sudo apt-get install gdal-bin
   sudo apt-get install libgdal-dev

We can then use the gdalwarp tool to downsample to 2048x2048, which
meets the 2^n x 2^n requirement:

.. code:: bash

  gdalwarp -ts 2048 2048 Shoemaker_final_adj_5mpp_surf.tif Shoemaker_final_adj_5mpp_surf_2048.tif

Cropping
^^^^^^^^

The crop_dem.py script, located in lunarsim_gz_worlds/scrikpts, can be
used to crop a part of the DEM out:

.. code:: bash

  python3 crop_dem.py Shoemaker_final_adj_5mpp_surf.tif Shoemaker_final_adj_5mpp_surf_1k.tif 1872 1872 256

Creating Normal Map
^^^^^^^^^^^^^^^^^^^

A normal map is a 2D texture that encodes information about the surface
orientation (normals) of the terrain, which influences how light
interacts with the surface. We can derive this texture by calculating
the gradients within our DEM. This can be done using the
surface_normals.py script in lunarsim_gz_worlds/scripts:

.. code:: bash

  python3 surface_normals.py

The image created will look something like this:

.. image:: /images/lunar_normal_map.png

.. rst-class:: image-subtitle

    Normal map generated from the DEM

Blending Normals
^^^^^^^^^^^^^^^^
The normal map we generated can be blended with a higher resolution texture
in order to create a more accurate representation of what the surface
would look like at a smaller scale. Sample higher resolution textures
can be downloaded at `Polyhaven <https://polyhaven.com/>`_. For
instance, the normal map from `this sand
texture <https://polyhaven.com/a/sandy_gravel>`_ can be blended with
DEM’s normal map. This can be done using the blend_normals.py script
located in lunarsim_gz_worlds/scripts:

.. code:: bash

  python3 blend_normals.py Shoemaker_final_adj_5mpp_surf_2048_normals.png sandy_gravel_nor_gl_4k.exr Shoemaker_sandy_norms.png

By blending the normal map from the DEM with the sandy texture normal
map, we get the following blended map:

.. image:: /images/lunar_textured_normals.png

.. rst-class:: image-subtitle

    Surface Normals of the terrain in LunarSim with blended sand texture normals

Using DEM in Gazebo
^^^^^^^^^^^^^^^^^^^
We also need to know the distance that the DEM covers in the x, y and z directions, which we
can do with gdalinfo (requires installation of gdal, instructions in the
Downscaling section):

.. code:: bash

  gdalinfo -stats Shoemaker_final_adj_5mpp_surf.tif

The output will contain some useful metadata on the DEM:

.. code:: bash

   Data axis to CRS axis mapping: 1,2
   Origin = (66000.000000000000000,49000.000000000000000)
   Pixel Size = (5.000000000000000,-5.000000000000000)
   Metadata:
     AREA_OR_POINT=Area
     NC_GLOBAL#Conventions=COARDS, CF-1.5
     NC_GLOBAL#GMT_version=5.4.5 [64-bit]
     NC_GLOBAL#history=xyz2grd -bi3 -I5 -R6.60000000000e+04/8.60000000000e+04/2.90000000000e+04/4.90000000000e+04 -Gtest.grd -r
     NC_GLOBAL#node_offset=1
     x#actual_range={66000,86000}
     x#long_name=x
     y#actual_range={29000,49000}
     y#long_name=y
     z#actual_range={-1075.152099609375,713.5578002929688}
     z#long_name=z
     z#_FillValue=nan

From this we can ascertain that the resolution is 5m per pixel (which
was also mentioned in the source), as well as get the distances covered
in the x, y and z directions by subtracting the start of the range from
the end. This gives us 20,000 metres in the x (86000 - 66000), 20,000
metres in the y (49000-29000) and 1789 metres (713.6 - (-1075.2)) in the
z.

The DEM can then be utilised within a Gazebo SDF as show below:

.. code:: xml

  <link name="link">
    <collision name="collision">
      <geometry>
        <heightmap>
          <uri>dem/Shoemaker_final_adj_5mpp_surf_2048.tif</uri>
          <size>20000 20000 1789</size>
          <pos>0 0 0</pos>
        </heightmap>
      </geometry>
    </collision>
    <visual name="visual">
      <geometry>
        <heightmap>
          <use_terrain_paging>false</use_terrain_paging>
          <texture>
            <diffuse>materials/textures/TerrainNew_Albedo.jpg</diffuse>
            <normal>materials/textures/Shoemaker_final_adj_5mpp_surf_2048_normals.png</normal>
            <size>20000 20000 1789</size>
          </texture>
          <uri>dem/Shoemaker_final_adj_5mpp_surf_2048.tif</uri>
          <size>20000 20000 1789</size>
          <pos>0 0 0</pos>
        </heightmap>
      </geometry>
    </visual>
  </link>

Let’s walk through the xml.

.. code:: xml

  <collision name="collision">
    <geometry>

This defines that we are creating a collision geometry within the
simulation, which is what allows for interactions between objects and
the surface.

.. code:: xml

  <heightmap>
    <uri>dem/Shoemaker_final_adj_5mpp_surf_2048.tif</uri>
    <size>20000 20000 1789</size>
    <pos>0 0 0</pos>
  </heightmap>

Here we define our heightmap to be used to create the collision
geometry. The ``uri`` tag specifies where the heightmap file can be
found. The ``size`` tag specifies the dimensions of the Heightmap in
meters along the x, y and z axis. If you want this to scale properly to
the real environment, enter the size as the size of the real area that
the DEM covers, which we derived using gdalinfo. Finally the ``pos``
tag defines the origin of the heightmap.

.. warning::
  The Heightmap must be a square, or it will cause issues
  where the collision and visual geometries are not aligned. The size
  also needs to be a power of 2.

.. code:: xml

  <visual name="visual">
    <geometry>

Now we’re creating a visual geometry so the rover (and humanoid user)
can see the lunar surface.

.. code:: xml

  <heightmap>
    <use_terrain_paging>false</use_terrain_paging>
    <texture>
      <diffuse>materials/textures/TerrainNew_Albedo.jpg</diffuse>
      <normal>materials/textures/Shoemaker_final_adj_5mpp_surf_2048_normals.png</normal>
      <size>20000 20000 1789</size>
    </texture>
    <uri>dem/Shoemaker_final_adj_5mpp_surf_2048.tif</uri>
    <size>20000 20000 1789</size>
    <pos>0 0 0</pos>
  </heightmap>

Here again we define a Heightmap so the visual geometry matches the
collision surface. ``uri``, ``size`` and ``pos`` tags should
remain the same. We now also have a ``texture`` tag where we supply an
Albedo map to define what the base color of the surface is , and the
Normal map that we previously generated to define how light interacts
with it. The size of the texture should be the same as the Heightmap
size.

Heightmaps collected from Lunar Reconnaissance Orbiter which surveys the
Lunar South Pole can be found here:
https://astrogeology.usgs.gov/search/map/moon_lro_lola_dem_118m

https://pgda.gsfc.nasa.gov/products/78

https://svs.gsfc.nasa.gov/cgi-bin/details.cgi?aid=4720

Note that while using heightmaps, the default collision detector ode `doesn’t seem to work very
well <https://github.com/gazebosim/gz-sim/issues/1714>`_. So we need to
make sure we are using the bullet detector instead. We can do this by
adding a tag within the sdf file that we’re loading the model into:

.. code:: xml

  <physics name="1ms" type="ignored">
    <dart>
      <!-- Heightmaps behave better with the bullet collision detector -->
      <collision_detector>bullet</collision_detector>
    </dart>
  </physics>

Dynamic Sun Simulation Using Ephemeris Data
-------------------------------------------
Our simulation environment contains a dynamic model of the Sun that moves according to Ephemeris data retrieved using the NASA Horizons tool.
The model is loaded into the world using a Gazebo plugin, which allows for dynamic changes to the models position as well as the attached light source. We use a `pre-existing model <https://app.gazebosim.org/OpenRobotics/fuel/models/Sun>`_ of the Sun to define it's visual appearance. Latitude and longitude of the center of the DEM can be retrieved using this gdalinfo command:

.. code:: bash

  gdalinfo -stats Shoemaker_final_adj_5mpp_surf.tif

The output should give us the coordinates of the center of the DEM:

::

  Corner Coordinates:
    Upper Left  (   66000.000,   49000.000) ( 53d24'32.08"E, 87d17'22.89"S)
    Lower Left  (   66000.000,   29000.000) ( 66d16'46.58"E, 87d37'22.65"S)
    Upper Right (   86000.000,   49000.000) ( 60d19'36.90"E, 86d44'12.26"S)
    Lower Right (   86000.000,   29000.000) ( 71d21'55.49"E, 87d 0'27.63"S)
    Center      (   76000.000,   39000.000) ( 62d50' 6.05"E, 87d11' 0.65"S)


This information can be used to retrieve the Apparent Azimuth and Elevation of the Sun in the Lunar Sky. `NASA's Horizons System <https://ssd.jpl.nasa.gov/horizons/app.html#>`_ provides an easy to use interface to generate Ephemeris data for a wide range of solar system bodies. In order to get the required data for the plugin, enter the Longitude and Latitude data into the application with the target body as the Sun and Observer location as the Moon:

.. image:: /images/nasa_horizons.png

Then enter in the settings to specify the timesteps as well as selecting the type of data we want (in this case just Azimuth and Elevation):

.. image:: /images/nasa_horizons_settings.png

This should output a table with the required data:

.. image:: /images/nasa_horizons_output.png

This can then be added to a csv file and loaded into the plugin.

In Gazebo, the Sun is represented as an actor, an animated model that follows a predefined trajectory. This trajectory is loaded through a plugin, and both the visual representation of the Sun and its associated light source in the world are updated accordingly.


