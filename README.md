
Smart-Irrigation-Robot
This project is all about the automatic irrigation system, This robot will start moving and then stop whenever it will find any plant then it starts watering according to their need.

Context
Introduction
Circuit diagram
Setup yours
code
tools
material
Guidelines
Manual
INTRODUCTION
Goal of plant watering robot was to follow a line to a potted plant, actuate a sensor into the dirt to check the moisture level of the dirt, water the plant if needed, and continue on to the next plant when the moisture threshold is reached.This robot will start moving and then stop whenever it will find any plant then it starts watering according to their need. Once the robot reaches the plants, a bump sensor would be triggered to cause actuation of a servo motor with a moisture sensor on it

Circuit Diagram
The circuit diagram of the automatic plant watering system is shown in Fig. The circuit comprises an Arduino UNO board, a soil moisture sensor, a servo motor, DC water pump, and an L293D (IC1) motor driver IC,ultrasonic sensor,moiture sensor, li on battery,TP4056 charging module, 100RPM geared motor,wheels.

image

Set up yours
To reproduce this project, you'll need some tools, some material, and the code from this project

Software Setup
The program is written in Arduino programming language. The code is well-commented and easy to understand. Compile the irrigation robot.ino code and upload it to the microcontroller, using Arduino IDE version 1.

Once the arduino is loaded with the program it's time to wire it all up. Follow the diagram and wire everything up accordingly. Do not plug the arduino in to power at this point. Make sure everything is situated before letting it loose on your plants. You will need to attach a hose to the pump which will deliver water to your plants. I used a fish air hose to do this.

The sensor will calibrate by itself once it is kept in the soil and the threshold value will be shown on the serial monitor in Arduino. Serial debugging is available in this program.

The program in the Arduino reads the moisture value from the sensor every 20 seconds. If the value reaches the threshold value, the program does the following three things:

It moves the servo motor , along with the water pipe fixed on it, toward the potted plant, whose moisture level is less than the predetermined/ threshold level.
It starts the motor pump to supply water to the plant for a fixed period of time and then stops the water pump.
It brings back the servo motor to its initial position.
Code
Upload the attached sketch to your arduino. Once the arduino is loaded with the program it's time to wire it all up. Follow the diagram and wire everything up accordingly. Do not plug the arduino in to power at this point.

First, you have to make to install these libraries in arduino IDE

  #include <AFMotor.h>
 #include<Servo.h> 
We haave to define the triggered pin and echo pic for ultrasonic sensor 1 and ultrasonic sensor 2 triggered pin> is used to trigger the ultrasonic sound pulses. Echo pin produces a pulse when the reflected signal is received. echo pin>Echo pin is an Output pin. This pin goes high for a period of time which will be equal to the time taken for the US wave to return back to the sensor

const int trigPin = A0;                       //Defines the trig pin of ultrasonic sensor 1st 
const int echoPin = A1;                       //Defines the echo pin of ultrasonic sensor 1st
const int trigPin1=A2;                        //Defines the trig pin of ultrasonic sensor 2nd 
const int echoPin1=A3;                        //Defines the echo pin of ultrasonic sensor 2nd 
Defines the pin of moisture sensor pin and store value given by moisture sensor

int mpin = A4;                                
int mout;                                     
Defines the frequency which will be given to motor 1 and motor 2

AF_DCMotor motor1(1, MOTOR12_1KHZ);           
AF_DCMotor motor2(2, MOTOR12_1KHZ);           
AF_DCMotor motor4(4, MOTOR12_1KHZ);
Starts serial communication with the arduino and PC and /Define the attached pin for servo motor 1

void setup() {
  Serial.begin(9600);                         
  pinMode(trigPin, OUTPUT); 
  pinMode(echoPin, INPUT);
  pinMode(trigPin1, OUTPUT); 
  pinMode(echoPin1, INPUT);
  m1.attach(10);                             
  m2.attach(9);                              
To set the particular speed of motor 1 and motor 2

 motor1.setSpeed(255);                      
  motor2.setSpeed(255);                      
Formula that set the distance of robot goes from one plant to next one

distance= duration*0.034/2;
Make sure everything is situated before letting it loose on your plants. You will need to attach a hose to the pump which will deliver water to your plants. If you haven't plugged in the pump go ahead and do so. Finally connect the arduino to your chosen power source. I'm using a simple 5V charger and a USB cable.

Tools
The tools are very simple, I used for this project:

Battery holders
mounting
A flat screwdriver
Plastic rod
If you have them you can also add some wire strippers, but they are not indispensable.

Material
Here is a list of the products used to build the system.

To control the system:

Arduino UNO (2100 pkr)
Servo SG90(640)
L293D Sheild(500pkr)
100 RPM Geared motor and wheels(880pkr)
Prototyping jumper wires(140pkr)
Wooden box
Plastic container
For the autonomy in energy:

LI_ON Battery(280pkr)
Charging module(TP4056)(120pkr)
For the water tank:

12V DC pump(350pkr)
The sensors:

Ultrasonic sensor(380pkr)
Moisture sensor(180pkr)
For a total of 10k. That's not cheap! But keep in mind that it's still cheaper than a pre-built system, and with a lot more capabilities! Also, some parts are only for prototyping, and we purchased many components in groups of several pieces for other projects, you don't need 3 NodeMCU boards, nor 6 relays for this project

Power Block The L293D Sheild can drive 4 DC motors and 2 stepper or Servo motors at the same time. Each channel of this module has the maximum current of 1.2A and doesn't work if the voltage is more than 25v or less than 4.5v.the supply voltage and reduces it to a constant 5V making it suitable to run the Arduino & Soil Moisture Sensor.

Moisture Sensor The sensor feeds an analog value to the Arduino. The threshold level of moisture is calibrated by the user depending on the type of plant used.

GUIDELINES:
Before powering the circuit on, you need to keep in mind the following macro definitions in the code:

Changing the angle of rotation of the servo horn toward the first plant and second plant. The default values are 70 degrees and 145 degrees.
Changing the watering time according to the size of the plant. The default values are five seconds and eight seconds.
Changing the threshold value according to your need. The default value is 600.
The water pipe is connected to the servo motor which rotates according to the requirement.

If there are two crops A & B. and if A has less amount of moisture then the servo motor rotates toward crop A.
Starts the watering and when it will fill up it will rotate towards PLANT B. this is one more benefit of this project.
Place the flower plants where the pipe from the servo motor can easily reach them. When the moisture level dips below 600, the servo rotates at an angle of 70 degrees. That is after the servo motor horn moves 70 degrees toward the first pot, the motor pump will be on for five seconds and then stop automatically. Then, the servo returns to its original position. Similarly, if you are using a second sensor, the servo motor horn will move to 145 degrees to the second biggest pot, and the motor pump will be on for eight seconds and then stop automatically. The servo returns to its original position.

