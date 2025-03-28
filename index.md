Master’s Degree in Data Science & Scientific Computing

## AI-Driven Dental Model Evolution: Updating 3D Representations Through Sequential 2D Image Analysis

**Candidate:** Agustin Campagnolo  
**Supervisors:** Prof. Fabio Anselmi, Prof. Luca Manzoni

---

# Introduction & Motivation

<div style="display: flex; align-items: center;">
  <div style="flex: 2;">

- **Challenge:** Traditional 3D dental imaging (e.g., CBCT, intraoral scanners) is costly, slow, and exposes patients to radiation.
- **Objective:** Reconstruct 3D dental models using just **five standard 2D intraoral images**.
- **Benefits:** Cost-effective, widely accessible, and radiation-free.
- **Clinical Focus:** Enhancing orthodontic monitoring during aligner therapy.
  </div>
  <div style="flex: 1; text-align: right;">

| !["5_1"](img/5_1.png "5_1") |
| :-------------------------: |
|  Image of intraoral views   |

  </div>
</div>
---

# Methodology

<div style="display: flex; align-items: center;">
  <div style="flex: 1;">

- **Tooth Segmentation:**
  - **Tool:** Mask R-CNN (ResNet-50 + FPN) detects individual teeth and extracts centroids.
- **Landmark Detection:**
  - **Automated:** Centroids are computed automatically.
  - **Manual:** Apex and base points are annotated due to current detection limits.
- **Tooth Numbering**
  - FDI notation using K-Means clustering and regression.
  </div>
    <div style="flex: 1; text-align: right;">

| ![alt text](img/mask3.png "Title") |
| :--------------------------------: |
|     Image of numbering result      |

  </div>
</div>
---

| ![alt text](img/4_2.png "Title") |
| :------------------------------: |
|    Image of revision software    |

---

<div style="display: flex; align-items: center;">
  <div style="flex: 1;">

- **3D Reconstruction:**
  - **Approach:** Multi-view triangulation with bundle adjustment.
  - **Enhancement:** Incorporates anatomical constraints for realistic tooth alignment.
  </div>
    <div style="flex: 1; text-align: right;">

<img src="img/camera2.png" alt="Animation" style="width: 80%;">

![](img/rec.gif "Animation")

  </div>
</div>

---

<!-- <video
data-autoplay
src="img/com.mov"> </video> -->

## ![](img/com.gif "Animation")

# Key Results

<div style="display: flex; align-items: center;">
  <div style="flex: 1;">

- **Accuracy Achieved:**
  - Centroid error: ~6.83 mm (mean across all teeth and all 14 reconstructed patients)
  - Overall landmark error (centroids, apex, base): ~9.07 mm
- **Insight:**
  - Enabling camera translation is critical for accuracy.
    </div>
  <div style="flex: 1; text-align: right;">

<!-- <video
data-autoplay
src="img/re.mov"> </video> -->

![](img/re.gif "Animation")

  </div>
</div>
---

# Conclusion

- **Current Limitations:**
  - Manual annotation for apex and base is time-consuming.
  - Small, homogeneous dataset (14 patients) limits broader applicability.
- **Future Improvements:**
  - Automate complete landmark detection.
  - Expand the dataset to improve generalizability.
  - Implement more complex anatomical constraints

---

# Thank You
