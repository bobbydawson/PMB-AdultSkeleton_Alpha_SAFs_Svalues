# Model specification

Mathematical notation follows the two Choi reference papers: phi_mu (\(\phi_\mu\)) is dimensionless microscale AF, phi_M (\(\phi_\mathrm{M}\)) is dimensionless macro AF, and capital Phi (\(\Phi\)) is SAF in kg^-1. The CSV label `AF` means phi_mu in the fitting dataset. A source-target pair is target <- source. Descriptive subscripts are upright in the manuscript; the underscore labels below are machine-readable names.

Ranges R_RBM, R_YBM and R_TB are in cm. The model uses R_marrow = cf R_RBM + (1-cf) R_YBM as an empirical arithmetic mixture descriptor, not an exact mixture CSDA range. delta=0.005 cm. Define u_R=R_RBM/delta, u_Y=R_YBM/delta, u_m=R_marrow/delta, x_m=R_marrow/l_marrow and x_t=R_TB/l_trab. The mean-intercept descriptors l=4V/S refer to isotropic line sampling; they are not asserted to be mean forward source-to-boundary distances under uniformly sampled interior sources. f_S is the fraction of trabecular surface adjoining RBM; f_R/f_Y are shallow volume fractions within RBM/YBM.

D(v;a,b,c)=(1+av)/(1+(a+b)v+cv^2). For a,b,c>=0 and v>=0 it is nonincreasing and D(0)=1. Its limit is zero if c>0; c=0 requires separate consideration.

Fitting minimizes the SciPy `least_squares` soft_l1 objective with f_scale=0.05 on unweighted AF residuals, one shared coefficient vector per pair. Parameter bounds and initial values are serialized in `model_spec.json`. Rows flagged trivial_zero or holdout_old are excluded. No prediction clipping is applied; the observed domain is reported in `results/training_domain_by_pair.csv`. Every held-out component loses all its energies. Cross-sex models use one sex for all fitting and the other for testing. Each validation fit starts from the fixed model starting point; central-fit parameters do not initialize validation folds. Three additional central-fit starts probe robustness; the original prespecified central fit is retained, and poorer basins are reported, not suppressed.

The source, sampling unit and dimensions of every input are documented in DATA_DICTIONARY.md. Neither pooled accuracy nor exact zero-range anchors establish safe extrapolation to ages, disease states, donors or geometry outside the supplied model collection.

Alpha forms (all outputs are phi_mu):

1. RBM <- RBM: 1 - a x_m/(1+c x_m) - b(1-cf)q/(1+q), q=R_RBM/d.
2. Endosteum <- RBM: f_R D(u_R;a,b,c).
3. RBM <- YBM: a cf q/(1+q), q=R_YBM/d.
4. Endosteum <- YBM: f_Y D(u_Y;a,b,c).
5. RBM <- TBS: (f_S/2)[1-a u_m/(1+u_m)] + b cf x_t/(1+c x_t).
6. Endosteum <- TBS: 1/[2(1+(c u_m)^p)]. This nonnegative-coefficient form cannot exceed one half.
7. RBM <- TBV: [a x_t/(1+b x_t)] [(f_S+cf c u_m)/(1+c u_m)].
8. Endosteum <- TBV: a x_t/[(1+b x_t)(1+c u_m)].

The parameter d is a fitted length in cm; the other coefficients are dimensionless. These equations describe the finite alpha training domain. Some large-range limits need not be physically valid, and no extrapolation guarantee is made.
