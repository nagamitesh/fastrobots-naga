+++
title = "Lab 3: Time of Flight Sensor"
description = "Learning how to use a Time of Flight Sensor and Configure It"
weight = 3
date = 2026-02-11

[extra]
local_image = ""
tags = ["artemis", "bluetooth", "time of flight", "sensors"]
+++
<style>
h1 {
    font-size: 1.75em;
}

h2 {
    font-size: 1.5em;
}

h3 {
    font-size: 1.25em;
}
</style>

# Prelab
## Documentation Reading
Through reading the documentation I found that the ToF sensor is able to have 3 different ranges of detection which I thought was really cool, and each of the ranges give a different combination of how far it can measure and how accurate it can be in that range. I also learned that there is a calibration function to calibrate the ToF which would be really interesting to try out later. I also learned that the __default I2C address is 0x52__ through reading the data sheet.

## Concurrent or Non-Concurrent
For my robot I am choosing to address both of the ToF sensors concurrently using different addresses because I believe that it will make the programming more streamlined and also make it easier to rapidly collect data across the sensors without need to switch on and off and intialize the sensors every time. I think that even though the data throughput might decrease a little bit due to having to have multiple sensors reading at similar times, but I think that the cycle time saved by not having to turn sensors on and off would make up for that and overall increase the amount of data that is able to be collected. I will do this in the beginning of my program by shutting down one of the sensors using the XSHUT pin and then assigning a new address to the other sensor, that will then allow me to selectivley turn the first sensor back on and then following just command the sensors individually with their different addresses.

## Placement of the ToF Sensor
I chose to place one of my sensors in front of my car to avoid obstacles that my car will run into while driving straight and I also chose to place my other sensor on the side of my car to allow for avoidance of walls or for keeping a distance from a wall so that the robot wont drive into obstacles on the side. I also used a short wire on the side to allow for this and a long wire for the front. My vision of how I want to mount these components is shown here. This does mean that my car will miss obstacles that is on its left side and back.
{{image(path = "./images/IMG_8533_result2.JPG",src = "./images/IMG_8533_result2.JPG", alt = "Layout Of Sensors")}}

## Wiring
Since I chose to put a sensor in the front and the side I will be using the longer QWIIC wire to attach to the front ToF sensor and the short QWIIC wire to attach the side one since it only has to go a shorter distance. Using the QWIIC wire also allows the sensors to be detached incase there needs to be a physical disconnect to set up the different sensors and also allows for easier troubleshooting as well. The connection between the ToF sensor and the wire can be permenant for this reason and I soldered the wires through the back so that they will not interfere with attaching the sensor to the robot. I did not cut the wires short and preserved as much of the length as possible so that I could keep the flexibility of slightly changing position later if needed. My wiring diagram is below, and I followed the color convention of the QWIIC wire with the ToF sensor labelled pins.
{{image(path = "./images/IMG_1931.PNG",src = "./images/IMG_1931.PNG", alt = "Wiring Diagram")}}
 

# Lab Tasks
## Soldering the Battery Connector
I soldered a new connector to the LiPO to allow for connection to the Artemis, picture of that connection is below.
{{image(path = "./images/IMG_8531_result.JPG",src = "./images/IMG_8531_result.JPG", alt = "Layout Of Sensors")}}

## ToF Setup and Soldering
I downloaded the library, wired the breakout board, and also soldered the connectors to the ToF sensors based on my wiring diagram. Through the QWIIC documentation I found that the blue wire is for SDA and yellow is for SCL. Below are images of my completed sensors and wiring. 

{{image(path = "./images/IMG_8534_result.JPG",src = "./images/IMG_8534_result.JPG", alt = "Short Cable Sensor")}}
{{image(path = "./images/IMG_8535_result.JPG",src = "./images/IMG_8535_result.JPG", alt = "Long Cable Sensor")}}
{{image(path = "./images/IMG_9035_result.JPG",src = "./images/IMG_9035_result.JPG", alt = "Layout Of Sensors Soldered")}}

## ToF I2C
To find and test the I2C address of the sensor I connected just one sensor to the Artemis and used the recommended Wire_I2C example to find the I2C address of the ToF sensor. I found that the address was 
## Dealing with Double Sensor Problem
Based on the Artemis pinouts, I found that I need a GPIO pin that can control high and low to operate the XSHUT so that I can decide which sensor to assign a different address too, and chose pin 8 since it didn't have a secondary function and was not an ADC either. I decided to solder the XSHUT pin on the right sensor since it has a shorter cable thus shorter run of wire needed.

Here is the code I used to intialize the sensors seperately by assigning a new value to them.


# Discussion
I think the biggest thing that I took away from the lab was how many different components go into making all of the devices I use daily talk to eachother, even making the Artemis talk in small chunks to my computer was hard, I can't imagine the amount of work it would take to make something like bluetooth headphones work. I think the thing that still stumps me and a big challenge is the scope issue I had on task 3 for 1B, I'm not sure why that arose, and I'm curious to figure out a fix for that. I overall learned how to communicate across python and the Artemis with bluetooth, how to add my own commands, and how to set up my environment for future development.

# Collaboration
I referenced Trevor Dales and Lucca Correia's websites for issues I faced in Lab 3, mainly with figuring out how to control 2 different ToF sensors at the same time.