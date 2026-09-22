# Multi-Modal, Sun-Angle and Scale Invariant Image Correspondence using Chandrayaan-2 Optical Images

**Smart India Hackathon 2026 | Team: Game of Nodes**

A robust image correspondence pipeline that aligns and matches lunar surface imagery across three heterogeneous Chandrayaan-2 sensors — OHRC, TMC, and IIRS — despite large differences in resolution, spectral range, scale, and sun-angle illumination.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Our Solution](#our-solution)
- [Key Features](#key-features)
- [Data Sources](#data-sources)
- [Technical Approach](#technical-approach)
- [Tech Stack](#tech-stack)
- [Pipeline Overview](#pipeline-overview)
- [Installation](#installation)
- [Impact and Applications](#impact-and-applications)
- [Feasibility and Scalability](#feasibility-and-scalability)
- [Roadmap](#roadmap)
- [References](#references)
- [Team](#team)
- [License](#license)

---

## Problem Statement

Chandrayaan-2 carries multiple optical payloads that image the lunar surface under very different conditions:

| Sensor | Resolution | Spectral Type | Coverage |
|--------|-----------|----------------|----------|
| OHRC (Orbiter High Resolution Camera) | ~0.32 m/pixel | Panchromatic | Local, high-resolution |
| TMC-2 (Terrain Mapping Camera) | ~5 m/pixel | Panchromatic | Global |
| IIRS (Imaging Infra-Red Spectrometer) | ~80 m/pixel | Multi-spectral | Global |

Because these sensors differ so much in resolution, spectral response, and imaging geometry (including sun angle at the time of capture), directly matching features between them is unreliable using standard techniques. This is especially difficult in permanently shadowed polar regions, where illumination is minimal or absent. There is no existing open pipeline that reliably fuses these three sensor types into a single, spatially consistent reference frame.

## Our Solution

We propose a unified, sensor-agnostic image correspondence pipeline that identifies and matches the same lunar surface features across OHRC, TMC, and IIRS imagery, regardless of resolution, scale, or illumination differences.

The pipeline normalizes each sensor's output, extracts robust and illumination-invariant features, matches them across sensor pairs, and estimates a geometric transformation to align all sources into a common spatial reference. The result is a registered, multi-sensor lunar map along with a confidence visualization showing the reliability of each matched region.

In short: different sensors, different views, one unified Moon.

## Key Features

- Cross-sensor matching between panchromatic, high-resolution, and multi-spectral lunar imagery
- Illumination-aware matching that remains robust under low sun-angle and shadow-heavy conditions, including permanently shadowed polar craters
- Scale-invariant correspondence across a resolution gap of roughly 250x (OHRC to IIRS)
- Explainable output through visual confidence and correspondence maps rather than a black-box result
- Modular design that allows individual pipeline stages to be replaced or extended
- Built entirely on open-source tools and publicly available ISRO data

## Data Sources

- ISRO, Chandrayaan-2 Payloads: Orbiter High Resolution Camera (OHRC)
- ISRO, Chandrayaan-2 Science: Terrain Mapping Camera-2 (TMC-2)
- ISRO, Chandrayaan-2 Science: Imaging Infra-Red Spectrometer (IIRS)

All datasets are publicly released with accompanying sensor specifications and metadata. See [References](#references) for source links.

## Technical Approach

The pipeline is organized into six main stages:

1. **Pre-processing**
   Radiometric normalization, noise removal, resolution and scale handling, and sun-angle/illumination correction to bring all sensor inputs into a comparable form.

2. **Feature Detection and Description**
   Robust keypoint detection using classical descriptors (SIFT, ORB) combined with deep feature matching (LightGlue) and shadow-invariant edge descriptors, extracted at multiple scales.

3. **Feature Matching**
   Matching via FLANN and brute-force approaches, refined with ratio testing, cross-checking, confidence scoring, and geometric consistency filtering.

4. **Geometric Estimation**
   Homography and affine transformation models fitted using RANSAC and USAC-MAGSAC for robust outlier rejection and a spatially well-distributed inlier set.

5. **Sub-pixel Refinement and Warping**
   Iterative refinement of keypoint locations to improve positional accuracy, followed by perspective/affine warping of the source image onto a common reference frame.

6. **Registered Output**
   Final aligned multi-sensor image, correspondence map, and confidence map indicating match reliability across the scene.

## Tech Stack

**Language:** Python 3.x

**Core Libraries:** OpenCV, PyTorch, NumPy, SciPy, Matplotlib

**Algorithms and Models:** SIFT, ORB, FLANN / Brute-Force Matcher, LightGlue, RANSAC, USAC-MAGSAC

**Hardware:** Runs on standard CPUs for prototyping; supports NVIDIA GPU (CUDA) acceleration for large-scale processing

All tools used are open-source with no proprietary licensing dependencies.

## Pipeline Overview

```
OHRC Image ─┐
TMC Image   ├──> Pre-processing ──> Feature Detection ──> Feature Matching
IIRS Image ─┘          │                                          │
                       v                                          v
              Sun-angle / scale                       Geometric Estimation
              normalization                            (RANSAC / USAC-MAGSAC)
                                                                   │
                                                                   v
                                              Sub-pixel Refinement and Warping
                                                                   │
                                                                   v
                                    Registered Output: Aligned Image, Correspondence Map, Confidence Map
```

## Installation

```bash
git clone https://github.com/codesbyashmit/chandra-link.git
cd chandra-link
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Requirements include: `opencv-python`, `torch`, `numpy`, `scipy`, `matplotlib`

## Impact and Applications

**Scientific Advancement**
Enables multi-sensor data fusion, supports detailed geological mapping, and aids temporal analysis and change detection such as new craters or surface variations.

**Improved Lunar Mapping**
Produces accurate, seamless lunar maps by aligning heterogeneous observations into a unified spatial reference, including shadow-prone polar regions.

**Mission Planning Support**
Assists landing-site assessment, terrain analysis for future rover navigation, and identification of scientifically significant regions.

**Social and Economic Benefits**
Makes lunar data more accessible for research and education, reduces computational effort in image registration, and relies entirely on open-source tools to minimize cost.

**Broader Applicability**
The modular pipeline extends naturally to future ISRO missions such as Mangalyaan and Shukrayaan, and applies to Earth observation and other planetary datasets facing similar multi-sensor alignment challenges.

## Feasibility and Scalability

- **Data Feasibility:** Built entirely on publicly released Chandrayaan-2 datasets with full sensor metadata, removing dependency on restricted or proprietary data.
- **Resource Feasibility:** Runs on standard computing hardware for prototyping, with optional GPU acceleration for scale.
- **Scalability:** Modular architecture allows individual components to be replaced or extended, supporting growth from small test regions to full lunar-surface datasets.
- **Cost-Effectiveness:** Uses only open-source tools with no licensing costs, making it sustainable to maintain and hand off to future research teams.

## Roadmap

1. **Data Setup** — Collect and preprocess OHRC, TMC, and IIRS datasets
2. **Pipeline Development** — Implement feature extraction, matching, and geometric estimation
3. **Testing and Validation** — Evaluate on sample datasets and refine accuracy
4. **Optimization** — Improve robustness and processing efficiency
5. **Prototype Ready** — Deliver a scalable, deployment-ready solution

## References

1. ISRO, Chandrayaan-2 Science — Terrain Mapping Camera-2 (TMC-2): [https://www.isro.gov.in/Chandrayaan2_science.html](https://www.isro.gov.in/Chandrayaan2_science.html)
2. ISRO, Chandrayaan-2 Payloads — Orbiter High Resolution Camera (OHRC): [https://www.isro.gov.in/chandrayaan2-payloads.html](https://www.isro.gov.in/chandrayaan2-payloads.html)
3. ISRO, Chandrayaan-2 Science — Imaging Infra-Red Spectrometer (IIRS): [https://www.isro.gov.in/Chandrayaan2_science.html](https://www.isro.gov.in/Chandrayaan2_science.html)
4. Lowe, D. G. (2004). Distinctive Image Features from Scale-Invariant Keypoints. *International Journal of Computer Vision*, 60(2), 91-110. DOI: [https://doi.org/10.1023/B:VISI.0000029664.99615.94](https://doi.org/10.1023/B:VISI.0000029664.99615.94)
5. Sarlin, P.-E., DeTone, D., Malisiewicz, T., Rabinovich, A. (2020). SuperGlue: Learning Feature Matching with Graph Neural Networks. *CVPR 2020*, 4938-4947. DOI: [https://doi.org/10.1109/CVPR42600.2020.00499](https://doi.org/10.1109/CVPR42600.2020.00499)
6. Lindenberger, P., Sarlin, P.-E., Pollefeys, M. (2023). LightGlue: Local Feature Matching at Light Speed. *ICCV 2023*, 17627-17638. DOI: [https://doi.org/10.1109/ICCV51070.2023.01616](https://doi.org/10.1109/ICCV51070.2023.01616)
7. Barath, D., Noskova, J., Matas, J. (2019). MAGSAC: Marginalizing Sample Consensus. *CVPR 2019*, 10197-10205. DOI: [https://doi.org/10.1109/CVPR.2019.01044](https://doi.org/10.1109/CVPR.2019.01044)
8. Barath, D., Noskova, J., Ivashechkin, M., Matas, J. (2020). MAGSAC++, a Fast, Reliable and Accurate Robust Estimator. *CVPR 2020*, 1304-1312. DOI: [https://doi.org/10.1109/CVPR42600.2020.00138](https://doi.org/10.1109/CVPR42600.2020.00138)

## Team

**Game of Nodes** — Smart India Hackathon 2026

**Team Members** 
Ashmit Kumar **(Team Leader)**
Harshita Chauhan
Aditya Gupta
Aditya Kumar
Tanushka Gupta
Dev Prakash Azad

## License

This project is intended for the Smart India Hackathon 2026 submission.
