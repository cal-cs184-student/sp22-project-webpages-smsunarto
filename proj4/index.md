---
layout: default
title: Project 4 - ClothSim
---

# Overview

In this project, we implemented a cloth simulation program that handles that is able to hande collision against sphere, plane, and itself. At the end, we also wrote many different kidn of shaders to render the simulation with.

The biggest challenge in this project for us is making sure that the formula we are using is correct. There is several long equations that we need to implemented and we often miss tiny details that takes hours to debug and get correctly.

# Part 1

In part 1, we created a data structure that becomes the representation of the cloth that includes its point masses and springs. We use a for loop to create the number of point masses according to the specified constants. We also implement two different orientation of the cloth: horizontal and vertical. The implementation for the horizontal is trivial, however for the vertical cloth wew ant to make sure to vary the z value with a certain offset so that we can simulate the physics correctly.

After generating the point amsses, we proceed to create different type of springs between the point masses: structural, bending, and shearing.

![part1](/img/part_1_1.png)
![part1](/img/part_1_2.png)
![part1](/img/part_1_3.png)
![part1](/img/part_1_4.png)

# Part 2

In part 2, we finally apply physics simulation to the point masses and spring that we have generated in the previous part. We start by calculating the total force that acts on a certain point mass. We implemented both the external acceleration that is trivially defined by F = ma and also the spring correction force that is implemented according to the Hooke's law formula. Once we have the force, we can calculate the position changes between time delta by using verlet integration through the timesteps. We also constraint our position update to a maximum to 110% to the length at rest to increase the physics simulation realism.

We found that:

1. With higher spring constant, the cloth drapes less.

2. With higher density, the cloth drapes more.

3. With higher damping, the cloth drapes slower and springs up and down less after draping down.

Default

![part2](/img/part_2_1.png)

No damping

![part2](/img/part_2_2.png)

Low spring constant

![part2](/img/part_2_3.png)

Low density

![part2](/img/part_2_4.png)

# Part 3

In part 3, we implement the ability for the obejct to collide with another object. The high-level approach is fairly straight-forward: if a position is going to move through an object (colliding), we need to adjust the new poition to somewhere "above" the object. The way to calculate this varies betweeen a sphere and flat plane object.

ks = 5000, ks = 500, ks 50000. As ks increases, the cloth becomes more rigid and doesn't drape as much at the edge of the sphere.

![part3](/img/part_3_1.png)
![part3](/img/part_3_2.png)
![part3](/img/part_3_3.png)
![part3](/img/part_3_4.png)

# Part 4

In part 4, we implemented self collision so that the cloth can simulate the collision againt its own point masses instead of crashing through it. We do this by hashing the position of every point mass that allows us to trivially find "buckets" where collission might happen. We then iterate through the point masses in the bucket to chcek for collission and adjust the position accordingly.

Start

![part4](/img/part_4_1.png)

Initial self collission

![part4](/img/part_4_2.png)

End

![part4](/img/part_4_3.png)

The lower the densiy, the more the cloth drapes on the plane.
![part4](/img/part_4_4.png)

The higher the ks, the less the cloth drapes on the plane.
![part4](/img/part_4_5.png)

# Part 5

In part 5, we implement shaders that we can use to render our cloth simulation. We define the different shaders in .vert and .frag files. These shaders works by returning vector that provides information color and displacement information of a certain vertex after a transformation is applied from a texture and color persective. Vertex shader applies visible shape transformation on the vertex, while fragment shaders are focused on shading/color.

The Blinn-Phong shader is a shader that use a combination of different lighting effect like ambient lighting, diffuse lighting, and specular highlight to reach a realistic lighting shading effect.

![part4](/img/part_5_1.png)
![part4](/img/part_5_2.png)
![part4](/img/part_5_3.png)
![part4](/img/part_5_4.png)
![part4](/img/part_5_5.png)
![part4](/img/part_5_6.png)
![part4](/img/part_5_7.png)
![part4](/img/part_5_8.png)
![part4](/img/part_5_9.png)
![part4](/img/part_5_10.png)
![part4](/img/part_5_11.png)
![part4](/img/part_5_12.png)
