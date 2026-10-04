# Validation runs added in revision (October 2026)

All scripts are run from anywhere; they change to the repository root. Results are written to `validation/results/` as one JSON file per point or per run, containing every replicate value, the settings, and SHA-256 hashes of the sources used.

| Script | Purpose | Results | Manuscript |
|---|---|---|---|
| `run_spin_validation.py` | Estimator A at arbitrary $(r_s,\zeta)$ with input checks | per-point JSON | SM S8, S9, S11 |
| `run_queue.sh` | the full set of Estimator A runs below | `identity/`, `fig8_m17,m19,m21/`, `fig8_m19_seedB/`, `lowdensity/`, `nearfull/` | SM S8, S9, S11 |
| `run_axis.py` | Estimator B: bare limit, grid refinement, comparison with A, identity | `axis/` | SM S10 |
| `run_residual_grid.py` | Estimator B interpolation residual at $r_s=10$ for $n_q=24,48,96$ on identical samples | `residual_grid_rs10.json` | Sec. V B, SM S10 |
| `run_revision_checks.py` | $r_s=2\times10^4$ seed dependence; Estimator B residual at $n_q=24$ | `revision_checks.json`, `axisB_residual_rs10.json` | Sec. IV A, SM S10, S11 |
| `run_s7_converged.py` | third implementation with a momentum-grid boundary at $q=2$ | `s7_converged.json`, `s7_converged_summary.json` | SM S7 |
| `make_sm_lowdensity_figure.py` | figure of SM S11 from the stored replicates (pixel-identical to the published figure) | `fig_sm_lowdensity_spin.pdf` | SM S11 |
| `analyze_validation.py` | aggregates the Estimator A runs into `summary.md` | `summary.md` | SM S8–S11 |
| `run_residual_grid_ext.py`, `analyze_residual_ext.py` | Estimator B interpolation residual on the converged grid at $r_s=1$, 10, 60 (about 40 min) | `results/residual_grid_ext.json`, `results/residual_ext_summary.json` | Table IV column B, SM S10 |
| `run_cutoff_scan.py`, `analyze_cutoff_scan.py` | momentum-cutoff test of the difference from the published BRM values, with every aggregate quoted in the paper (common cutoff, residual sums, weighted mean with the direct-side covariance, joint fit) produced by the analysis script (scan about 15 min) | `results/cutoff_scan.json`, `results/cutoff_scan_summary.json` | Sec. V C, SM S22 (Table SXXI) |
| `check_A3_kin_quadrature.py` | direct two-dimensional quadrature of $A_{3,\rm kin}$ against its closed form | printed | Appendix A, SM S6 |
| `make_sm_additions.py` | first draft of the SM S8–S11 tables (the published text was edited afterwards) | `supplement_additions_20261001.tex`, `S11_tables.tex` | SM S8–S11 |

Run times on one core: an Estimator A point at $m=19$ and 16 replicates takes 20–40 s; the whole `run_queue.sh` about 1.5 h; an Estimator B point at $2^{15}$ points and $n_q=24$ about 2.5 min.

`patches/` contains two proposed changes (as `.patch` files) to production code that are deliberately not applied, because `production/verify.py` checks the source hashes of the production records: an input check in `external/benites_direct.py` (F3) and a change of the small-$q$ branch of `g_brm` (F4). Neither affects any reported number.

## High-density expansion (`high_density/`)

| Script | Purpose | Results | Manuscript |
|---|---|---|---|
| `hd_kernel.py` | long-wavelength kernel: $A(z)$ and the finite-part $B(z)$ | none | Sec. IV, SM Eq. for $A$, $B$ |
| `hd_coefficients.py` | $c_{\rm hd}(\zeta)$ and $c_1(\zeta)$, with the full-polarization rescaling check (about 4 min) | `results/hd_coefficients.json` | Sec. IV, SM Table SXV |
| `run_direct_high_density.py` | Estimator A at $r_s=10^{-3}$–$1$ with disjoint master seeds (asserted), 64 replicates for $r_s\le0.1$, term replicates stored (about 2 min per density) | `results/direct_high_density.json` | Table II, SM Table SXIX |
| `fit_high_density.py` | seed-disjointness check, $c_2$ with analytic $c_1$ and a free fit of $c_1$ and $c_2$ on the windows $r_s\le0.1$, $0.03$, $0.01$ | `results/hd_fit.json` (read by `make_fig_high_density.py`) | Sec. IV, Table II, SM Sec. S19 and Table SXVIII |
