# Overview
This script creates the finite element mesh for the model. It runs in serial and outputs a file to out_dir/mesh_name, as specified at the top of the script. By default, out_dir is dump/meshes/. Output files are in .geo, .msh, and .xml.gx format. Gmsh (and other) programs can be used to look at finished mesh. 

This script uses the DataFactory class to get the data. New data can be added to cslvr/cslvr/data_factory.py to be used in a new mesh. This script is also where the domain of the model is set.


# Adding different data to DataFactory
Basal topography and surface velocity data is grabbed by this script to generate them mesh. To swap out the basal topography, for example, to something new, it's necessary to create a new entry in cslvr/cslvr/data_factory.py. It is best to use other entries in data_factory.py as a guide on how to do this. The new entry must include appropriate information for the new data's map projection, for example. See get_bedmap2() in  data_factory.py for an example entry.

#Setting the size of the mesh
The dbm.rescale_field() function is used to determine the size of the mesh. It takes inputs umin and umax, which are sizes in meters indicating the smallest and largest boxes the mesh will create. The mesh resulting from running this script can be examined to check the appropriateness of these values for your problem.

The dbm.data['ref'][slp] is a limit in meters on the refinement of slopes. It can be used to limit the "pointiness" of mountains to make them more or less realistic.

the dbm.data['ref'][shf] function controls the size of the mesh over floating ice.

#Defining the domain
This can be done in two ways in the existing code, at least in Antarctica.
1. Use a pre-existing NASA catchment
2. Draw a box around an area of interest.

The code is available for either method. Be sure to comment out the method not in use.

##Basin domain
The NASA-defined catchments are available [online here](http://icesat4.gsfc.nasa.gov/cryo_data/ant_grn_drainage_systems.php). They are called by number in the GetBasin() function in the gen_mesh.py script.

## Box domain
Alternatively, a new contour can be drawn using coordinates of the corner of a box. This is available in the script as
```bash
new_cont = array([[x1, y1],
                  [x2, y1],
                  [x2, y2],
                  [x1, y2],
                  [x1, y1]])
m.intersection(new_cont)   
```