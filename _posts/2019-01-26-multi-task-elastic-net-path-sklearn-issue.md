---
title: 'Sensitivity of warm-start in computing MultitaskElasticNet path by coordinate descent in Sklearn'
date: 2019-01-26
excerpt: 'We found warm-start strategy can be critical for computing MultitaskElasticNet path using the coordinate descent implemented in Sklearn.'
permalink: /posts/2018/09/blog-post-sensitivity-multi-task-elastic-net-warm-starts/
tags:
  - regularized linear model
  - Sklearn
---

## Abstract

This post describes a phenomena that we encounter in computing MultitaskElasticNet path, i.e., computing the coefficients of MultitaskElasticNet model with sweeping sparsity regularization parameter $\alpha$. We solve this problem by manually setting up the warm starts and everything works as expected.

## What kind of problem does MultiTaskElasticNet solve?

### Linear regression

Let's begin with a standard linear regression problem, with $N$ as number of data points, $M$ as the dimension of the data,  $P$ as the number of features used. To find the $W$ with least square of the residuals, we simply solve the following problem,   
$$
\min \lVert Y - XW \rVert^2_{F},
$$
where $X \in \mathbb{R}^{N \times P}$ are features, $Y \in \mathbb{R}^{N \times M}$ are  targets, $W \in \mathbb{R}^{P \times M}$ are model coefficients.

### Unique solution is preferred in modeling scientifc problem  

In general, the above problem can be viewed as a linear inversion problem especially if the problem is ill-condition either due to lack of observations or the heavily correlated features. Thus simply solving the above least square problem won't give us unique solution. However, most of the ground truth behind a inversion problem in scientic community is unique. So what can we do? 

A standard 

If we only have very few data, certainly it can become an underdetermined system so we don't have a unique solution. However, if the features are statistically correlated, we cannot obtain a unique solution, even if we have a lot of data. 






$$
\begin{equation}
\frac{1}{N}  \lVert Y - XW \rVert_{F}^2
+ \alpha  c   \lVert W  \rVert_{21}
+ 0.5 \alpha (1 - c)  \lVert W \rVert_{F}^2
\end{equation}
$$


## Using pip to install vtkInterface

```bash
sudo pip install vtkInterface 
```

## Tutorial: 2D Flow past cylinder

The material can be obtained from [Wolf Dynamics](http://www.wolfdynamics.com) at this [link](http://www.wolfdynamics.com/images/begtuts/vortex_shedding.tar.gz). 

### Prepare data

1. `untar` the **.tar** file

   ```bash
   tar -zxvf vortex_shedding.tar.gz ./
   ```

2. go to `c1` directory for running a standard case

   ```bash
   cd c1
   blockMesh
   checkMesh
   icoFoam > log &
   ```

### Convert OpenFoam default format to VTK

```bash
foamToVTK
```

### Using Python to manipulate VTK data

```python
import vtkInterface as vtki
import numpy as np

## grid is the central object in VTK where every field is added on to grid
grid = vtki.UnstructuredGrid('./VTK/c1_1000.vtk')

## point-wise information of geometry is contained
print grid.points

## get a dictionary contains all cell/point information
print grid.cell_arrays # note that cell-based and point-based are in different size
print grid.point_arrays # 

## get a field in numpy array
p_cell = grid.cell_arrays['p']

## create a new cell field of pressure^2
p2_cell = p_cell**2
grid.AddCellScalars(p2_cell, 'p2')

## remember to save the modified vtk
grid.Write('./VTK/c1_1000_shaowu.vtk')
```

### Visualize the new field in ParaView

```bash
paraview  
```


![Screen shot](/images/blog-10-20-save.png)
