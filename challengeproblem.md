---
layout: page
slide_id: 4
---

## Introduction:
We created a simulated environment that contains two rovers called `leader` and `follower`. Upon starting up the simulation, `leader` takes a random path while changing its speed continuously. In this challenging problem, you are required to use a MDD tool and implement a control model for `follower` such that it always follows `leader` while keeping a safe distance from it. Safe distance is defined by a range from maxDistance to minDistance which can be configured.  Anytime that `follower` is out of safe distance is considered as an unsafe condition and must be avoided.

 To control `follower` safely two set of commands are provided. (1) A set of control commands that can be used to control the `follower` such as `speedup` and `turn`. (2) A set of observation commands that allows to observe the environment and understand the `leader` position and other required information. As shown in the following figure, both of control and monitoring command can be accessed through passing related messages using TCP connections to `controlPort` and `observationPort` port which are configured using configuration file.   

<img  align="middle" src="assets/images/challangeProblem.png">





## Starting Guide:
The only prerequisite for using simulator is Java 1.8. Please make sure that Java is installed before start using the simulation. 

### Download 
The simlaution package is avaialble for three platfroms (Mac, Windows, Linux), to download please use the following links:
[Windows]() 

[Linux]() 

[Mac]() 


### Running the simulation
The simulation package is a compressed zip file which  contains the following folders and files. 
Note that depending on your platfrom, only one of the three folders Linux, Mac, and Windows is included. 

```
.
├── Linux
│   ├── Challenge-Linux.x86             ---> Executable simulator for Linux x86
│   ├── Challenge-Linux.x86_64          ---> Executable simulator for Linux x86_64
│   └── Challenge-Linux_Data
├── Mac
│   └── Challenge-Mac.app               ---> Executable simulator for Mac
├── RoverController-Source              ---> The source code of a simple java program that controls the follower using the keyboard
│   ├── DriveCommands.java
│   ├── RoverController.java
│   └── SocketCommunicator.java
├── RoverController.jar                 ---> A simple java program that controls the follower using the keyboard
├── Settings
│   └── config.txt                      ---> Configuration file 
├── UnityObserver.jar                   ---> A program that provides observation service and commands 
└── Windows
    ├── Challenge-Win64.exe             ---> Executable simulator for Windows 64 
    ├── Challenge-Win64_Data
    └── UnityPlayer.dll

```
To run the simulation follow the below steps sequentially:

1. Run the executable simulator. Depending on the used platform it is located at `Linux|Windows|Mac` folder. This will load a simulation environment as shown in the following figure. As discussed it includes two rovers `leader` and `follower`. When the simulation is loaded, the control commands are ready to use. By default, the simulator listens at TCP port xxx. However, you do not have access to observation commands and you need to start the simulation before using that. 

2. Run the  `UnityObserver.jar` which provides observation commands. by default, it listen on TCP port xxx. 

3. Now everything is ready to use, and you can start modeling your control. First you need to send a `ready` command to start the simulation. To make sure that simulation is loaded correctly and UnityObserver is started correctly, you can use the simple java program (`RoverController.jar `) that allows controlling the `follower` using the arrow keys. Note that the source code of this program is aviable at `RoverController-Source` and you can review that to get more familiar with supported commands.


## Configuration
Simulation can be configured using the parameters inside the `Settings/config.txt`. You may need to change this configuration on special cases such as changing TCP ports. The main configuration and their default value are described below.

{:.mbtablestyle}
|Parameter             | Default Value &nbsp; | Description|
-----------------------| --------------       |-------------
controlPort|9998|The TCP port that control commands can be accessed
observationPort|9999|The TCP port that observation commands can be accessed
leaderPort|9997|The TCP port that UnityObserver controls the leader through
evaluationPort|9996|The TCP port that UnityObserver observes the follower through
simulationIP|127.0.0.1|The IP address that the simulation and UnityObserver are running on
runTime|60000|The Running time of the simulation in ms
waitMin|2000|The minimum time between direction changes for the leader in ms
waitMax|5000|The maximum time between direction changes for the leader in ms
speedMin|10|The minimum wheel motor speed for the leader in % of total.
speedMax|60|The maximum wheel motor speed for the leader in % of total.
straightPercent|20|The percent chance that the rover will go straight instead of turning 
fileName|test1.csv|The name of the file to read or write movements too
preRecorded|false|False will create a new set of random moves and write them to a file after simulation, True will read moves from the file and play them back.
minDistance|12|The distance at which the follower is too close to the leader
maxDistance|15|The distance at which the follower is too far from the leader
sampleRate|250|The time in ms between position updates, and distance evaluations. 


## Supported Commands
As discussed several commands are provided for control and observartion which can be used by sending a message to related TCP port. Each message is a ASCII string. Format message of control messages is   `"Rover,"+commandID([command Parameter]*)`. For example there is a `brake` command that accepts one interger parametr a string,  sending the string  `Rover,brake(2)` call the brake command with parameter 2.  Format message of observation messages only contain command id and its parameter, i.e., `commandID([command Parameter]*)`
Some commands return some fields as results packed in string seperated by comma. Below we decribe the supported commands in two groups of observation and control.

### Control Commands

|Command ID            | Parameters  &nbsp;       | Returns  &nbsp;    | Description   &nbsp;                                        | Sample Message |
:-----------------------| :--------------   |:-------------| ------------                                                | ---------------|
setForwardPower        | amount of power                | None         | change the power of all wheels   |Rover,setForwardPower(int)|
incrementPower         | amount of power                 | None         | increment the power of all wheels |Rover,incrementPower(int) | 
setLRPower         | right power, left power                 | None         | Set the power of the left and right wheels |Rover,setLRPower(int,int) | 
brake         | amount of power                | None         | Apply the amount of brake power to all wheels |Rover,brake(int) | 
GPSx         | None                | Rover,X coordinate;         | returns the X coordinate |Rover,GPSx() | 
GPSy         | None                | Rover,Y coordinate;         | returns the Y coordinate |Rover,GPSy() | 
GPSz         | None                | Rover,Z coordinate;        | returns the Z coordinate |Rover,GPSz() | 
getCompass         | None                | Rover,degrees clockwise of North;         | returns the the degrees clockwise of North |Rover,getCompass()| 



### Observation Commands  

|Command ID            | Parameters  &nbsp;       | Returns  &nbsp;    | Description   &nbsp;                                        | Sample Message |
:-----------------------| :--------------   |:------------- | ------------                                                | ---------------|
getTargetX        | None               | x position of  `leader`         | returns the x position of the `leader`   |posx|
getTargetyZ       | None               | z position of  `leader`         | returns the x position of the `leader`   |posx|
getDistance         | None                 | distance with  `leader`        | returns the distance between `follower` and `leader` |dist| 
ready         | None               | None         | start the simulation | ready| 


## How to evaluate your solution?
As discussed `follower` always should keep a safe distance from `leader`. To verify that, the `UnityObserver` always monitor the distance between two rovers and report the time that `follower` is out of the safe distance. You can evaluate your solution based on this data reported by `UnityObserver`.

Note that by seeting  `straightPercent` to `0`, `leader` will take a straight path, hence following it would be an easy problem to solve. We expect your solution work with different values for `straightPercent`.





