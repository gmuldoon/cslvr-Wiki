##Overview 

This is a layman's description of how the model inverts for basal traction. See Evan Cumming's MS Thesis at the University of Montana for complete details.

1. Initial values of θ (energy) and β (basal traction) are estimated from the Shallow Ice Approximation and surface temperature (which gives a rate factor).

2. θ and β are thermomechanically coupled to produce 3D velocity (u), pressure, updated θ and T, and water content

3. Invert for basal water flux, Fb, from θ

   _Steps 2 and 3 are iterated until ||θ_n-1 - θn|| is small. Usually ~ 3 iterations_

4. Invert for β

   _This iterative process is run for ~ 10,000 iterations to be sure it converges._

5. Repeat steps 2-3 ~10 times for convergence.

##Notes
The Blatter-Pattyn method is used to invert for β because it is lower computational cost than Full Stokes. Full Stokes is used for the thermomechanical coupling because it solves for pressure and therefore provides a more accurate energy solution.
