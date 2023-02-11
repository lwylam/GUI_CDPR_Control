# GUI_CDPR_Control
A simple GUI for CDPR robots built with up to 8 Teknic motors.

## Preparation
The 6 files listed below are all the minimal needed files to run a cable robot with this graphical user interface (GUI). Always keep these files in the same folder. Upon successful run of the programme, a new folder named **Data Log** will be created and simple log files are saved within automatically.

- CDPR-GUI.exe
- lastPos.txt
- model.json
- sFoundation20.dll
- traj.csv
- extern.csv

Please feel free to download this repository as a package to run the programme.

## Basic components 
The GUI for general purposes cable robot consists of 4 major components. 

(1)	__Status bar__. This is the text on the top left corner, starting with the word “Status:” This shows the current section of programme. The details of the sequence are explained later in this documentation.

(2)	__Menu buttons__. This list of buttons is located under the status bar. Different functions in the specified section are listed here.

(3)	__Next/Exit button__. This button is at the middle right of the application. Usually it allows the user to move on to the next section of the programme, unless under unexpected situations then it shows “Exit” to terminate the programme.

(4)	__Textbox__ (black). This is the big black box on the lower half of the application. Most of the current status of the programme would be updated here. This is mainly for any system extra information.

![GUI Layout](https://github.com/lwylam/GUI_CDPR_Control/blob/main/images/stage1.png)

The programme operation follows the sequence below.

## Read model and Search network

Before starting the **CDPR-GUI.exe**, please make sure the parameters saved in "model.json" is correct. Any changes in this json file after starting the programme will not be effective. Below is an example of the json file. For details of the "model.json", plese refer to [Define model.json](https://github.com/lwylam/GUI_CDPR_Control/wiki/Define-model.json#element-explanation).

```
{
    "ModelName": "CNC Cable Robot",
    "tekMotorNum": 8,
    "home": [2.8, 1.8, 0.8, 0, 0, 0],
    "limit": [-0.01, 5.9, -0.01, 3.92, -0.05, 2.1],
    "targetTorque": -2.5,
    "absTorqueLmt": 85.0,
    "toMotorCmdScale": 814873.3086,
    "pulleyRadius": 0.045,
    "pulleyZdir": [ 1, 1, 1, 1, -1, -1, -1, -1],
    "frameAttachments":[
        [0.000, 0.000, 2.126],
        [0.000, 3.900, 2.126],
        [5.830, 3.900, 2.126],
        [5.830, 0.000, 2.126],
        [0.000, 0.000, -0.056],
        [0.000, 3.900, -0.056],
        [5.830, 3.900, -0.056],
        [5.830, 0.000, -0.056]
    ],
    "endEffectorAttachments":[
        [-0.194, -0.200, 0.222],
        [-0.196, 0.197, 0.223],
        [0.197, 0.197, 0.220],
        [0.197, -0.197, 0.221],
        [-0.190, -0.220, -0.190],
        [-0.190, 0.220, -0.190],
        [0.190, 0.220, -0.190],
        [0.190, -0.220, -0.190]
    ]
}
```

When starting the programme, it first reads the saved model from "model.json". Upon success, "Imported model.json: XXX" will be shown in the textbox. However, if the file cannot be read or some data are missing, the “Next” button will appear as an exit button to terminate the programme.

![Read model](https://github.com/lwylam/GUI_CDPR_Control/blob/main/images/stage0r.png)

When it is determined that the system is ready for the next section of operation, click __once__ on the “Search” button (which is the next/exit button) to search for the motor network. Please __WAIT__, as this process may take several tens of seconds. Upon completion, the textbox shows the number of motors found in the network.

## Initialization
![Initialization](https://github.com/lwylam/GUI_CDPR_Control/blob/main/images/stage1.png)

__Torque__ – Tighten cables with Torque mode

All motors run independently until the defined percentage of the full torque (-2.5% in the example) is reached. This tightens the cable robot. Repeat this command multiple times to achieve the desired stiffness of end-effector of the cable robot. The check box on the side allow the motors to turn in opposite directions to loosen the cables at +1% of the full torque.

__Set as Home__ – Set the current pose as pre-defined home

The defined home pose can be seen to the right of the “Set as Home” button. This command sets all 8 internal motor counts to zero, and copy the home array into the in array, which stores the current position / pose of the cable robot.

__Update Pos__ – Update the in1 array with an external file

This command reads the pose saved in the “lastPos.txt”, then update the in array. The internal motor counts are updated accordingly. Please note that this file is updated upon safely exiting the application, and the last pose of the cable robot and internal motor counts are saved.
An alternative is to use Leica for finding the current position and rotation, update “lastPos.txt” manually and save the file, then finally run this command.

IMPORTANT! Always run this function after starting up the programme, before moving on to the operation stage. This ensures the system has the correct position as intended.

__Adjust Cable__ – Adjust the cable individually

The button itself will update the display of the internal motor count of the selected motor (#1-8). “+” and “-” increase and decrease the cable length respectively. “Home” is going to the motor counter zero, ie. homing. This can be used to restore the cable robot to home position after crash, motor to motor, as long as the motors' power is never cut off during crash. IMPORTANT! Do not use commands to motor no. above the defined "tekMotorNum" in the "model.json", or else the programme will crash/quit upon failing to send commands to non-existing motors.

If any commands or functions from this section is needed afterwards, please safely quit the programme and restart it. Upon clicking on the "Next" button, the application moves on to the operation section.

## Operation
Upon reaching this section of the programme, data logging would start, recording the modes used and the points commanded. Saved files can be found in the "Data Log" folder.

![Trajectory](https://github.com/lwylam/GUI_CDPR_Control/blob/main/images/stage2ext.png)

__Trajectory__ – Read from \"traj.csv\" file for pre-set trajectory

Define the trajectory in the form of 6 DoF pose and time in ms a line (eg. 2.197, -3.599, 0.744, 0, 0, 0, 350). Save the file before running this command.

IMPORTANT! Trajectory stop and pause buttons are on the right side of the application. Use them as applicable.

__External__ – External point step trajectory, read from \"extern.csv\" file

Define the trajectory in the form of 6 DoF pose for every system defined time step (ie. 35 ms) a line (eg. 2.197, -3.599, 0.744, 0, 0, 0). Save the file before running this command.

IMPORTANT! Trajectory stop and pause buttons are on the right side of the application. Use them as applicable.

__Manual__ - Manual control using the arrow buttons on the application

The direction of motions assume the user faces towards the positive y direction of the cable robot, meaning positive x would be to the right. Note that the diagonal rise and fall has a pre-defined 55° movement. "home" button can be used to home the robot with trajectory planning. The white textbox below the arrows can be used to change the step size of the manual control.

After all the desired operations, it is recommended to home the robot in the manual control, because the internal counts will be lost when power is cut off. Then, click "Next" to move on to the next section of the programme, which is to prepare for shut down.

## Shut down preparation
Upon coming to this point of the programme, the log file is closed with the time saved. The "lastPos.txt" is saved with the in array and motor internal counts. Then, click on "Exit" to safely quit the application.

![Shut Down Preparation](https://github.com/lwylam/GUI_CDPR_Control/blob/main/images/stage3.png)

_Last updated on 11/02/2023_
