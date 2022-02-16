---
layout: default
title: Project 1 - Rasterizer
---

# Task 1

In task 1, we perform the rasterization by first iterating through an area of 2D space.

This area is bounded horizontally between min(x0, x1, x2) to max(x0, x1, x2) and vertically between min(y0, y1, y2) to max(y0, y1, y2).

For each pixel in this area, we check whether the middle (using + 0.5 offset both vertically and horizontally) of the pixel is inside the triangle. We determine this by deriving three line equations that allows us to check the position of the pixel relative to the line. We would then perform this for both the clockwise direction and the counter-clockwise direction as it's not guaranteed that the vertices of the triangles are supplied in a particular order.

If it's inside the triangle, we would then set the pixel to its corresponding color.

This method performs no worse than one that one that checks each sample within the bounding box of the triangle because:

- We bounded the area that we need to iterate through to at most the bounding box of the triangle.
- For each sample, we only perform one check: the center of the pixel.

# Task 2

# Task 3

# Task 4

Barycentric coordinates are a way of coordinating within a set of points, used in our case between the three vertices of a triangle. They can be used to smoothly plot within a triangle to map texture, color, etc.
Ex.

![ct](https://user-images.githubusercontent.com/12012798/154198080-0083fee1-ade0-4e4d-b1b0-7a1c8d3f7337.jpeg)

Our implementation:

<img width="801" alt="Screen Shot 2022-02-15 at 8 50 41 PM" src="https://user-images.githubusercontent.com/12012798/154198371-4f499dd7-ea91-49ee-9f2a-6d84f39579da.png">

# Task 5

Pixel sampling refers to the method by which pixel values are chosen for various graphical uses, from color to, in this case, texture. Nearest PSM is a basic method that takes the nearest point on the texture map to our sampled point. Bilinear interpolates several points to create a more blended, combined view of a texture.

<img width="801" alt="Screen Shot 2022-02-15 at 7 30 35 PM" src="https://user-images.githubusercontent.com/12012798/154191264-48157022-c0ea-4e22-9ddc-300f382ace5c.png">
<img width="799" alt="Screen Shot 2022-02-15 at 7 30 40 PM" src="https://user-images.githubusercontent.com/12012798/154191279-42b259b7-1fe8-4c1b-b8c6-f0c49b0da4ac.png">

This example between 16 spp for bilinear vs. nearest most demonstrates their difference. Bilinear smoothly blends between the colors of the texture, while nearest cuts sharp edges.

# Task 6
