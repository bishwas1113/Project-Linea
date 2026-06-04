# Project-Linea

Project Linea is a standalone, client-side web application designed to map and project PDF vector and raster sewing patterns at **100% true physical scale** onto a cutting mat. 

By utilizing a **4-point homography calibration grid**, Project Linea compensates for keystone/lens distortion (tilt, rotation, and height offset) caused by mounting a projector, aligning the digital lines exactly with physical grid units on a cutting mat.

---

## 🌟 Key Features

* **Keystone Correction via Homography:** A 4-point Direct Linear Transform (DLT) solver maps design-space coordinate systems onto the physical mat, maintaining scale precision even when the projector is tilted or offset.
* **Vector Path Sizing Engine (Dash Signature):** Automatically parses PDF operator lists, extracting vector paths and matching line styles (solid, dashed, dotted) to size keys. Allows isolating sizes, adjusting line thicknesses, and changing stroke colors dynamically.
* **Warped Raster PDF Projection:** Supports image-based PDFs (e.g. scans or non-vector prints). It renders PDF pages as images and warps them in real-time onto the screen using a high-performance **vertical perspective slicing algorithm**.
* **Scale Verification (Virtual Ruler):** Includes an interactive measurement verification tool. Click any two points on a projected test square to confirm exact dimensions in inches or centimeters.
* **Calibrate Board Customization:** Configure presets for standard cutting mats (imperial or metric), adjust grid opacity/thickness, bold lines at custom intervals, and toggle live projection background colors (black, white, blueprint, green).
* **Convexity Constraint Protection:** Self-correcting boundary logic (`isConvex`) locks corner handle drags to prevent quad folding, ensuring homography math never collapses or distorts.
* **Modern Scandinavian Aesthetic:** Includes 6 premium, cool-toned theme presets (Nordic Light, Fjord Slate, Stockholm Mist, Gothenburg Dusk, Copenhagen Sky, Birch & Sage) with styled scrollbars, dynamic checkboxes, and geometric sliders.
* **Privacy-First & Standalone:** Operates entirely client-side. No servers, no tracking, and no data leaves your browser. PDF.js is loaded dynamically via CDN, making the file a single-file application you can run offline.

---

## 🛠️ Tech Stack

* **Core:** HTML5, CSS3, JavaScript (ES6+)
* **UI Library:** React (loaded via UMD CDN scripts at runtime)
* **PDF Rendering Engine:** PDF.js (Mozilla)
* **Math Library:** Custom linear equation solver for 3x3 Homography matrices (Gaussian elimination with partial pivoting)
* **Design Language:** Geometric, minimalist Scandinavian styling built with Vanilla CSS variables and dynamically injected stylesheets

---

## 🚀 How to Run

Since Project Linea is a standalone client-side application, you can run it without installing any dependencies or setting up a local web server:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-username/project-linea.git
   cd project-linea
   ```
2. **Open in Browser:**
   Double-click `ProjectLinea_16.html` (or drag it into Chrome, Firefox, or Safari).
   *(Note: An active internet connection is required on the first launch to load React and PDF.js from CDNs, but it can be cached for offline use).*

---

## 📐 The Mathematics: 4-Point Homography

To correct keystone distortion, Project Linea computes a 3x3 projective transformation matrix $H$ that maps coordinates from the design space (a perfect flat rectangle representing the physical dimensions of your cutting mat) to screen coordinates (the distorted quad representing where your projector throws light).

Given four matched source points $(x_i, y_i)$ and destination points $(u_i, v_i)$ for $i \in \{1,2,3,4\}$, we solve the system:

$$
\begin{bmatrix} 
u_i \\
v_i \\
1
\end{bmatrix} 
\sim
H 
\begin{bmatrix} 
x_i \\
y_i \\
1
\end{bmatrix}
=
\begin{bmatrix} 
h_{11} & h_{12} & h_{13} \\
h_{21} & h_{22} & h_{23} \\
h_{31} & h_{32} & 1 
\end{bmatrix}
\begin{bmatrix} 
x_i \\
y_i \\
1
\end{bmatrix}
$$

Expanding this relation yields two linear equations per corner, totaling 8 equations for the 8 degrees of freedom in $H$:

$$u_i(h_{31}x_i + h_{32}y_i + 1) = h_{11}x_i + h_{12}y_i + h_{13}$$
$$v_i(h_{31}x_i + h_{32}y_i + 1) = h_{21}x_i + h_{22}y_i + h_{23}$$

Project Linea solves this linear system dynamically using a custom Gaussian Elimination solver. All coordinates, grids, and PDF lines are then mapped through $H$ before rendering, producing a projection that is geometrically square and physically to-scale.

---

## 📖 Step-by-Step Guide

### Step 1: Calibrate Board
1. Select your cutting mat's size preset (e.g. `18 × 24 in` or `36 × 48 in`) or set custom dimensions.
2. Select your unit (`Metric (cm)` or `Imperial (in)`), grid cell subdivisions, and major line intervals.
3. Choose a cursor assist mode (`crosshair`, `loupe`, or `snap`) and select your drag/float interaction style.
4. Click **Calibrate: Start Projecting** to go fullscreen. Drag the 4 corner handles until the projected grid lines align exactly with your physical mat's grid lines. Nudge with arrow keys for sub-pixel precision.
5. Click **Save Calibration** when aligned.

### Step 2: Load Pattern
1. Upload your sewing pattern PDF.
2. The file will render all page thumbnails and automatically run size-legend detection.
3. If sizes are graded, they will display in the list. You can choose a custom swatch page if auto-detect misses the sizing legend.
4. The pattern layout is **automatically assembled** into a default grid. Click **Ready to Project**.

### Step 3: Project Pattern
1. Select the page background, transparency, and line opacity.
2. If your PDF is **raster/image-based** (like the *Simple Fanny Pack*), slide the **Page backing opacity** slider to `1.0` to display the full image page, and optionally toggle **Global invert** to make it white-on-black for higher contrast.
3. Adjust the **Fine-tune scale** slider or zoom scale if needed.
4. Click **Project Pattern** to enter fullscreen projection mode and trace your pattern pieces directly onto your fabric!

---

## 🔒 Privacy & Data Safety

Project Linea does not collect, store, or transmit any user data or uploaded files. The PDF parsing and image rendering take place strictly inside your browser's local sandbox memory.
