#Sphinx Documentation
Much of the code has been documented using [Sphinx](http://www.sphinx-doc.org/en/stable/) which can be used to generate documentation by the user. Here I provide an introduction to the four basic run scripts and how they can be used to change parameters in the model. This is by no means an exhaustive guide.

# Workflow
There are four scripts used to run cslvr. These scripts must be customized for the domain and data of interest to you.

These are found in cslvr/simulations/[Location]/data_assimilation/[AreaOfInterest] where Location is Greenland, Antarctica, or ISMIP test cases. AreaOfInterest specifies regional or continental-scale. Meshes for new areas of Interest can be added by the user.

The most important scripts are:

1. **gen_mesh.py** -- This script puts the data into finite elements for a particular projection. This script is serial.

2. **gen_vars.py** -- This script takes the "brick" mesh and puts data on the mesh to turn it into a realistic ice sheet. This script runs in parallel.

3. **gen_submeshes.py** -- This script makes a few useful submeshes to expedite later calculations. This script is serial.

4. **data_assimilation.py** -- This script evaluates the physics of the model and inverts for basal traction.

See the documentation on the individual scripts to learn more about how they can be edited to suit your use case.
