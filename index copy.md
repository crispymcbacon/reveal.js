Master’s Degree in Data Science & Scientific Computing

## AI-Driven Dental Model Evolution: Updating 3D Representations Through Sequential 2D Image Analysis

**Agustin Campagnolo**

---

# Context & Scope

- **Goal:** Reconstruct 3D dental models from only **5 standard intraoral 2D images**
- **Target:** Orthodontic patients (aligner therapy)
- **Benefits:**
  - Lower cost
  - Greater accessibility
  - No radiation exposure (unlike CBCT)
- **Scope:** Proof of concept with 14 patients (2 visits each)

---

# Motivation & Objectives

### Motivation

- Traditional 3D methods (CBCT, intraoral scanners) are:
  - **Expensive**
  - **Time-consuming**
  - Involve **radiation** (CBCT) or require specialized equipment
- Aligners require **frequent monitoring** of tooth movement

### Objectives

1. Build a **pipeline** from 2D photos to 3D models
2. Use **deep learning** for tooth segmentation
3. Detect key **landmarks** (centroid, apex, base)
4. Apply **multi-view triangulation** for 3D reconstruction
5. Evaluate accuracy vs. manually annotated reference models

---

# Background & Literature (1/2)

### Orthodontic Imaging & Tooth Movement

- **CBCT:** High precision 3D imaging, but high cost & radiation
- **Intraoral Scanners:** Accurate yet expensive and require skilled operators
- **2D Photography:** Simple, non-invasive, and widely available

### The Role of Computer Vision

- **Goal:** Infer 3D structure from multiple 2D views.
- **Key Techniques:**
  - **Segmentation:** Identifying teeth in 2D.
  - **Multi-view Geometry:** Uses geometric relationships between images.
  - **Optimization:** Refining 3D estimates (like Bundle Adjustment).

---

# Background & Literature (2/2)

### Challenges in 2D-to-3D Dental Reconstruction

- **Occlusion:** Limited visibility of posterior teeth due to anatomical structures and soft tissues complicates 3D localization.
- **Perspective Distortion:** Variations in camera pose introduce geometric distortions, hindering accurate correspondence matching across views.
- **Scale Ambiguity:** The absence of a defined metric scale in 2D projections necessitates the estimation of a global scale factor.
- **Anatomical Plausibility:** Reconstructions must adhere to known anatomical constraints of the dental arch and inter-tooth relationships.

### Addressing the Challenges: Core Techniques

- **Segmentation via Mask R-CNN:** Employs a Mask R-CNN for robust instance segmentation of individual teeth, providing 2D bounding boxes and masks.  This addresses the initial challenge of tooth identification within complex images.
- **Multi-view Triangulation with Optimization:** Utilizes non-linear least squares optimization (akin to Bundle Adjustment) to refine 3D landmark positions and camera parameters, mitigating perspective distortion and scale ambiguity.
- **Incorporation of Anatomical Constraints:** Integrates prior knowledge of dental anatomy, such as expected inter-tooth distances, as soft constraints within the optimization process, promoting biologically plausible reconstructions.
---

# Methodology Overview (1/2)

### Pipeline Architecture

1. **Segmentation:**  
   Use Mask R-CNN to detect individual teeth
2. **Landmark Detection:**
   - **Automatic:** Compute centroids from bounding boxes
   - **Manual:** Annotate apex and base landmarks
3. **Tooth Numbering:**  
   Use FDI notation with K-Means clustering & regression for quadrant assignment
4. **3D Reconstruction:**  
   Apply multi-view triangulation (non-linear least squares, bundle adjustment)  
   Integrate camera parameters and anatomical constraints

---

# Methodology Overview (2/2)

### Data & Processing

- **Data:**
  - 14 patients
  - 5 intraoral views per patient: frontal, left, right, upper, lower
  - 2 visits (initial & follow-up)
- **Segmentation Details:**
  - Fine-tuned Mask R-CNN on DentalAI dataset (2495 images) for individual teeth
  - Generates bounding boxes, masks, and centroids
- **Optimization:**
  - Minimizes reprojection error using bundle adjustment
  - Critical: Enabling camera translation greatly improves accuracy

---

# Deep Learning Segmentation & Landmark Detection

### Tooth Segmentation

- **Model:** Mask R-CNN (ResNet-50 + FPN)
- **Training:** Fine-tuned on a dental dataset
- **Output:**
  - Segmentation masks & bounding boxes
  - Automatic centroid extraction

### Landmark Detection

- **Centroid:** Directly computed from segmentation
- **Apex & Base:** Currently manually annotated
  - These landmarks are more prone to error due to occlusion and subjectivity

---

# Mapping 2D Points to 3D Space via Triangulation

