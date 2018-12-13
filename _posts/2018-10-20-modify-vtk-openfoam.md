---
title: 'The ultimate way to postprocess OpenFoam data in Python'
date: 2018-09-27
excerpt: 'Getting industrial standard CFD format data into Python is not that easy as it seems to be. I used a toolkit library and everything works like charm.'
permalink: /posts/2018/09/blog-post-modify-vtk-openfoam/
tags:
  - visualization
  - vtk
  - openFoam
---

## Abstract

In this post, I use **foamToVTK** in [OpenFoam](https://www.openfoam.com/) to convert OpenFoam data into **legacy VTK [(The Visualization ToolKit)](https://www.vtk.org/Wiki/VTK))** format, then use **[vtkInterface](https://github.com/akaszynski/vtkInterface)** for data manipulation in **Python** under **Ubuntu**. 

## Introduction

[**OpenFoam**](https://www.openfoam.com/) is a popular open source code for **computational fluid dynamics** (CFD). Although it contains various helpful postpocessing modules in command line such as **postProcess**, it is still designed for convenience but not flexibility. For example, it only provides operations that are very common in the context of fluid mechanics or vector mathematics and it hides the details of operation such as the numerical scheme to approximate the derivatives. Most of the time, **OpenFoam** saves the data in folder named by the current time and in each folder contains a **special OpenFoam format-txt like** data, which is also designed for convenience such that one can directly read the result in the field data. However, if one wants to manipulate the data in a more flexible sense in the modern data-driven era, a Python-script-driven manipulation of data is extremely favorable. Also, to avoid dealing with the mesh in the script, if would be great if one can simply add the modified field on the original mesh. Fortunately, with the help of an awesome Python package on **Github**: **[vtkInterface](https://github.com/akaszynski/vtkInterface)** , by **[Alex Kaszynski](https://github.com/akaszynski)**, one can easily leverage the powerful libraries in Python environment to postprocessing traditional, mature, standard and specialized scientific computing data and immediately put them back in to again, leverage the existing powerful visualization software in scientific computing community. 

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
