---
title: "Report"
date: 2019-04-30T22:39:17-07:00
draft: false
---

[Video](https://youtu.be/TxUFy5annY4)

## Abstract

We modeled radio frequency (RF) rays interacting with realistic materials so users can find the router placement that maximizes WiFi connection at the points where they use it most.  We started by modeling the space where a user would want to compute their wifi. We created a realistic office scene, complete with a couple rooms, desks, wifi-connected devices, and routers. To model RF ray tracing, we began with project 3-2 as a foundation. We decided to introduce two new abstractions to the project, a router and a hotspot. We implemented the router abstraction as a literal router object specified in the blender scene. It shoots RF rays into the scene uniformly at random in the sphere encompassing the router. We implemented hotspots as spheres centered around the device you place in the blender scene. When a ray from the router intersects with one of these special spheres, we record the phase and brightness. Once all the rays have been traced, for each hotspot, we aggregate all the rays which intersected by representing their contribution as `sine(phase) * brightness`. To create a more compelling graphic, we then render the scene from a birds eye view shooting rays from the camera like we did in 3-2 and overlay our computed wifi signal values onto the hotspots.

## Technical approach
### Pipeline

![Pipeline](../img/pipeline.png)

### Scene modeling (Real life -> .dae)

The `.dae` files we created in blender contained objects that pathtracer couldn’t read. After significant trial and error, we found that pathtracer relied heavily on simple objects such as planes and spheres and offered little support for other objects. We attempted to use materials that were commonly found in everyday life and that had different interaction with WiFi signals. After experimenting with different materials, we decided on glass, mirror, and concrete. We then imported compatible .dae files to fill the space. After we generated the .dae file, we manually changed variable names and imported material/ effects that were compatible with the XML parser.

#### Summary

We manually edited the .dae file exported from blender to fit the XML parser from pathtracer. We added custom objects, materials and their effects on the scene.

### Parsing (.dae -> raytracer)

We changed the collada parser in the project 3 to read in our special router and hotspot objects.

### Computing Wifi values

#### Decisions

* We had two major decisions to make to compute wifi values.
    * How to model routers, devices, and rays
        * Our initial approach was to create a heat map. For this, we landed on the approach of a birds eye view of the scene, routers as light sources, and devices weren’t necessary as you would see the wifi everywhere in the scene. One major drawback to this approach was the increase in complexity of figuring out ray interference and the fact that our image was limited to wifi rays that managed to intersect with our floating birds eye camera.
        * We opted for a more realistic, and doable approach of computing wifi signal at the location of devices in your scene.
            * Unfortunately, no meaningful picture is generated.
            * But, we could compute interference by aggregating all the rays that intersect with our devices.
            * This also captured every router ray that intersected with a hotspot.
    * What should go into calculating wifi signal and how to aggregate them.
        * We initially wanted to just include brightness.
            * Every render would produce a heatmap where the brighter points in the picture correspond to better reception.
        * We ultimately opted for brightness and phase.
            * This allowed us to capture interference effects where waves can amplify or cancel each other out.

#### Problems

We ran into two significant challenges in our implementation.

* Since we opted to use the staff binary for 3-2 and we needed to modify components of 3-1 to model RF, we had to start by integrating our 3-1 code in with our 3-2 code. Amir’s 3-1 didn’t mesh well with 3-2, but thankfully Quetza’s did so we integrated hers.
* A ton of the objects are marked as const in the 3-2 raytracer. Initially this manifested itself as errors that we couldn’t really understand. After learning how const works in C++, we understood what was going on, but it made it very difficult to figure out where to record the phase and brightness values for rays without refactoring major parts of 3-2. This ended up being a frustrating, yet interesting design problem.

#### Summary
With the router and hotspot objects loaded in from the `.dae`, all we had left was to trace the rays. We generated rays, uniformly at random in the sphere encompassing the router. We modified the BSDFs of each material to scale the rays brightness down by some factor we decided upon after researching the way RF rays interact with that material. In order to measure the reception, we kept track of how many rays intersect each hotspot by storing the state of the wave at a point in time (the phase, which is `time%frequency`(either `2.4 GHZ`or `5 GHZ`), and the amplitude (brightness)). We placed light sources under each router so we could use the brightness of the ray as a heuristic for the amplitude of the RF wave. After all the rays are generated, we go through each hotspot and combine these sinusoids using a weighted sum of the rays
 `sin(phase) * amplitude`. With this equation we were able to mimic phase interference and obtain our final value representing the total wifi signal at that hotspot.

### Lessons
`Amir` relearned sinusoidal math, the purpose of const in C++, and gained a much deeper understanding of the Project 3 codebase and ray tracing. He also enhanced his project management skills, coordinating different talents, and organizing the logistics of scheduling meetings with 3 busy college students.

`Quetza`

When creating and working in blender, it was really cool being able to have a deeper understanding of how the phong, ambient, specular, and emission all interacted after learning about them in project 4.

`Minos`

### Results
![DiffuseLeftRouter](../img/SphereHotSpot_DiffuseLeftRouter_screenshot_5-13_23-3-24.png)

![DiffuseRightRouter](../img/SphereHotSpot_DiffuseRightRouter_screenshot_5-13_22-5-30.png)

![MixedLeftRouter](../img/SphereHotSpot_MixedLeftRouterFix_screenshot_5-14_12-32-3.png)

![MixedRightRouter](../img/SphereHotSpot_MixedRightRouterFix_screenshot_5-14_13-19-1.png)

### References
* Reference projects:
    * [Router as camera Wifi trace](https://www.sciencealert.com/a-physicist-has-calculated-the-best-place-to-put-your-router)
    * [WIFI-Trace](https://github.com/SoleSensei/WiFi-Trace)
* Resources
    * [Project 3-2](https://cs184.eecs.berkeley.edu/sp19/article/26/assignment-3-2-pathtracer-2)
    * [Formal Physics approach](https://jasmcole.com/2014/08/25/helmhurts/#more-161)
    * [Adding Sines](https://dspguru.com/files/Sum_of_Two_Sinusoids.pdf)

### Contributions
`Quetza` learned how 3-2 ingest a scene, fixed compatibility issues between blender and 3-2’s parser, and created custom objects in blender to represent the router, hotspots
and a couple of materials: concrete, glass, mirror, diffuse.


`Amir` researched a method of combining rays contributions to capture RF interference. He also collaborated with Minos to implement the router/hotspot objects in the raytracer and aggregate the rays contributions using the researched aggregator function.

`Minos` collaborated with Amir to implement the router/hotspot objects in the raytracer and aggregate the rays contributions using the researched aggregator function. He also hooked up the blender router/hotspot objects to the XML parser to populate our C++ abstractions with the blender data.
