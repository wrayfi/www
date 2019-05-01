---
title: "Challenges"
date: 2019-04-30T22:39:17-07:00
draft: false
---
## Challenges:
* Initially, we aimed to model the router as the camera. However, the case of multiple routers was not well suited to this model.
* Instead we decided to have one camera above the scene looking down in a birds eye view.
* We place the devices around the scene and have the wifi router be the light source.
* We also pivoted from having a heat map of wifi signals as the final product to a prescription of where you should place your wifi routers based on the hot spots:) in your room, in terms of wifi usage.
* In order to show the strength of the wifi signal, we decided to include “cell phones” which represent these zones where you anticipate using wifi the most, that would help to determine the optimal place to put a router in a house.
    *  The cellphones are basically a material that absorbs the light waves and becomes brighter, or some other indicator,  as a result.
* One of the largest struggles was getting materials to render in Xcode after exporting them from blender. Once we figured it out, we were able to create a room with 4 doors representing different materials that interact with the wifi signals.
* We focused on modifying the ray and BSDF to accommodate different interactions with the wifi signal and surroundings
* Simulating accurate RF signal is quite hard and compute intensive, especially in a confined space where the rays interact with each other. (will Link some articles that uses inverse matrix on each pixel basically, that is quite compute intensive).
    * So we pivoted from this
