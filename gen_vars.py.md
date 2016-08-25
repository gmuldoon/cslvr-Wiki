## Overview

This script puts data on the previously-generated mesh, in effect changing it from a brick into a relatic ice sheet. The data includes surface and bed geometries, ice temperature and velocity, subglacial geothermal flux, surface accumulation, etc. This is the place to change the input data fields if desired. 

This script can be run in parallel. It may have other names as well, such as gen_thwaites_vars_basin.py.

##Change input data
As described in the documentation for generating a mesh, new data sources can be introduced by the user. The data files can be placed in the data/[Location] directory in the cslvr home directory. A new function must then be added to cslvr/data_factory.py with a description of the data. Use other functions in data_factory.py as a guide.

Depending on what you all the new data, several parts of this script may need to be edited to use the new data. Below is an example to include a new geothermal flux field. This editing comes after adding a new entry in DataFactory called ```get_qgeo_schroeder```.

1. Call the DataFactory function:

   ``` q_geo_schroed = DataFactory.get_qgeo_schroeder(thklim = 0.0) ```

2. Use that to call DataInput:

   ``` sch= DataInput(q_geo_schroed,  mesh=mesh) ```

3. Use get_expression to extract the data values:

   ``` q_geo = sch.get_expression("q_geo_schroed", near=False)```
   
4. Initialize the values:

   ``` model.init_q_geo(q_geo) ```
   
5. Add it to the list of variables to save:

   ``` 
   lst = [model.S,
          model.B,
          model.mask,
          model.q_geo,
          model.T_surface,
          model.adot,
          model.u_ob,
          model.v_ob,
          model.U_mask,
          model.lat_mask]
   ```

Note: It's easiest to not change the name of the field in the list. If the name is changed, it needs to be updated during initialization in data_assimilation.py and potentially gen_submeshes.py depending on which field was changed. 

## Stitch together data
geothermal or for scaling a param: bedmap1['new_gf'] = gfhsr*n