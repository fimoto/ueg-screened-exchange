# Release scope and limitations

Figure numbers in the dated numerical-history notes below retain earlier
manuscript numbering. The numerical plots of the kernels paper are Figs. S1–S4 of
its Supplementary Material, and those of the spin paper are Figs. 2 and 4–6 and Figs. S2–S5 of its Supplemental
Material (Fig. 2 is an analytic profile). The README lists the code and data for each.

For the current spin paper, all displayed measurements have been regenerated and
all replicate means and numerical settings are archived in `production/`.
`REPRODUCE_SPIN.md` defines the verified workflow. The uncertainties are sampling
errors; frequency and interpolation refinements are reported separately.
The current spin-paper SM Sec. S17 contains the remainder argument; the notebook checks
supporting identities, not the entire continuous-variable proof.

The notes below describe the shared algorithms and historical kernels-paper workflow.
Historical statements about unarchived spin-paper production values have been superseded
by the new spin-paper production release.

# Scope and known limitations

This note states, once, what this code establishes and what it does not.  It
mirrors the scope paragraph in the papers.

## Exact

The closed-form coefficients are evaluated integrals, not fits:

* `A0 = 3/(32 pi^5)` and `K[v] = e_2x = ln2/3 - 3 zeta(3)/(2 pi^2)` (Onsager);
* `A1`, `A2 = 8/15`, `A3 = -(16408/3375) C_Gamma`, each reduced to a
  one-dimensional integral in closed form (`lowdensity.py` recomputes all
  three from their profiles rather than reading the constant);
* `C_ln2 = -alpha_L (1-log2)/(6 pi^3)` and its spin dependence;
* the polarization identity `e_kite(rs,1) = e_kite(2^{-4/3} rs, 0)`.

`scripts/verify_analytics.py` and `tests/test_all.py` check every one of these
against an independent numerical evaluation; `scripts/check_A3_highprec.py`
does the A3 chain at 30 digits (needs `mpmath`).

## Standard asymptotics

The matched-asymptotic construction that attaches those coefficients to the
diagram, and the little-o remainders that go with it, are carried out at the
level customary in asymptotic analysis of many-body integrals.  Three steps
are done without tracking their constants: the weighted shell remainder at
small q, the uniformity in `s_lambda = Lambda/q` of the large-q estimate, and the
small-q bound on the absolute-value Fermi-shell factor `Zbar(q)`.  For the current spin-paper treatment see SM Sec. S17 of the separately supplied
`prb_manuscript_source/main_brm_kite_asymptotics.tex`;
this paragraph describes the historical/shared asymptotic workflow.

## Numerical

* Proposition ward (the cancellation of the coincidence logarithm between the
  vertex and self-energy insertions) is now checked from the defining integral
  of the insertion, not only by the identification made in the manuscript:
  `highdensity.self_energy_response_log_slope` fits the log(1/q) coefficient of
  `Pi_SE^(1)` directly and compares it with `+q_tf_sq**2 alpha(z)/4` at
  z = 0, 0.3, 1, 3, in the common response units. Multiplication by
  `pi**2*q_tf_sq` converts the former loop normalization into these units;
  the energy functional contains the compensating factor, so its energy is unchanged.
  The kernels-paper supplementary checks in `kernels_checks/` additionally
  evaluate the self-energy finite part and compare independent radial and
  original-coordinate reductions; the older shared-module test alone does
  not provide that check.

* Monte Carlo uncertainties are standard errors of the replicate mean,
  `s_R/sqrt(R)` with `s_R` the sample standard deviation over the `R`
  independently randomized replicates.
  **Finiteness of the second moment is not proved.**  The `1/(a+b)` factor of
  BRM Eq. (29) is not by itself a source of infinite variance: the transverse
  cross-section of the exit cap vanishes linearly at the particle-hole
  boundary, so the measure is `a da b db` and `int int ab/(a+b)^2` converges.
  The heavy tail of a naive sampler comes from approaching the Coulomb
  coincidence at the same time.  Empirically the production sampler behaves as
  if the variance were finite: Hill tail index 3.8 and 2.7 for the two terms,
  stable over a decade in the number of order statistics, and a replicate
  scatter falling as `N^-0.49` over a factor 16 in `N`; the uniform-ball
  sampler in `external/brm_literal_mc.py` measures 1.3-1.6, which is why it is
  kept only as a diagnostic.  A tail index below four makes the estimate of the
  variance itself converge slowly, so quote errors from many replicates, never
  from one run.