### Core Idea

- Reconstruct 3D locations of dental landmarks using their **2D positions** from multiple views.

- **Pinhole Camera Model:**  
  Each camera projects 3D points onto a 2D image plane using intrinsic and extrinsic parameters.

### Projection Equation

$$
\begin{aligned}
\mathbf{u} &= \mathbf{K} \, [\mathbf{R} \mid \mathbf{t}] \, \mathbf{X}^h \\\\
\text{where} \quad \mathbf{X}^h &= \begin{bmatrix} X \\\\ Y \\\\ Z \\\\ 1 \end{bmatrix}, \quad \mathbf{u} = \begin{bmatrix} u \\\\ v \\\\ w \end{bmatrix}
\end{aligned}
$$

- $\mathbf{K}$: Intrinsic matrix (focal length, principal point)
- $\mathbf{R}, \mathbf{t}$: Rotation and translation (extrinsic parameters)
- **Projection:** $(u/w, v/w)$ via perspective division

---

# Mathematical Summary of Triangulation

### Reprojection Error Minimization

- For each landmark $k$ in view $v$:

  - **Observed 2D point:** $(u_{v,k}, v_{v,k})$
  - **Projected point:**
    $$
    \hat{\mathbf{u}}_{v,k} = \mathbf{P}_v \begin{bmatrix} X_k \\\\ Y_k \\\\ Z_k \\\\ 1 \end{bmatrix}
    $$
  - **After normalization:**
    $$
    \hat{u}_{v,k}  = \frac{\hat{u}\_{v,k}}{\hat{w}\_{v,k}}, \quad \hat{v}\_{v,k} = \frac{\hat{v}\_{v,k}}{\hat{w}\_{v,k}}
    $$

- **Reprojection Residual:**

  $$
  \mathbf{r}\_{v,k} = \begin{bmatrix} u_{v,k} - \hat{u}\_{v,k} \\\\ v_{v,k} - \hat{v}\_{v,k} \end{bmatrix}
  $$

- **Cost Function:**

  $$
  E = \sum_{v,k} w_k \, \|\mathbf{r}_{v,k}\|^2
  $$

  where $w_k$ is the landmark weight (higher for centroids)

---

# Optimization & Anatomical Constraints

### Key Components in the Reconstruction

- **Camera Translation:**  
  Essential for aligning 2D observations; enabling translation reduces errors drastically.

- **Global Scale Factor $\alpha$:**  
  Adjusts the overall size; fine-tunes the scale ambiguity inherent in 2D images.

- **Anatomical Constraints:**  
  Soft constraints (e.g., expected inter-tooth distance $\sim 0.3$ in normalized units)  
  Help maintain a realistic dental arch geometry during optimization

---

# Evaluation Metrics

- **Mean Centroid Distance:**  
  Average Euclidean distance between reconstructed and reference centroids

- **RMSE & MAE:**  
  Assess overall error magnitude and sensitivity to outliers

- **Hausdorff Distance:**  
  Measures worst-case discrepancy between sets of points

_These metrics are computed across all views and compared to manually annotated 3D ground truth._

---

# Key Results

- **Overall Accuracy:**  
  Best configuration achieved ~6.83 mm mean centroid error (std ~1.11 mm)

- **Critical Finding:**  
  Enabling **Camera Translation** during optimization is vital

  - Disabling it results in errors around 47.9 mm

- **Other Parameters:**  
  Arch constraints and alpha adjustments had minimal impact once translation was enabled

- **Tooth-Level Insights:**  
  Posterior teeth and manually annotated apex/base landmarks show higher errors

---

# Discussion

- **Proof of Concept Achieved:**  
  3D models from just 5 intraoral photos are feasible for monitoring tooth movement

- **Clinical Relevance:**  
  Can lower cost and radiation exposure in aligner therapy monitoring

- **Current Limitations:**

  1. Manual apex/base annotation is labor-intensive and subjective
  2. Small, homogeneous dataset (14 patients) limits generalizability
  3. Simplistic anatomical constraints

- **Opportunities for Improvement:**
  - Automate landmark detection
  - Improve occlusion handling and camera calibration
  - Expand dataset diversity

---

# Conclusion & Future Work

### Conclusions

- Achieves ~6–7 mm mean error — promising for coarse alignment tracking
- **Camera translation** and deep learning segmentation are key
- Offers a low-cost, radiation-free alternative to conventional 3D imaging

### Future Directions

1. **Automate Apex/Base Detection**
2. **Improve Occlusion Handling**
3. **Refine Anatomical Constraints**
4. **Expand & Diversify Dataset**
5. **Clinical Validation**

---

# Thank You!

**Questions?**

_Contact: [Your Email]_
