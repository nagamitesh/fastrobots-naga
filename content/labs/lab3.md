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
To find and test the I2C address of the sensor I connected just one sensor to the Artemis and used the recommended Wire_I2C example to find the I2C address of the ToF sensor. I found that the address was 0x29. This is different than the 0x52 and I noted that 0x29 becomes 0010 1001 in 8 bit binary and 0x52 becomes 0101 0010. I also noticed that in the wire code the for loop to execute the serial print only activates if the address is under 127 which is a 7 bit number, so I believe that by default an I2C address is only supposed to be a 7 bit number such as what 0x29 is and the last bit is used for read or write designation as is explained in the data sheet, so since here we are reading from the sensor the I2C address is left shifted and a 0 is appended, which gives 0x52 in binary since 0010 1001 (0x29) left-shifted is 0101 0010 (0x52) which is what is detailed in the ToF manual. Thus this information corroborates the manual's address.

{{image(path = "./images/Screenshot 2026-02-25 031831.png",src = "./images/Screenshot 2026-02-25 031831.png", alt = "I2C Address")}}

## Sensor Mode
Here the main modes I considered were the Short and Long modes since Long and Middle modes were very similar in terms of performance in different lighting and only had a 1 meter range difference. The data sheet and manual did not have much description between the Short and Long modes except for showing its performance in dark and light as well as distance. I saw that the Long mode had a very high sensitivity to light and especially bright light made it lose its ranging capabilities really fast and decreased its maximum range to half of the range of the short mode in the bright light environment, essentially nullifying the biggest pro of the Long mode which was high range. Since our lab is pretty brightly lit I think that the Short mode will give me the best measurements and most repeatable measurments regardless of environmental conditions so I will be using the Short mode. I also feel the Short mode range of 1.3 meters will be adequate to navigate around any obstacles faced by the robot as well.

## Sensor Measurement Analysis
I used the sensor sample code to get a number of measurements to test the sensor.

### Ranging
To test ranging I set up a testing set up as below and screen recorded as I moved the flat keyboard up in 6 inch increments (I took the protective film off before doing my final test). My setup wasn't perfectly precise but it was good enough to get a rough estimate of accuracy and I found that within the 4 foot range the sensor is very accurate in a evenly bright lit area. Within 2 feet the accuracy was to the 0.01 of a foot pretty much but after the 2 feet range it dropped to within 0.05 foot and beyond 4 feet it was only really accurate to the 0.1 foot which is still pretty good but something to take note off. Setup and video of the results below, jump in distances in the video is the 6 inch move.
{{image(path = "./images/IMG_9055.JPG",src = "./images/IMG_9055.JPG", alt = "Ranging Setup")}}
<iframe src="https://drive.google.com/file/d/1nIApVm0g3U0W34_Smm0yHFEook8Grc8U/preview" width="900" height="240"></iframe>

### Accuracy and Repeatability
I used my setup to accurately measure 6 inches and left my sensor to keep reading and I copied 150 of the measurement results in inches into python to plot it using matplotlib and below is my data. I had to change my output in the arduino script to only output the inch value and no text to make it easy to parse.
{{image(path = "./images/Screenshot 2026-02-25 045644.png",src = "./images/Screenshot 2026-02-25 045644.png", alt = "Frequency Plot of Measurments")}}
Based on the plot I found that the repeatability is generally within 0.1 of an inch even if the object is not moving at all and generally the measurements are pretty close to the expected measurement. I also found that the sensor reads in bins of 0.04 inches and that is something to keep in mind if I see repeated measurements in the future as well. I think that this is due to the conversion between the millimeter reading to the inches that are being displayed. 

### Ranging Time

## Dealing with Double Sensor Problem
Based on the Artemis pinouts, I found that I need a GPIO pin that can control high and low to operate the XSHUT so that I can decide which sensor to assign a different address too, and chose pin 8 since it didn't have a secondary function and was not an ADC either. I decided to solder the XSHUT pin on the right sensor since it has a shorter cable thus shorter run of wire needed.

Here is the code I used to intialize the sensors seperately by assigning a new value to them.


# Discussion
One great learning was that taking the protective film off really does improve the measurements lol, and after taking the film off my measurements improved a lot.

# Collaboration
I referenced Trevor Dales and Lucca Correia's websites for issues I faced in Lab 3, mainly with figuring out how to control 2 different ToF sensors at the same time. I also used Google Gemini to help with making the code to plot the ToF results.