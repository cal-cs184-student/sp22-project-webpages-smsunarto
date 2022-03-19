---
layout: default
title: Project 3-1 - Physically-based Renderer via Pathtracing Algo
---

### By: Scott Sunarto and Nishan D'Souza

# Overview

In this project we impleemnt a physically-based renderer that utilizes pathtracing algorithm. The implementation starts with the ray generation and intersection determination algorithm in part 1, proceeded with a bounding box implementation to accelerate the rendering process in part 2. We then move on the implementing the illumination calculation through both hempisphere and importance sampling direct illumination + global illumination in Part 3 and Part 4 respectively. Last but not least, we implement adaptive sampling to terminate the sampling for a pixel earlier if it has converged.

Our general approach for this project is to make sure that the earlier tasks works properly before continuing to the next task. This is important because most of the later tasks relies heavily on the earlier task being implemented correctly. By making sure the earlier task is correct first, we can isolate the debugging to only the task at hand instead of having to debug to the earlier tasks too.

The part that took us the most time was on Part 3. Ironically, this problem arose by not discplinely following the approach outlined in our previous paragraph. We found that there our Part 2's midpoint determination algorithm was fault and didn't realize that until we already implemented Part 3. We spent a lot of time thinking that the problem was in our Part 3 implementation when the source of the problem is in Part 2. After a lot of hours of debugging, we finally pinpoint the problem and was able to rectify the issue.

# Part 1

The ray generation is relatively straightforward. We want to create rays equal to the number of samples defined by `ns_aa`. We call the `generate_ray()` method of the camera and pass in the origin and direction of it. We then calculate the average of the radiance from all of the samples and update the corresponding pixel in the sample buffer.

We then proceed to implementing the triangle intersection algorith. First, we need to obtain the barycentric coordinates of the triangle. To do this, we can use the Moller-Trumbore algorithm to obtain `t` and 2 barycentric coordinates. Afterwards, we can can calculate third barycentric coordinate by subtracting 1 with the 2 barycentric coordinate that we know. Now, we check that `t` and is between `min_t` and `max_t` to see whether it intersect. If it pass the boundary check, we calculate `n` by `b1 * n1 + b2 * n2 + b3 * n3` and normalizing it. We also made sure to update `max_t` to the `t` that we obtain.

Last but not least, we want to the update the `isect` variable that is being passed by reference.

![part1](/img/part1-1.png)
![part1](/img/part1-2.png)
![part1](/img/part1-3.png)

# Part 2

To accelerate our rendering for more complex object, we want to construct a BVH. From the project spec, we know that the internal node should contain a non-null left and right children while the leaf node must have a defined `start` and `end` iterator that point to the primitive.

The implementatation first starts by defining the base case fo the recursion: when the number of primitives is smaller or equal to `max_leaf_size`, that node is a leaf node.

We then proceed with implementing the non-base case: when it's an internal node.

We first need to decide a way to calculate the splitpoint. We opt to the simplest implementation (in our opinion), that is to calculate the midpoint of the longest axis.

After a midpoint is determined, we use the `partition` function to split the primitives into left and right. If `coordinate <= midpoint`, then it goes to the left child, otherwise it goes to the right child.

We then recursively call `construct_bvh` on the left and right child, passing in the corresponding partitioned primitives that we have determined previously.

![part2](/img/part2-1.png)
![part2](/img/part2-2.png)
![part2](/img/part2-3.png)

Rendering these solutions with BVH accelration is almost instantaneous with an M1 Mac laptop with all of them seeing sub 1 second time. In contrast, without BVH acceleration, the time it takes to render can reach up to 30 seconds.

# Part 3

In part 3, we implement 2 different direct lighting function: sampled uniformly from a hemisphere and sampled directly from light via importance sampling.

**Uniform sampling across hemisphere**

We use a for loop to get the number of sampling equal to the `num_samples` variable.

Inside the for loop, first we obtain a sample from `hemisphereSampler` by calling the `get_sample` method. We then convert the sample into world coordinate, then using it to calculate the origin that is defined by `hit_p + (EPS_F * d)`. We use the `EPS_F`, the epsilon constant, here to prevent numerical precision error. After the origin and direction is determined, we generate a ray object. We then determine whether the ray intersets by using the `intersection` method of the bvh.

If it intersects, we can fetch the emission by calling `bsdf->get_emissions` method. Then we calculate the irradiance by multiplying the emission with the cosine of the sample vector and BSDF. We then add it to the `L_out` accumulator.

At the end of the function, we calculate the average of the sample and divide it by the probability density function.

**Importance sampling**

We use a for loop to iterate through all of the lights in the scene.

Inside the for loop, we first determine whether the light is a point light source. If it is, then only need to sample it once. Otherwise, we want to sample it `ns_area_light` times.

We then use a for loop to get the number of sampling equal to the amount of we have determined previously.

Inside the for loop, first we obtain a sample by calling the `light->sample_L` method. We then convert the the sample vector into object space.

If the `z` coordinate is bigger or equal to 0 (positive), we know that the light is visible and we want to handle it. Then,we calculate the origin of the ray that is defined by `hit_p + (EPS_F * d)`. We use the `EPS_F`, the epsilon constant, here to prevent numerical precision error. We then generate the array object by passing the origin and the direction, followed by setting the `max_t` to distance to light minus epsilon. We then determine whether the ray intersets by using the `intersection` method of the bvh.

If it intersects, we can fetch the emission by calling `bsdf->get_emissions` method. Then we calculate the irradiance by multiplying the emission with the cosine of the sample vector and BSDF, then we divide by the probability density function. We then add it to the `L_out` accumulator

At every iteration of the light object, we recalculate the average and at the end of the function, we return the accumulated irradiance from the lights of the scene.

_Both done with the same parameters `-t 8 -s 16 -l 8 -m 6`_
**Uniform sampling across hemisphere**
![part3](/img/part3-1h.png)

**Importance sampling**
![part3](/img/part3-1i.png)

We can easily tell that even with the same parameters, the result from the importance sampling is significantly less grainy than the result from our uniform sampling across the hemisphere.

**Importance sampling with 1, 4, 16, and 64 lights array (in that order)**
We can observe that as the number of light rays increases,the noise level in soft shadows decreases (less grainy).

![part3](/img/part3-2-1.png)
![part3](/img/part3-2-4.png)
![part3](/img/part3-2-16.png)
![part3](/img/part3-2-64.png)

# Part 4

We now proceed to implementing the indirect lighting function.

First and foremost, we call the `one_bounce_radiance` and add the value to the accumulator since we will always want to include the one bounce radiance.

Afterwards, we obtain a sample by calling the `bsdf->sample_f` method.

If the `max_ray_depth` is less or equal to 1, we can immediately terminate and return the one bounce radiance.

Otherwise, we continue based on a probability that we have defined, which is 0.7, if the coin flip returns true, we recursively call `at_least_one_bounce_radiance` and accumulate the value in `L_out`.

Unfortunately, we didn't have enough time render the images for this task.

# Part 5

By implementing adaptive sampling, we can terminate our sampling for a pixel once we know that the vaue have converged.

We implement this by adding the `illuminationAccumulator` and `illumination2Accumulator` in the raytrace_pixel function. Inside the for loop of our sampling, we accumulate the illumination and the illumination^2. At every batch (defined by user), we calculate the mean and variance; by using the mean and variance, we can use the defined formula to see whether the variance is within our defined maximum tolerance. If it is, we can terminate the sampling.

![part5](/img/bunny.png)
![part4](/img/bunny_rate.png)
