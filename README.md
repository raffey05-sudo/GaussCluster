# GaussCluster
**A 3D Kinematic Gaussian Mixture Model Pipeline for Open Cluster Extraction**

A probabilistic detection pipeline built on Gaia DR3 astrometry, developed as part of an Astrophysics dissertation. `GaussCluster` is specifically designed to ingest spatial seed cores and recover extended, diffuse tidal halos in heavily obscured galactic environments.

## What it does
While density-based algorithms like HDBSCAN (e.g., STORM, Hunt et al. 2023) excel at identifying continuous regions of high spatial density, they often aggressively trim a cluster's footprint to prevent bridging into the background field. 

`GaussCluster` solves this by operating strictly in 3D probabilistic kinematic space (proper motion $\mu_{\alpha*}$, $\mu_{\delta}$, and parallax $\varpi$). It isolates the true dynamical members of a cluster from dense galactic backgrounds, completely independent of photometric priors. 

From an initial seed coordinate or catalogue, the pipeline can:
- **Autonomously filter astrometry:** Interface directly with Gaia DR3, purging sources with RUWE $\ge 1.4$ or high proper motion errors.
- **Anchor the Kinematic Core:** Utilize a 3D sigma-clipping algorithm to establish a systemic cluster centroid.
- **Map the Field Topology:** Employ a Non-Parametric Kernel Density Estimator (KDE) to accurately map the asymmetric astrometric density of the surrounding galactic field.
- **Calculate Dynamic Priors:** Establish a dynamic Bayesian prior ($n_c$) without manual steering, discovering the true physical scale of the cluster.
- **Iterative Refinement:** Use an Expectation-Maximization (E-M) algorithm, guarded by a Mahalanobis distance truncation ($3\sigma$), to prevent variance inflation from extreme outliers.
- **Export Diagnostics:** Automatically extract high-probability members ($P \ge 0.9$) and generate performance diagnostics.

## Validated Results
The pipeline was critically evaluated and validated against two extreme targets:

| Cluster | Members Recovered | Notes |
| :--- | :--- | :--- |
| **NGC 957** | 339 ($P \ge 0.9$) | Clean validation target. Isochrone age precisely matches literature (10 Myr). |
| **Berkeley 87** | 103 ($P \ge 0.9$) | Highly complex, differential reddening target (Cygnus X). Pipeline successfully bypassed massive spatial contamination to recover a clean kinematic Main Sequence. |

## Installation
Tested on Python 3.9+. A virtual environment is recommended.
```bash
git clone https://github.com/raffey05-sudo/GaussCluster.git
cd GaussCluster
pip install -r requirements.txt
```
*(Dependencies include `numpy`, `scikit-learn`, `astropy`, `astroquery`, and `matplotlib`).*

## Quick Start
The primary script `gausscluster_v7.py` acts as the core pipeline engine. It is currently configured to run standalone extractions.

```bash
# Run the core 3GMM pipeline
python gausscluster_v7.py

# Cross-reference outputs against density-based models (e.g., STORM)
python compare_clusters.py
```

## Output Files
Each successful cluster extraction produces two primary outputs:
1. **Diagnostic Dashboard (`.png`):** A 4-panel visual export showing the 2D proper motion VPD, the 3D KDE field distribution, the final spatial footprint, and the isolated Colour-Magnitude Diagram (CMD).
2. **Kinematic Catalogue (`.csv`):** A clean data table containing all Gaia DR3 astrometry and photometry for stars meeting the strict $P \ge 0.9$ kinematic membership threshold. 

## Known Limitations
- **Mahalanobis Truncation Bias:** To force E-M convergence on compact cores, a strict $3\sigma$ distance truncation is applied. This mathematically fits a shortened (rather than full) trivariate Gaussian, introducing a slight downward bias in the recovered velocity and parallax dispersions.
- **Differential Reddening & Isochrones:** While the kinematics are unaffected by interstellar dust, attempting to fit single optical isochrones to heavily obscured targets (like Be87) will artificially drag the fit rightwards, mathematically mimicking an older stellar population. Infrared photometry is required for accurate fundamental parameters in these regions.

## Citation
If you use this pipeline in your research, please cite this repository:
```bibtex
@software{gausscluster2026, 
    title = {{GaussCluster}: A 3D Kinematic Open Cluster Extraction Pipeline}, 
    author = {Shakoor, Abdul Raffey}, 
    year = {2026}, 
    url = {https://github.com/raffey05-sudo/GaussCluster} 
}
```
