# CLASS_EDE: CLASS for Early Dark Energy

![](https://github.com/mwt5345/class_ede/blob/master/fEDE_v_z.png) <!-- .element height="10%" width="10%" -->

## Class edited by
- J. Colin Hill; jch2200 at columbia.edu
- Evan McDonough; evan_mcdonough at brown.edu
- Michael W. Toomey; michael_toomey at brown.edu

## Files 

All CLASS files in the directory CLASS, the relevant file for the sampler Cobaya is in the  directory Cobaya.

## Examples 

Python notebooks with worked out examples can be found [here](https://github.com/mwt5345/class_ede/tree/master/notebooks-ede).


The below examples use the best-fit params from Table I of from Smith et al. arXiv:1908.06995

(1) explanatory-EDE.ini

To run: ./class explanatory-EDE.ini . To plot the output: run the Matlab script "plot-EDE.m", in "output" subdirectory.

Or

(1.2) EDE-example.py

To run: python EDE-example.py.


(1) P_k_max_h/Mpc too small. 

CLASS run in c, i.e. with the command ./class xxxx.ini , throws a warning if you try to compute the non-linear P(k) at high-z, requiring too high k values (as set by P_k_max). The python wrapper flags this warning as a CosmoSevereError and will kill the evaluation. This will crash an MCMC sampler like Cobaya; the DES likelihood sets P_k_max=15 (overwriting the user-input value), which is too low for some corners of parameter space in certain models. This problem has been resolved by CosmoSevereError-->CosmoComputationError in classy.pyx, and with an exception added to Cobaya's classy.py . 


## Modifications to CLASS 

Modifications are explained below. All edits are flagged in the code by "EDE-edit".


### Explanations 

(1) Scalar field parameters: The syntax for entering scalar field parameters has been changed to the following: the user must input one of {m_scf,log10m_scf,log10z_c} and one of {f_scf,log10f_scf,fEDE}, as well the power law n_scf, the initial field displacement thetai (=\phi_i /f), and an additive constant CC_scf. The units of f_scf and m_scf are eV. Note that fEDE may only be input if log10z_c is also input. 

(2) Background dynamics:

The scalar field is implemented in background.c in a seemingly trivial way, with the key detail that V(\phi) includes an additive constant. This is used as the "tuning parameter" in all CLASS runs, and the explicit Omega_Lambda is set to 0.

(3) To run as LCDM: Works the same as normal CLASS. Note the CC is now handled by omega_lambda!

(4) Perturbations: adiabatic initial conditions for the scalar field are implemented in perturbations.c . 

(5) fEDE and zc: we have implemented the calculation of f_EDE and z_c into the background module and python wrapper. We have also implemented a the built-in shooting algorithm for fEDE and log10z_c, in input.c, allowing the user to specify {fEDE,log10z_c,thetai} and CLASS will find the corresponding {f_scf,m_scf,thetai}.  Note that extremely small values of fEDE require increased precision in precisions.h ; for fEDE=1e-3 a timestep precision 1.e-3 was sufficient for all test cases.

(6) Exit codes: Large values of f_{EDE} (>.9) will crash CLASS (often the thermodynamics module). Such large values are not physical. To avoid crashing an MCMC run, an error code has been added to background.c, "fEDE = %e instead of < 0.5"

(7) Defined a function fsigma8= f(z)*sigma8(z) for use with RSD likelihoods.


### Modifications to classy.py in COBAYA 


(1) Error handling: Cobaya can bypass a CLASS "CosmoComputationError" by assigning it zero likelihood and then continuing to sample. This has been implemented for extremely large values of fEDE, and for cosmologies where P_k_max_h/Mpc=15 [or the value set in _DES_prototype.py] is not sufficient to compute P(k).

(2) For RSD: get fsigma8(z) from class, line 547, line 249.


### An Incomplete List of Line-by-Line Modifications of CLASS 
All edits in the code are tagged by "EDE-edit". Below are the line numbers of many of the edits, but not all of them.
background.c:
(1) 348  
(2) 464  
(3) 744  
(4) 348  
(5) 958  
(6) 2213  
(7) 2322  
(8) 2382  
(9) 2451  
input.c:
(1) 1179  
(2) 1225  
(3) 1841  
(4) 1845  
(5) 1851  
(6) 3000  
perturbations.c:
(1) 4795  
background.h:
(1) 52  
(2) 107  
(3) 172  
(4) 216  
(5) 522  
classy.pxd:
(1) 70,71  
classy.pyx:
(1) 1073  
(2) 1662  
(3) 1691  
(4) 1177 
