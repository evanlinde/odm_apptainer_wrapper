ODM Apptainer Wrapper
=====================

A wrapper for running [OpenDroneMap's](https://opendronemap.org/) ODM
using [apptainer](https://apptainer.org/) images converted from the 
project's original Docker images.

The wrapper script `opendronemap` (in the `bin` directory) accepts
positional command line arguments and executes the apptainer image
defined in the environment variable `ODM_APPTAINER_IMAGE`.

The first two positional parameters are the datasets directory and
project directory. These two are required and must be given in order.
All additional parameters are optional and are passed to ODM itself. 
(Note that the `--project-path` and `--name` parameters for ODM do
not need to be specified. They are already set using the first two
parameters.)

For example, in a setup like this:

```
/big-data-volume/drone-runs/2020-02-02/images/{lots_of_image_files}
  ------------------------   -------- 
            |                   |
            |               project_name = "2020-02-02"
            |
      datasets_dir = "/big-data-volume/drone-runs"
```

the command to start ODM via the wrapper script would look like this:

```
opendronemap /big-data-volume/drone-runs 2020-02-02 {additional_odm_args}
```


## Motivation

The goal was to create simple [Lmod](https://lmod.readthedocs.io/en/latest/) 
modules for OpenDroneMap that are usable without having to deal with all the
complexity of apptainer (or docker).


## Converting the ODM docker image to an Apptainer image

General form:
```
apptainer pull {name}.sif docker://opendronemap/odm:{tag}
```

Using the tag `3.1.0` as an example:
```
apptainer pull odm_3.1.0.sif docker://opendronemap/odm:3.1.0
```


## Make an LMOD module for the Apptainer image

### Lua

```lua
local imgDir = "/opt/container_images/opendronemap"
local imgVersion = "3.1.0"
local wrapperRoot = "/opt/apps/odm_apptainer_wrapper"
setenv("ODM_APPTAINER_IMAGE", imgDir .. "/odm_" .. imgVersion .. ".sif")
prepend_path("PATH", wrapperRoot .. "/bin")
prepend_path("MANPATH", wrapperRoot .. "/man")
```

### TCL

```tcl
#%Module1.0###################################################
set 		imgDir 			/opt/container_images/opendronemap
set		imgVersion 		3.1.0
set 		wrapperRoot 		/opt/apps/odm_apptainer_wrapper
setenv 		ODM_APPTAINER_IMAGE 	${imgDir}/odm_${imgVersion}.sif
prepend-path 	PATH 			${wrapperRoot}/bin
prepend-path 	MANPATH 		${wrapperRoot}/man
```


## Dependencies

* bash
* apptainer
* ODM image(s) in apptainer format

