---
layout: default
title: Project 1 - Rasterizer
---

### By: Scott Sunarto and Nishan D'Souza

# Overview

Our general approach for this project is to make sure that the earlier tasks works properly before continuing to the next task. This is important because most of the later tasks relies heavily on the earlier task being implemented correctly. By making sure the earlier task is correct first, we can isolate the debugging to only the task at hand instead of having to debug to the earlier tasks too.

One of the biggest problem that we faced in this project comes down to floating point error and/or rounding. There's a lot of subtle edge cases that needs to be taken into account. For instance, when calculating the barycentric coordinates, it turns out that we can't calculate gamma via `1 - a - b` due to a compiler specific floating point error.

# Section I: Rasterization

## Task 1

In task 1, we perform the rasterization by first iterating through an area of 2D space.

This area is bounded horizontally between min(x0, x1, x2) to max(x0, x1, x2) and vertically between min(y0, y1, y2) to max(y0, y1, y2).

For each pixel in this area, we check whether the middle (using + 0.5 offset both vertically and horizontally) of the pixel is inside the triangle. We determine this by deriving three line equations that allows us to check the position of the pixel relative to the line. We would then perform this for both the clockwise direction and the counter-clockwise direction as it's not guaranteed that the vertices of the triangles are supplied in a particular order.

If it's inside the triangle, we would then set the pixel to its corresponding color.

This method performs no worse than one that one that checks each sample within the bounding box of the triangle because:

- We bounded the area that we need to iterate through to at most the bounding box of the triangle.
- For each sample, we only perform one check: the center of the pixel.

![task1](/img/task1.png)

## Task 2

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

## Task 3

![task3](/img/task3-1.svg)
![task3](/img/task3-2.png)

Title: "Steve jobs waves to the crowd"

We made several modifications to the cubeman.

1. We removed the rotation on the head so it's no longer tilting
2. We rotated one of the arm by -70 degrees to emulate a resting position an made translation adjustments to make it connected to the torso.
3. We rotated the other arm by -10 degrees, rotated the lower part of that arm an additional -60 degrees, and then do translation adjustments for aesthetics.
4. We increase horizontally scaling of the legs to make it more proportional.

## Section II: Sampling

## Task 4

Barycentric coordinates is a coordinate system in which the position is measured in reference to a certain a set of points. In our casem between the three vertices of a triangle. They can be used to linearly interpolate values at the triangle's vertices and smoothly blend it across the surface.

For example, in this triangle, the bottom left vertex correspond to the color red, the bottom right vertex correspond to the color green, and the top vertex correspond to the color blue.

![task4](task4-1.jpeg)

Our implementation:

![task4](/img/task4-2.png)

## Task 5

Pixel sampling is a method that we can use to determine the texture that correspond to a specific pixel. We implemented this by first calculating the barycentric coordinates and then sampling the texels that corresponds to those coordinates.

Nearest sampling is the most straight-forward way of determining the corresponding texture to a specific pixel. It does this by choosing the texel that is _nearest_ to the center of the pixel. The trade-off here is that the texture would blend more sharply.

Bilinear sampling, on the other hand, takes more computation but is able to make texture blend smoother. It does this by calculating the linear interpolation of 4 adjacent texels that is nearest to the corresponding pixel.

![task5](/img/task5-1.png)
Nearest sampling - 1 sample/pixel

![task5](/img/task5-2.png)
Nearest sampling - 16 sample/pixel

![task5](/img/task5-3.png)
Bilinear sampling - 1 sample/pixel

![task5](/img/task5-4.png)
Bilinear sampling - 16 sample/pixel

We position the pixel inspector in a position where we can see both the bilinear sampling and supersampling in action. We can notice that the example that uses bilinear sampling have a much smoother blending in the texture in contrast to nearest sampling. Higher sample rate is especially noticeable at the edge of the image where it smoothen out the jaggies.

## Task 6

Level sampling is a method that allows us to use the resolution of the texture that correspond the most to the sampling rate of the screen. We implement this by building upon our implementation from Task 5. We first calculate two additional set of barycentric coordinates for `(x+1, y)` and `(x, y+1)`. We would then pass use these additional sets of barycentric coordinates to determine the corresponding mipmap level for each sample. The actual color that is assigned would then be determined based on the strategy of the level sampling which can be `L_NEAREST` that chooses the nearest level or `L_LINEAR` that uses the linear interpolation of two adjacent levels.

- Supersampling - Works well for antialiasing, more computationally intensive both in terms of run time and memory.
- Pixel Sampling - Less computationally intensive as supersampling, no additional storage overhead, bilinear filtering allows for smoother transition of the texture.
- Level Sampling - Requires additional storage space to store the mipmaps, works well for antialiasing with minification, but prone to overbluring.

![task6](/img/task6-1.png)
L_ZERO and P_NEAREST

![task6](/img/task6-2.png)
L_ZERO and P_LINEAR

![task6](/img/task6-3.png)
L_NEAREST and P_NEAREST

![task6](/img/task6-4.png)
L_NEAREST and P_LINEAR
