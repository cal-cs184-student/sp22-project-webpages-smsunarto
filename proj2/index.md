---
layout: default
title: Project 2 - Meshes
---

# Task 1

De Casteljau's algorithms is basically about lerping between a set of points repeatedly until we reach a single point, which can be placed on our curve. Thus, I baseically had to implement the recursive step of lerping until we reached a single point. 

![task1](/img/p1-1.png)
![task1](/img/p1-2.png)
![task1](/img/p1-3.png)
![task1](/img/p1-4.png)
![task1](/img/p1-5.png)

# Task 2

For Bezier surfaces, we basically do De Casteljau across two dimensions, so twice. We first perform it in the u direction , retrieve a curve, then for each point on the curve extend it into the v direction.

![task2](/img/p2.png)

# Task 3

I decided to end up using the built in normal() function for simplicity - once we had a normal, as long as we do proper bounds checks we can continue the given "loop around a vertex" code to come up with our value.

![task3](/img/p3-1.png)
![task3](/img/p3-2.png)


# Task 4

# Task 5

# Task 6
