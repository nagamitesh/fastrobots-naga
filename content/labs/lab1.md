+++
title = "Lab 1: The Artemis Board and Bluetooth"
description = "Learning how to work with the Artemis board and its bluetooth connectivity"
weight = 1
date = 2026-01-28

[extra]
local_image = ""
tags = ["artemis", "bluetooth", "ardunio", "sparkfun"]
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

# Lab 1A
## Prelab
I downloaded Arduino IDE and installed all the relevant libraries and boards. 

## Lab Tasks
I hooked up the Artemis board to my computer and made sure that the board was detected and I ran through the example files as defined in the lab instructions. I will outline the "results" of running my exmaple files below.

### Blink
Running the example Blink file, video below of my board executing the blink example.
<iframe src="https://drive.google.com/file/d/1Mxs7RPfhoyrULo3fbCpnBBGuhU0VWE7Q/preview" width="480" height="640"></iframe>

### Serial
Running the example serial file, and the image below shows the serial output of the example code.
{{image(path = "public\images\Screenshot 2026-01-21 092055.png",src = "public\images\Screenshot 2026-01-21 092055.png", alt = "Serial Example Output")}}

### Analog Read
Here I am running the Analog Read example, and during the test I'm putting my finger on the CPU which is showing an increase in the temperature counts due to the increase in the temperature that the CPU is seeing.
<iframe src="https://drive.google.com/file/d/1DDWc-986k-VB7W_rBZZx1MFQy3Q8QvtS/preview" width="900" height = "480"></iframe>

### Microphone Output
In this section I ran the microphone output example code from the arduino library and I tested both modulating the pitch of my voice and whistling to make the frequency increase. I've included a video below of my serial monitor output. 
<iframe src="https://drive.google.com/file/d/17curwIvX4QCcRgY2-S7FL8w9RXgfPS8n/preview" width="900" height="360"></iframe>

# Lab 1B

## Prelab

### Summary of BLE
I read through the linked page from Arduino about BLE and the main thing that I learned is that BLE is more of a passive system that has peripheral devices just display all the characteristics that they need to display, and the "central device" can read them if it chooses too. The main types of interaction is reading, writing, notifiying, and indicating. The size of each characteristic is also small so not much data can be written to each one, and GATT defines what functions each device can have and what it acts as.

### Setup
For python I chose to go with my existing instalation of 3.13.5 that I use through anaconda, and for the set up steps I had no issues with compatability or the like. The only trouble that I ran into was that I had to use my admin account to install virtualenv but other than that I was able to do the rest of the steps straight forwardly. I was also able to set up my virtual environment and install all the packages, but when using juypter server later I did notice that my packages were only accessible through juypter lab and not general VS Code because I wasn't able to figure out how to have VS Code compile/run using the juypter lab python. This might be something I look into later if I have the time. 

Going through the rest of the setup, I first got the MAC address from my arduino using the ble_arduino.ino file from the codebase. The MAC adress is screenshotted below. 
{{image(width = 500, path = "public\images\Screenshot 2026-01-28 095920.png",src = "public\images\Screenshot 2026-01-28 095920.png", alt = "MAC Address")}}

### Codebase
I was able to cleanly unzip the lab codebase into my project directory. From reading through the code base I first found that it is essentially split into 2 different sections, one for the arduino and one for the computer on python. For the arduino the main piece of code is an .ino file that sets up BLE and allows for characteristics to be initialized. This .ino also provides the basic functionalities of being able to read data, write data, and also listen for any connections. Then reading through the python part of the code base, specifically the ble.py and demo.ipynb I found that the python code base is used for intializing the connection to the Artemis and sending instructions to the Artemis, and it has useful functions to scan, send commands, and recieve data from the Artemis, and since this is in python this will also allow for easy parsing and manipulation of the data.  

## Configuration
First I updated the MAC address in connections.yaml to the MAC address I got from the prelab as follows. 
```yaml
artemis_address: 'c0:7:41:91:a5:44'
```
Next I used the code given in a python notebook to generate a UUID and placed that UUID in the connections.yaml file and ble_arduino.ino file as follows respectively. 
```yaml
ble_service: '34d66db7-fb82-4a1b-a60a-923a2442615d'
```
```cpp
#define BLE_UUID_TEST_SERVICE "34d66db7-fb82-4a1b-a60a-923a2442615d"
```
I double checked that the UUIDs match and that the CommandTypes match between both the python and the arduino libraries as well. I then ran through the demo.ipynb, I first ran into an issue where none of the cells were working due to library and connection errors, but with TA help I was able to find that the issue was because I didn't scroll high enough on demo.ipynb and didn't run the intialization cells to set up the connection, and I need to ensure to do that in the future. Below are images showing my results.

Successful Connection shown below.           
{{image(width = 900, path = "public\images\Screenshot 2026-02-04 051911.png",src = "public\images\Screenshot 2026-02-04 051911.png", alt = "Successful Connection")}}

Demo Python commands sent and the responses recieved below. 
{{image(width = 900, path = "public\images\Screenshot 2026-01-28 102941.png",src = "public\images\Screenshot 2026-01-28 102941.png", alt = "Demo Python Connections")}}

