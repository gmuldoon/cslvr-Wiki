# Overview
This script is where the model physics are evaluated and the inversion performed. It can be run in parallel.

##  Solver method
[IPOpt](http://www.coin-or.org/Ipopt/documentation/) is the preferred method for solving the nonlinear optimization in data_assimilation.py. However, not everyone has it available. If you are missing IPOpt, change 'method' in ```wop_kwargs``` and ```uop_kwargs``` to the [Limited-memory BFGS algorithm] (https://en.wikipedia.org/wiki/Limited-memory_BFGS) like so:

```
wop_kwargs = {'max_iter'            : 350,
              'bounds'              : (0.0, 100.0),
              'method'              : 'l_bfgs_b',
              'adj_save_vars'       : w_opt_save_vars,
              'adj_callback'        : None}

uop_kwargs = {'control'             : d3model.beta,
              'bounds'              : (1e-5, 1e7),
              'method'              : 'l_bfgs_b',
              'max_iter'            : 1000,
              'adj_save_vars'       : u_opt_save_vars,
              'adj_callback'        : None,
              'post_adj_callback'   : adj_post_cb_ftn}
```
## Restart
There are a few instances in this script where restart functionality has been hard coded, but commented out:
```
 #frstrt = HDF5File(mpi_comm_world(), out_dir + '01/inverted.h5', 'r') 
 #d3model.init_T(frstrt)                                  
 #d3model.init_W(frstrt)                                           
 #d3model.init_Fb(frstrt)                                      
 #d3model.init_alpha(frstrt)                                         
 #d3model.init_U(frstrt)                                           
 #d3model.init_p(frstrt)                                            
 #d3model.init_theta(frstrt)  
```

These restarts can be used to continue the script from its last checkpoint in order to save time during testing or to recover from a truncated run. As the script runs, it creates checkpoint directories in out_dir, e.g. '01' in this example. If out_dir + '01/inverted.h5' exists, it is possible to restart from that point by uncommenting this restart code.

## Assimilating for Enhancement factor, _E_
### Explanation
It is possible (though untested) that alternately inverting for the basal traction, β, under grounded ice and the ice flow enhancement factor, _E_, over ice shelves, will improve the inversion. This can be done through manual iteration of the code described below.

Glen's flow law states that:

<p align="center">ε' = A τ^n</p>

where ε is strain rate, τ is shear stress, _n_ is the creep exponent (usually assumed to equal 3) and _A_ is the creep constant. _A_ has the form of the Arrhenius equation and depends on ice temperature and fabric:

<p align="center"> A = A0 * exp[Q/(RT)]</p>

where _A0_ is a prefactor, _T_ is temperature, _Q_ is activation energy for creep, and R is the universal gas constant. In this case, we had an additional term called _E_ for fitting the inversion:

<p align="center"> A = E * A0 * exp[Q/(RT)]</p>

_E_ is known as an enhancement factor.


### Method
Because there is no basal traction, _β_, over the ice shelves, the inversion for _β_ cannot be performed there. However, an inversion for _E_ can be performed over the ice shelves. It's therefore possible to improve the overall inversion by combining the two in tandem:

1. Optimize J(u,β) so that _β_ is smooth and surface velocity, _u_, matches observations. 

2. Optimize _E_ in the same way and calculate corresponding _β_ at the grounding line.

3. Repeat steps 1 and 2 until _E_ and _β_ are not changing. 

This can be achieved manually in the following way:

1. Run data_assimilation.py as is.

2. Create a separate version of data_assimilation.py (perhaps called data_assimilation_E.py).


   _**In data_assimilation_E.py:**_

3. Add _E_ to the list of variables to save after adjoining:
   ```
   adj_save_vars = [d3model.T,
                     d3model.W,
                     d3model.Fb,
                     d3model.Mb,
                     d3model.alpha,
                     d3model.alpha_int,
                     d3model.PE,
                     d3model.Wbar,
                     d3model.Qbar,
                     d3model.temp_rat,
                     d3model.U3,
                     d3model.p,
                     d3model.beta,
                     d3model.theta,
                     d3model.E]
   ```

4. Edit ``` u_opt_save_vars``` to take E:
   ``` u_opt_save_vars = [d3model.E, d3model.U3]```

5. Either comment out the regularization functional entirely or edit it to regularize _E_:
   ```
   # form the regularization functional : 
   mom.form_reg_ftn(d3model.E, integral=d3model.OMEGA_FLT,
                    kind='TV_Tik_hybrid', alpha_tik=1e-1, alpha_tv=10.0)
   ```

6.  Under ``` uop_kwargs ```, change the `control` to d3model.E. Also change the `bounds` on E. It's not clear what the bounds should be, but something not too big.
   ```
   uop_kwargs = {'control'             : d3model.E,
                  'bounds'              : (1e-5, 500),
                  'method'              : 'l_bfgs_b',
                  'max_iter'            : 1000,
                  'adj_save_vars'       : u_opt_save_vars,
                  'adj_callback'        : None,
                  'post_adj_callback'   : adj_post_cb_ftn}
    ``` 

## Balance velocity smoothing
The balance velocity function includes an input parameter, κ, which is the surface smoothing raduys in units of ice thickness. A typical value is κ = 0.5, however this is a tunable parameter. It can be edited in the following line:

``` bv = BalanceVelocity(bedmodel, kappa=5.0) ```

Additional information about this function can be found in cslvr/balancevelocity.py.