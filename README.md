<div align=center>
  <h1>
    Implicit Moving Least Squares and Marching Squares
  </h1>
  <p>
    <a href=https://mhsung.github.io/kaist-cs479-spring-2025/ target="_blank"><b>KAIST CS479: Machine Learning for 3D Data</b></a><br>
    Programming Assignment 4
  </p>
</div>

<div align=center>
  <p>
    Instructor: <a href=https://mhsung.github.io target="_blank"><b>Minhyuk Sung</b></a> (mhsung [at] kaist.ac.kr)<br>
    TA: <b>Jisung Hwang</b></a>  (4011hjs [at] kaist.ac.kr)
  </p>
</div>

<div style="display: flex; justify-content: center; align-items: center; width: 100vw;">
  <img src="./asset/teaser.png" style="width: 60vw; height: auto;"/>
</div>


## Description
Point cloud representations provide a flexible and powerful means of capturing 3D geometry, but they often remain ambiguous for tasks requiring a structured or clearly defined shape. While point clouds can accurately encode spatial information, they are not inherently suited to applications such as direct rendering, simulation, or manufacturing where a continuous surface representation is needed. Consequently, working directly with point clouds can be challenging when the goal is to obtain a precise boundary or closed form.

In this assignment, we address these limitations by converting raw point clouds into an implicit function, then extracting a contour (in 2D) or mesh (in 3D). By transforming the unstructured points into a smoothly varying scalar field, we enable standard algorithms—such as Marching Squares or Marching Cubes—to generate a final structured boundary or surface. This two-step process expands the utility of raw point data, allowing it to be readily used in visualization, geometric processing, and downstream applications that demand well-defined edges, surfaces, or volumes.


## Setup

This assignment uses pytorch, numpy and matplotlib only. You can use the setup from assignment 3.
```
conda activate cs479-gs
```

You can install matplotlib by the following command
```
pip install matplotlib
```


**You MUST NOT import additional libraries**

## Code Structure
This assignment is done on only one jupyter(ipython) notebook.
```
mls_ms
│
├── data                <- Directory for data files.
├── main.ipynb          <- Main file that you should work on.
└── README.md           <- This file.
```

## Task 1: Implicit Moving Least Squares for Implicit Function Approximation

In this assignment, we use **Implicit Moving Least Squares (IMLS)** to approximate a local signed distance function $f(\mathbf{x})$ given a set of points $\{\mathbf{p}_i\}$ and their associated normals $\{\mathbf{n}_i\}$. The IMLS method computes $f(\mathbf{x})$ by a **weighted average** of local contributions from each neighbor point:

$$
f(\mathbf{x})=\frac{1}{\sum_{j} w_{j}}\sum_{i} w_{i} \Bigl(\mathbf{x} - \mathbf{p}_{i}\Bigr)^{T}{\mathbf{n}_i}.
$$


### Weight Function

To capture local influence, each point $\mathbf{p}_i$ contributes a weight $\mathbf{w}_i$. You should use a Gaussian‐like kernel:

$$
w_i=\frac{1}{k_i} \exp\Bigl(-\tfrac{\|\mathbf{x} - \mathbf{p}_i\|^2}{\epsilon^2}\Bigr),
$$

where

- $\epsilon$ is a *radius* parameter controlling the falloff of influence (sometimes referred to as the “ball radius”). We use 0.01.
- $k_i$ is the number of neighbor points within $\epsilon$ of $\mathbf{p}_{i}$.


### TODO

1. **Gather Neighbors**: For each query $\mathbf{x}$, identify all points $\mathbf{p}_i$ within a distance $\epsilon$. We provide the code for this.
2. **(TODO) Compute Weights**: Calculate each $w_i$ according to the chosen kernel. 
3. **(TODO) Accumulate**: Form the numerator and denominator as above, summing over the neighbor indices $i$.  
4. **(TODO) Evaluate**: The scalar value $f(\mathbf{x})$ is the ratio of these sums. This value can be interpreted as a signed distance or displacement from $\mathbf{x}$ to the surface, depending on how normals are defined.


## Task 2: Marching Squares for Contour Extraction

In this assignment, we use **Marching Squares** to convert a 2D scalar field into line segments that approximate the contour where the field equals a given iso‐value (often 0). Building on the code from **Cell 5**, we first organize a regular grid of scalar values into cells. Each cell has four corners; we check each corner’s sign (above or below the iso‐value) to determine a 4‐bit “case” from `0000` to `1111`. The lookup table `case_to_edges` specifies which edges in the cell are intersected by the contour. We then compute the exact intersection points by **linear interpolation** between the relevant corners, storing segments of the form “start point to end point” in `contour`. By repeating this process over all cells, we reconstruct a piecewise‐linear approximation to the complete contour.

### TODO
1. **Fill out `case_to_edges`**: to map each 4‐bit corner configuration to one or two edges for interpolation.

Please refer to `points_to_offset` and `edge_to_points` to check the indexing.

## Grading
You will receive a zero score if:
* you do not submit,
* your code is not executable in the Python environment we provided, or
* your code additionally imports some libraries, or
* you modify anycode outside of the section marked with `TODO` or use different hyperparameters that are supposed to be fixed as given.

**You only need to submit the `main.ipynb` file.**

Task 1 and Task 2 are worth 10 and 20 points each.

**Task1**
Top 90% error | Points
--- | ---
0.0125⬇️ | 10
0.0250⬇️ | 5
0.0250⬆️ | 0

**Task2**
Hausdorff Distance | Points
--- | ---
0.0350⬇️ | 10
0.1850⬇️ | 5
0.1850⬆️ | 0

Ratio of Degree 2 | Points
--- | ---
0.9600⬆️ | 10
0.9000⬆️ | 5
0.9000⬇️ | 0


#### Plagiarism in any form will also result in a zero score and will be reported to the university.