Demo code sent into Artemis from python and the response given by arduino.
```python
ble.send_command(CMD.SEND_TWO_INTS, "2|-6")
```
{{image(width = 900, path = "public\images\Screenshot 2026-01-28 103018.png",src = "public\images\Screenshot 2026-01-28 103018.png", alt = "2 Ints Demo")}}

All demo tasks ran successfully. 

## Tasks
For the python code here, I used the same initializion sequence of library imports and connection commands as the demo.ipynb.
### Echo
In this task I wrote code to send a string value from the python script to the Artemis using the ECHO command that is given as part of the code base. I also coded the Artemis to append the recieved string to a phrase and then return that new string. Then on the python side I used the recieve string command to print out the string that is recieved.

My python code is below
```python
ble.send_command(CMD.ECHO, "Good Morning Pineapple")
str_recieved = ble.receive_string(ble.uuid['RX_STRING'])
print(str_recieved)
```
For the Artemis, to the ble_arduino.ino file I added the following code for the ECHO case to recieve the transmitted string and append a predefined string to it.

```cpp
case ECHO:

            char char_arr[MAX_MSG_SIZE];

            // Extract the next value from the command string as a character array
            success = robot_cmd.get_next_value(char_arr);
            if (!success)
                return;

            tx_estring_value.clear();
            tx_estring_value.append("NagaBot says ->");
            tx_estring_value.append(char_arr);
            tx_estring_value.append(" :)");
            tx_characteristic_string.writeValue(tx_estring_value.c_str());
            
            break;
```
The output back to the computer is following.
{{image(width = 900, path = "public\images\Screenshot 2026-02-04 065227.png",src = "public\images\Screenshot 2026-02-04 065227.png", alt = "Echo Output")}}

### Get 3 Floats
First for the python script sending command containing the SEND_THREE_FLOATS command and adding 3 floats delineated by the straight line character. I am using a very similar set up to the SEND_TWO_INTS command given as part of the code base, and essentially just switching out the integers for floats. This means that I am intializing 3 floats, and then using the get next value command to get next value to store in the floats. 
My python code is below
```python
ble.send_command(CMD.ECHO, "Good Morning Pineapple")
```
For the Artemis, to the ble_arduino.ino file I added the following code.

```cpp
case SEND_THREE_FLOATS:
    float flt_a, flt_b, flt_c;

    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_a);
    if (!success)
        return;

    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_b);
    if (!success)
        return;

    // Extract the next value from the command string as an float
    success = robot_cmd.get_next_value(flt_c);
    if (!success)
        return;

    Serial.print("Three Floats: ");
    Serial.print(flt_a);
    Serial.print(", ");
    Serial.print(flt_b);
    Serial.print(", ");
    Serial.print(flt_c);

    break;
```
The output from the Artemis was as following.
{{image(width = 500, path = "public\images\Screenshot 2026-02-04 064815.png",src = "public\images\Screenshot 2026-02-04 064815.png", alt = "3 Floats Output")}}

### Get Time Millis
Here I added a new command GET_TIME_MILLIS to the ble_arduino (I added as a new case) and cmd_types.py scripts. For the arduino script I used the millis() command to get the current number of milliseconds and append that to a T: string in the characteristic. On the python side, I passed a blank string with the GET_TIME_MILLIS command and printed out the string that was sent back to the python script. I also ran into an error where I first didn't add GET_TIME_MILLIS into the enum command types at the top of ble_arduino, but after that my new command worked. I also had a scoping issue, I am not sure why, but adding curly brackets around my GET_TIME_MILLIS function solved that problem. Also learned that when adding new commands need to reimport CMD so the new commands show up in python. 

My python code is below
```python
ble.send_command(CMD.GET_TIME_MILLIS, "")
str_recieved = ble.receive_string(ble.uuid['RX_STRING'])
print(str_recieved)
```
For the Artemis, to the ble_arduino.ino file I added the following code for the new case, and also added GET_TIME_MILLIS to the command types near the top of the file.

```cpp
case GET_TIME_MILLIS: {
    long currtime;
    currtime = millis();
    String timestr = String(currtime);

    tx_estring_value.clear();
    tx_estring_value.append("T: ");
    tx_estring_value.append(timestr.c_str());
    tx_characteristic_string.writeValue(tx_estring_value.c_str());

    break;
}
```
The output back to the computer is following.
{{image(width = 700, path = "public\images\Screenshot 2026-02-04 072140.png",src = "public\images\Screenshot 2026-02-04 072140.png", alt = "Get Time Millis Output")}}


# Discussion
I think the biggest thing that I took away from the lab was how many different components go into making all of the devices I use daily talk to eachother, even making the Artemis talk in small chunks to my computer was hard, I can't imagine the amount of work it would take to make something like bluetooth headphones work. I think the thing that still stumps me and a big challenge is the scope issue I had on task 3 for 1B, I'm not sure why that arose, and I'm curious to figure out a fix for that. I overall learned how to communicate across python and the Artemis with bluetooth, how to add my own commands, and how to set up my environment for future development.

# Collaboration
I referenced Trevor Dales and Lucca Correia's websites for issues I faced in Lab 1B. I used Google Gemini to help me understand compile errors, and to trouble shoot image addition to my website. I used Daria's guide on Zola to create the website.