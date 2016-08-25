Ipython can be used to look at basic code documentation which may be helpful when exploring the model functionality. Portions of the code can be run in ipython and then those functions are available in help. For example, running the first portion of gen_thwaites_mesh.py in ipython:

```
> ipython
Python 2.7.9 (default, Apr  2 2015, 15:33:21) 
Type "copyright", "credits" or "license" for more information.

IPython 2.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: from cslvr             import *

Info : Increasing process stack size (8192 kB < 16 MB)

In [2]: from pylab             import *

In [3]: from scipy.interpolate import RectBivariateSpline

In [4]: 

In [4]: #===============================================================================

In [5]: # data preparation :

In [6]: out_dir   = 'dump/meshes/'

In [7]: mesh_name = 'thwaites_3D_U_mesh_basin'

In [8]: #mesh_name = 'thwaites_3D_U_mesh_block_crude'

In [9]: 

In [9]: # get the data :

In [10]: measure = DataFactory.get_ant_measures(res=900)
::: getting Antarctica measures data from DataFactory :::
      Measures : vx                             key : "vx" 
      Measures : vy                             key : "vy" 
      Measures : v_err                          key : "v_err" 
      Measures : mask                           key : "mask" 

In [11]: bedmap2 = DataFactory.get_bedmap2()
::: getting Bedmap 2 data from DataFactory :::
      Bedmap 2 : B                              key : "B" 
      Bedmap 2 : S                              key : "S" 
      Bedmap 2 : H                              key : "H" 
      Bedmap 2 : mask                           key : "mask" 
      Bedmap 2 : lat_mask                       key : "lat_mask" 
      Bedmap 2 : rock_mask                      key : "rock_mask" 
      Bedmap 2 : b_uncert                       key : "b_uncert" 
      Bedmap 2 : coverage                       key : "coverage" 
      Bedmap 2 : gl04c_WGS84                    key : "gl04c_WGS84" 


```
Produces the ability in ipython to call help on those functions:
```
In [12]: help(DataFactory)
```
This returns:
```
Help on class DataFactory in module cslvr.datafactory:

class DataFactory(__builtin__.object)
 |  Static methods defined here:
 |  
 |  get_ant_measures(res=900)
 |  
 |  get_bamber(thklim=0.0)
 |  
 |  get_bedmap1(thklim=0.0)
 |  
 |  get_bedmap2(thklim=0.0)
 |  
 |  get_gre_measures()
 |  
 |  get_gre_qgeo_fox_maule()
 |  
 |  get_qgeo_schroeder(thklim=0.0)
 |      Sets NANs to 1e6
 |  
 |  get_rignot()
 |  
 |  get_searise(thklim=0.0)
 |  
 |  print_dim(rg)
 |  
 |  ----------------------------------------------------------------------
 |  Data descriptors defined here:
 |  
 |  __dict__
 |      dictionary for instance variables (if defined)
 |  
 |  __weakref__
 |      list of weak references to the object (if defined)
 |  
 |  ----------------------------------------------------------------------
 |  Data and other attributes defined here:
 |  
 |  color = '229'
 |  
 |  filename = '/home/gailm/Software/myfork_cslvr/cslvr/datafactory.py'
```
Entering ```help(``` and hitting tab will show all the functions available in help:
```
In [13]: help(DataFactory.
DataFactory.color                   DataFactory.get_bedmap1             DataFactory.get_qgeo_schroeder      DataFactory.print_dim
DataFactory.filename                DataFactory.get_bedmap2             DataFactory.get_rignot              
DataFactory.get_ant_measures        DataFactory.get_gre_measures        DataFactory.get_searise             
DataFactory.get_bamber              DataFactory.get_gre_qgeo_fox_maule  DataFactory.mro 
```

<!--- ##Imshow() --->