* The current spin-paper entries in `data/tables.json` are generated compatibility
  outputs from `production/raw/`. Historical kernels-paper records remain separate.
  Exact regeneration is checked under the pinned spin-paper numerical environment.
* `scripts/check_reduction_endtoend.py` is a consistency check, not a bound:
  its small-q tail fits `beta(Lambda)` from the lowest measured nodes and its
  large-q tail keeps only the leading term of the large-q expansion.
## Figures

* The README lists the plotting scripts. The numerical plots are generated by the supplied
  Python scripts; kernels-paper Figs. 1–4 and spin-paper Figs. 1 and 3 are manuscript-side TikZ.
* `figs/xc_grid.npy`, behind kernels-paper SM Fig. S4, is bit-reproducible with
  `scripts/sample_xc_grid.py` (seed 3).
* Kernels-paper SM Figs. S1–S3 are drawn from `figs/phi_grid_corrected.npy`: the supplied
  grid `figs/phi_grid.npy` (900 points, kept unchanged as the original) with all
  900 abscissae replaced by recomputed values;
  `scripts/build_phi_grid_corrected.py` merges the three runs below. The panel
  layout that produced the supplied grid was not recorded, so the supplied values
  themselves are not bit-reproducible.
  - **0 < y < 4 (712 points, recomputed).** `scripts/recompute_phi_range.py`
    (`figs/phi_recomputed_y_lt_4.json`, settings stored in the file) with
    `sample_phi_grid.phi`, `(nu, nv) = (32, 20)`, `u` cut 40 times the default
    scale. 36 checks at `(48, 24)` differ by at most `9.3e-12` and the same
    checks with the `u` cut doubled by at most `1.6e-12`; at `y = 1e-7` the value
    agrees with the small-`y` form `y[(log y)/2 + gamma/2 - 3/8]` to `1e-8`
    relative; `phi_point_crosscheck.py` agrees to `2e-12` at `y = 0.30` and
    `1.4e-9` at `y = 0.020`. The supplied values were off by up to `1.7e-5`
    absolute (`1e-3` relative near the zero of Phi); in the left panel of Fig. 2
    the change is at most `1.0e-5` on an axis of height `0.35`.
  - **4 < y < 60 (110 points, recomputed).** `scripts/recompute_phi_fig1_range.py`
    with every `u` interval split to at most half a period and the tail added,
    at `(nu, nv) = (32, 20)`; 12 points repeated at `(48, 24)` change by at most
    `1.4e-10`, halving the `u` cut at the largest `y` changes the value by
    `6.6e-12`, and an independent run at `(24, 16)` agrees on 86 points to
    `4.7e-9` (`figs/phi_fig1_range_recomputed.json`). The supplied values were off
    by up to `3.9e-5` absolute, which is `0.12` in `y^2 Phi` at `y = 55.9`.
  - **y > 60 (78 points, recomputed 2026-09-17).** `scripts/recompute_phi_range.py`
    (`figs/phi_recomputed_y_gt_60.json`, settings stored) at `(nu, nv) = (24, 16)`
    with a `u` cut of 200 and the tail correction; the checks at `y` = 60.2, 98.5,
    161.0, 263.3 and 400 repeated at `(32, 20)` and with the cut doubled differ by at
    most `4.2e-10`. The supplied values were off by up to `5.9e-5` absolute (up to
    100% relative near zeros of Phi), as an independent audit had found at two
    points. Not drawn in Fig. 2. In Fig. 4 the replacement changes `S(Lambda)` by
    at most `2.32e-05` (at Lambda = 6.4) and its maximum from `2.344048` to `2.344025`
    (text: `2.34`); changing the integration rule on the grid moves `S` by `6.5e-6`.
  - `scripts/phi_point_crosscheck.py` is valid only for `y <= 10`: its fixed
    panels do not resolve the oscillation at larger `y` (it is off by `4e-4` at
    `y = 30.2` although doubling its cut changes it by `1e-14`), and it now
    refuses larger `y`. Earlier versions of `sample_phi_grid.py` also did not
    resolve `cos(u y)` at large `y` (first all `u` panels had width 1, then only
    those above `u = 10` were split); every `u` interval is now split, the block
    size follows a memory budget, and `scripts/check_phi_panels.py` checks both.
    That split was at first forced to width 1 at every `y`, which at the
    smallest abscissa (`y = 1e-7`, `u` cut `1e10`) asked for `1e10` panels and
    exhausted memory before any integration; for `y <= pi` the panels above
    `u = 10` now grow geometrically, `min(pi/y, max(1, 0.1 u))` (505 panels at
    `y = 1e-7`; halving the growth rate changes Phi by at most `4e-13` relative),
    the layout for `y > pi` is unchanged, and more than `2e6` panels are refused.
  - Right panel of Fig. 2: the grid has 4-6 points per period there, and
    `y^2 Phi` oscillates with half-range 11-14, so the curve is a cubic spline
    through the grid, ending at the last grid point below 60 (`y = 58.743`), and
    the period average is the one-period mean of that spline applied twice
    (triangular window of half-width `2 pi`), evaluated exactly from the second
    antiderivative and drawn only where the window lies inside the data. The spline differs from direct evaluation at
    grid midpoints by `0.06` (`y = 50.1`) and `0.19` (`y = 56.6`) in `y^2 Phi`. The
    earlier plain mean over grid points in one period deviated from `2 pi log 2`
    by up to `1.7`; the present average by at most about `0.15` beyond `y = 10`.
  - The integral over the grid depends on the rule applied to the 900
    logarithmically spaced points (`0.249891`, `0.249915` or `0.249890` for the
    supplied grid with the gradient-weighted sum, the trapezoid of `Phi/y` in `y`,
    or the trapezoid in `log y`, against `D_0 = 0.249902`); the grid has about 6
    points per period of `sin y` at `y = 40`, 2.5 at `y = 100` and 1 at `y = 250`.
    None of these is a high-precision evaluation of `D_0` and none is used as one.
    `D_0` is known in closed form and is checked independently in the reviewer
    notebook. The corrected grid combines the three documented batches above. A
    new full 900-point integration was not run in the final audit.
