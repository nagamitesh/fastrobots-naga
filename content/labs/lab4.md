+++
title = "Lab 4: Motor Drivers"
description = "Learning how to use a motor driver"
weight = 4
date = 2026-02-25

[extra]
local_image = ""
tags = ["artemis", "bluetooth", "motors", "motor driver"]
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
I am going to use the guide provided by the TI datasheet to wire up my car. This is what I found in the documentation and will be my general wiring scheme. 
{{image(path = "./images/Screenshot 2026-02-25 091813.png",src = "./images/Screenshot 2026-02-25 091813.png", alt = "I2C Address")}}


I am ensuring all of my pins are PWM capable. I am finding if pins are PWM capable by looking at the schematic and I learned from Aidan that a tilde next to a pin means that it is PWM capable. I chose to use A0, A1, A2, and A3 to connect my motor drivers in because that works best with my orientation of my Artemis. 
{{image(path = "./images/Screenshot 2026-02-25 095913.png",src = "./images/Screenshot 2026-02-25 095913.png", alt = "I2C Address")}}

# Lab Tasks



# Discussion


# Collaboration
I referenced Trevor Dales and Lucca Correia's websites for issues I faced in Lab 3, mainly with figuring out how to control 2 different ToF sensors at the same time. I also used Google Gemini to help with making the code to plot the ToF results. I also worked with Aidan Chan to find pins for my board. 