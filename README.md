# Isosurface Experiments with Unity, public source
### Completeness Disclaimer
I'm not ready to release the complete source code for this project to the public yet, given the problem with plagiarism on the internet.  
Therefore while this repo is mostly complete, I have left out some important parts in order to prevent someone else copy-pasting my work as their own.  
*TODO: put picture of spun rough cube here*
## Summary
This is a hobby project where I explored the idea of isosurface visualization in 3d and its potential application to a game or other interactive 3d program. I didn't have anything particular in mind, I was just curious about *Marching Cubes* and similar visualizations and wanted to try implementing something like that. 
An isosurface is like the manifold which traces out some threshold value in a scalar field. For example a topographic map has elevation lines which are iso*lines* in a 2d scalar field (elevation). In 3d applications an *isosurface* usually means a triangle mesh of some weird shape or planetary surface.  
  
The method I implemented for isosurface triangularization is the [Dual Contouring (2001)](https://www.cs.rice.edu/~jwarren/papers/dualcontour.pdf) method. I didn't follow their paper exactly, for example I found a more numerically stable solution to vertex location, and my implementation does not use adaptive octree subdivision because in this case the field is not a distance field; but overall, their paper is the key to this project.

In my case the field is represented as a locally differentiable scalar-valued function in 3d. The field is sampled along the points of a regular grid in 3d. For each grid edge where an isosurface intersection is detected, the position along the edge is iteratively refined and then a gradient is measured at that point. Using the main idea of the Dual Contouring method, all the isosurface intersections of one grid cell are used to project a vertex within that cell, which becomes one of the vertices of the triangle mesh of the isosurface.