* The distributed PDFs were written by Matplotlib 3.10.8 (pinned in
  `requirements-figs.txt`); under that version the kernels-paper plotting scripts reproduce them pixel for pixel.
* The plotted series of Figs. 2, 4, 5 and 6 are also written as ASCII to
  `figs/data/*.dat`, with the analytic endpoint constants in the headers;
  `figs/gnuplot/figures.gp` redraws all four from those files.  The constant
  `C_0` of Fig. 4 is now taken as the exact `-C_1/4` of Eq. (29); earlier
  versions of the figure used the rounded `-0.786`, a difference of 1e-4 in the
  plotted asymptote and invisible at the scale of the panel.

## Auxiliary APIs outside the production path

The stable helper `lindhard.exchange_self_energy_shape(k)` fixes the tail defect
of the former `lindhard.sigma_x(k)` API
and the optional Fortran helper using a convergent inverse-momentum series.
The duplicate Python self-energy helpers use the same stable evaluator.
Regression checks compare endpoints, the cusp, branch switches, and arguments
through `1e12` with high-precision references.

Three helpers outside the production path each had a closed form that
subtracts two nearly equal quantities, and each is now evaluated from a series
where that happens. `f_L(x)` returned a negative number above x ~ 10^6, because
the printed form subtracts 1/2 from a quantity tending to 1/2; it now uses
`sum_k x^{-2k}/((2k-1)(2k+1))` above x = 30. `Phi_spin(a)` returned exactly zero
at a = 10^6, because `2a^2 - a` cancels against the expansion of
`-2a^3 log(1+a)`; it now uses `log a - 2/3 + 3/(2a) - 9/(10a^2) + ...` above
a = 150. `yukawa_pole(rs)` capped its root bracket at 50 and could not bracket
the root at rs = 200 or 1000, where kappa grows like pi q_tf_sq / 8; the bracket now
expands until the sign changes. `tests/test_all.py` covers all three at the
arguments where the old forms failed, and at the switch points.

These helpers are still not used to produce any spin-paper figure or table. The
historical raw records were not rewritten; source compatibility and fresh
representative numerical-payload comparisons are recorded explicitly in the
final audit.
