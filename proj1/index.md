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

![task1](/img/task1.png)

# Task 2

In task 2, we implemented the supersampling algortithm. To do this, we need to make several changes in our rasterization piple.

First and foremost, during the rasterization of the triangle, we added 2 additional loops to subsample horizontally and vertically for each pixel. Instead of checking only once per pixel, the number of sampling in supersampling changes with the sampling rate. As a result, the offset for each subsample differ based on the sampling rate. That said, it takes a subsample evenly on a pixel.

For each subsample, we do the same check as task 1 to determine whether it's inside a triangle. If it's inside the triangle, we would set it to the corresponding color. The interesting part here is with regards to how we store the image in the scaled sample buffer.

In our case, we perform it using the following structure:

```
sample_buffer[(y * N + j) * (width * N) + (x * N + i)]
```

We adjusted `fill_pixel` to fill the scaled samper buffer using the same data structure to also make sure that points and lines draws correctly. On top of that, we also adjusted `set_sample_rate` and `set_framebuffer_target` to resize the sample buffer accordingly based on the sample rate.

Last but not least, we modify the `resolve_to_framebuffer` function to make sure it works with our new data structure. Additionally, when supersampling is active, we would calculate the average color of the subsamples before setting the color to the frame buffer.

Supersampling helps in antialiasing the triangle by blurring the sharp edges or jaggies that is normally visible without supersampling.

![task2](/img/task2-1.png)
![task2](/img/task2-2.png)
![task2](/img/task2-3.png)
![task2](/img/task2-4.png)

# Task 3

![task3](/img/task3-1.svg)
![task3](/img/task3-2.svg)

Title: "Steve jobs waves to the crowd"

We made several modifications to the cubeman.

1. We removed the rotation on the head so it's no longer tilting
2. We rotated one of the arm by -70 degrees to emulate a resting position an made translation adjustments to make it connected to the torso.
3. We rotated the other arm by -10 degrees, rotated the lower part of that arm an additional -60 degrees, and then do translation adjustments for aesthetics.

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
