---
layout: default
title: Project 2 - Meshes
---

# Task 1

De Casteljau's algorithms is basically about lerping between a set of points repeatedly until we reach a single point, which can be placed on our curve. Thus, we implemented the recursive step of lerping until we reached a single point.

![task1](/img/p1-1.png)
![task1](/img/p1-2.png)
![task1](/img/p1-3.png)
![task1](/img/p1-4.png)
![task1](/img/p1-5.png)

# Task 2

For Bezier surfaces, we perform De Casteljau twice across two dimensions. We first perform it in the u direction, retrieve a curve, then for each point on the curve extend it into the v direction.

![task2](/img/p2.png)

# Task 3

To implement the area-weighted vertex normals, we used the built-in `normal()` function for simplicity. Once we had a normal, as long as we do proper bounds checks we can use the given "loop around a vertex" code snippet to calculate our values.

![task3](/img/p3-1.png)
![task3](/img/p3-2.png)

# Task 4

First, we check that all of the halfedges are not on the boundary. If it is on the boundary, we immediately return the original edge.

Beyond that, most of the heavy lifting in the implementation of the edge flip operation is in changing the pointer direction.

On a high-level, we want to first modify the neighbors of the halfedges, then we update the edge and vetices based on the new halfedges.

For us, it was relatively straightforward as long as we eyeball the parameters for the new halfedges neighbors correctly.

![task4](/img/p4-1.png)
![task4](/img/p4-2.png)

# Task 5

Implementing the edge split is similar to implementing edge flip but on a much more complex level.

First, we check that the edge is not on the boundary. If it is on the boundary, we immediately return the original vertex.

Then, we need to create a new vertex which will represent the midpoint. We call this `vertexM` which is located right in the middle of the center halfedges. Therefore, we calculate the position using `(vertexA0->position + vertexA1->position) / 2`

Beyond this, we simply need to create new faces, halfedges, and edges and rearrange the pointer accordingly.

One small detail that took the bulk of our time to debug is to make sure to set the new edges and vertex `isNew` to true where necessary.

![task5](/img/p5-1.png)
![task5](/img/p5-2.png)
![task5](/img/p5-3.png)

# Task 6

For Upsampling, first we want to iterate through the existing vertices and calculate their new positiona using the weighted average formula. We also make sure to set to mark the vertex `isNew` to false.

Afterwards, we iterate through all of the edges to calculate the new vertex position with the given formula. We then also make sure to mark the edge `isNew` to false.

Now, we can iterate through _ONLY_ the original edges and execute `splitEdge()` on it.

After the edge split, we make sure to flip the new edges that connects an old vertex with a newly created vertex.

Last but not least, we update the position of all the vertices based on what we have calculated earlier.

In the teapot example below, we can see that after each subsequent loop subdivision the object become more detailed with smaller triangles that makes it looks smoother.

![task6](/img/p6-1.png)
![task6](/img/p6-2.png)
![task6](/img/p6-3.png)

When we try to do subdivision on the cube, we can observe a weird behavior.
![task6](/img/p6-4.png)

In this picture, after several subdivision, we can see that the cube doesn't subdivide symmetrically. In the next image, we will try to fix by using some pre-processing.
![task6](/img/p6-5.png)

By splitting the edge at each side of the cube, we are able to achieve a more symmetrical subdivision.
![task6](/img/p6-6.png)

The reason why the subdivision is asymmetrical in the original cube is due to how the subdivision algorithm biases towards vertices with higher degree. As a result, there's an imbalance on how the subdivision is applied on low vs. high degree vertices. By splitting the edges, we make the degrees of the vertices the same and, as a result, it now has symmetric subdivision.
