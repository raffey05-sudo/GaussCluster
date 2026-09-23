# GaussCluster

**GaussCluster** is an autonomous, 3D kinematic Gaussian Mixture Model (3GMM) pipeline designed to isolate open clusters and their extended tidal halos from dense galactic backgrounds.

It operates entirely in probabilistic kinematic space (proper motion and parallax) using Gaia DR3 data, overcoming the structural limitations of standard spatial density-based algorithms (like HDBSCAN) in heavily obscured, differentially reddened environments (e.g., Cygnus X).

## Features
- **Autonomous Data Acquisition:** Interfaces directly with the Gaia DR3 archive via `astroquery`.
- **3D Kinematic Anchoring:** Uses a 3D sigma-clipping algorithm on ingested seed cores to calculate an initial cluster centroid.
- **Non-Parametric Field KDE:** Maps the asymmetric astrometric density of the surrounding galactic field using a Kernel Density Estimator.
- **Dynamic Bayesian Prior:** Calculates true local cluster density without manual steering.
- **Diagnostic Dashboard Export:** Automatically extracts high-probability members ($P \ge 0.9$) and generates performance diagnostics.

## Usage
The primary script `gausscluster_v7.py` acts as the core pipeline engine. `compare_clusters.py` is used to cross-reference outputs against density-based models.

```bash
python gausscluster_v7.py
