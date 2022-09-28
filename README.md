# Isosurface Experiments with Unity, public source
![image of spun rough cube](images/SpunRoughCube.png)
## Summary
This is a hobby project where I explored the idea of isosurface visualization in 3d and its potential application to a game or other interactive 3d program. I didn't have anything particular in mind, I was just curious about *Marching Cubes* and similar visualizations and wanted to try implementing something like that.
### Completeness Disclaimer
I'm not ready to release the complete source code for this project to the public yet, given the problem with plagiarism on the internet. Therefore while this repo is mostly complete, I have left out some important parts in order to prevent someone else copy-pasting my work as their own. This means that while you can download and view most of the code, you won't be able to load it into Unity and build it without filling in the gaps yourself.
### Isosurface
An isosurface is like the manifold which traces out some threshold value in a scalar field. For example a topographic map has elevation lines which are iso*lines* in a 2d scalar field (elevation). In 3d applications an *isosurface* usually means a triangle mesh of some weird shape or planetary surface.  
  
The field used to describe an isosurface can be conveniently defined through the composition of arbitrary math functions. For example a sphere can be defined as the field function:
>field(xyz) = radiusOfSphere - distance(xyz, centerOfSphere)
### Method
I based this project on the main idea from the [**Dual Contouring (2001)**](https://www.cs.rice.edu/~jwarren/papers/dualcontour.pdf) paper. I didn't follow their paper exactly, for example I found a more numerically stable solution to vertex location, and my implementation doesn't use adaptive octree subdivision because in this case the field is not a distance field; but overall, their paper is the key to this project.

Dual Contouring is similar to Marching Cubes but instead of placing vertices along grid cell edges, vertices are placed in grid cell interiors. This allows sharp edges / corners in the isosurface to be more accurately approximated, automatically and without having to handle special cases or ambiguities.  

In my case the field is represented as a locally differentiable scalar-valued function in 3d. The field is sampled along the points of a regular grid in 3d. For each grid edge where an isosurface intersection is detected (where the field value crosses zero), the position along the edge is iteratively refined and then a gradient is measured at that point. Using the main idea of the Dual Contouring method, all the isosurface intersections of one grid cell are used to project a vertex within that cell, which becomes one of the vertices of the triangle mesh of the isosurface.
### Unity
I initially considered Unreal Engine as the rendering front-end but ended up using Unity when I discovered that Unity Editor was less prone to crashing and other weird bugs and glitches. Unity's triangle rasterization performance is adequate but the severe performance limitations of Mono C# runtime and even IL2CPP's inadequate performance optimization forced me to eventually rewrite much of this project to use the Unity Burst Compiler, which is a subset of C# that gets compiled to native machine code at runtime by Unity's custom Burst Compiler. 
### Mesh Generation
This application attempts to approximate the isosurface within a large radius in all directions around the camera. The field is sampled at high resolution close-up and lower resolution far-away, such that the angular resolution of a mesh is about the same for near and for far portions of the isosurface.  
  
The isosurface meshes are not stored ahead of time, and are calculated entirely at program run-time. The world is dynamically divided into chunks and rendered into triangle meshes using multiple worker threads. The meshes are then rendered to the screen by Unity, which also takes care of texturing and lighting.
### Field Representation
In this application scalar fields are defined by composable C# classes representing usually simple math functions. For example one class might provide a scalar function to define a cuboid, or another class might scale the position input to some other function to in effect change the size of the object.
*TODO: put a picture of some simple field code here*
