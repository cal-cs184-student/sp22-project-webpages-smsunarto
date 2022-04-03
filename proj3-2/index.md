---
layout: default
title: Project 3-2 - Lighting
---

# Overview

In this project, we implemented Part 1 (Mirror and Glass Materials) and Part 2 (Microfacet Material). Task 1 allows us to mirror and glass textured materials, while the part 2 allows us to render metallic object like gold and copper.

The biggest challenge in this project for us is:

1. Making sure that the formula we are using is correct. There is several long equations that we need to implemented and we often miss tiny details that takes hours to debug and get correctly, especially when having to render through a Hive machine (due to M1 chip incompatibility).

2. Working with a remote renderer setup using Hive machine due to incompatibility of the binary with M1 machine.

# Part 1

In part 1, we implemented the ability to render mirror and glass textured materials.

First and foremost, we implement the `reflection()` function that performs the BSDF calculation, which given a vector, it calculates the reflaction relative to the normal.

Then, we continue by implementing `MirrorBSDF::sample_f()` that returns `reflectance / abs_cos_theta(*wi)`.

We then implement the refraction function as defined by Snell's Law. Given a vector, it will calculate a vector that is the result of the refraction.

Last but not least, we implement `GlassBSDF::sample_f()` that returns `transmittance / abs_cos_theta(*wi) / pow(eta, 2)`

<img width="800" alt="0 MR" src="https://user-images.githubusercontent.com/12012798/161325683-90d978d6-be05-496e-b2dd-f84331845b0a.png">

Max ray depth = 0

<img width="801" alt="1 MR" src="https://user-images.githubusercontent.com/12012798/161325707-8aa0637a-752f-4ccb-9c75-9e1ef5227447.png">

Max ray depth = 1

<img width="801" alt="2 MR" src="https://user-images.githubusercontent.com/12012798/161325715-305f3003-626b-4905-9854-541887bdcee6.png">

Max ray depth = 2

<img width="803" alt="3 MR" src="https://user-images.githubusercontent.com/12012798/161325723-899a1298-5fbf-4953-bd2c-ec04a4319261.png">

Max ray depth = 3

<img width="799" alt="4 MR" src="https://user-images.githubusercontent.com/12012798/161325729-8a700aee-8c0d-44a8-9472-4148209e9231.png">

Max ray depth = 4

<img width="803" alt="5 MR" src="https://user-images.githubusercontent.com/12012798/161325738-530a90c9-25fa-4779-838a-8f8002bdb021.png">

Max ray depth = 5

<img width="802" alt="100 MR" src="https://user-images.githubusercontent.com/12012798/161325743-3fab82c3-dee1-4309-8544-9fafd2ad3937.png">

Max ray depth = 100

The most prominent new features that appear as we increase maximum ray depth are the images that appear as reflections/refractions of light inside the glass spheres. In the one on the left, we see a mirror image of the room in front of the sphere, including the sphere on the right. For the right sphere, the image of the blue wall refracts into the sphere and we see a blob of blue.

# Part 2

In part 2, we implemented the ability to render metallic material like copper, gold, etc.

First and foremost, we implement `MicrofacetBSDF::f()` that will call upon the F function (Fresnel term), G (shadowing-masking term), and D (NDF) that have been provided or will we implement.

We then proceed by implementing the Normal Distribution Function, Fresnel Term, and Importance Sampling following the equations that is outlined in each task specification.

![part2](/img/part2-1.png)

α = 0.5

![part2](/img/part2-2.png)

α = 0.25

![part2](/img/part2-3.png)

α = 0.05

![part2](/img/part2-4.png)

α = 0.005

On these dragon renders, we can see that as the alpha decreases the dragon looks increasingly glossy and vice versa.

![part2](/img/part2-5.png)

Rendered using importance sampling

![part2](/img/part2-6.png)

Rendered using cosine hemisphere sampling

We can see that the picture rendering with cosine hemisphere sampling is extremely noisy in contrast to the importance sampling even though the same amount of sampling is used.

![part2](/img/part2-7.png)

An _aluminium_ dragon

We changed the `eta` and `k` value in the `.dae` file to get a new material!
