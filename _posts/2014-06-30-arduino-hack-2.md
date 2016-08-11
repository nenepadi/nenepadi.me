---
layout: post
title:  "My first hack with the board - Part 2"
date:   2014-07-04
description: "The code for my traffic light system"
tag: [arduino, electricals, hacks, traffic-light]
categories: [arduino]
share: true
comments: true
project: false
---

Holla people, code for the arduino (traffic light system) project is below. I am in a hurry today. I might tell you how it all works in my next post.

{% highlight c %}
// Create constants for the pins (refer to [part one]({{ site.url }}/arduino-hack-2))
const unsigned int RED = 6;
const unsigned int YELLOW = 4;
const unsigned int GREEN = 2;
const unsigned int PRED = 8;
const unsigned int PGREEN = 9;
const unsigned int PAUSE = 700;

// variables for the sake of monitoring
const int sensor = A0;
const float carSpeed = 5.0;

// traffic delays
int DELAY_GREEN = 15000;
int DELAY_YELLOW = 3000;
int DELAY_RED = 10000;

// function initializing board setup
void setup(){
    pinMode(RED,OUTPUT);
    pinMode(YELLOW,OUTPUT);
    pinMode(GREEN,OUTPUT);

    //pinMode(sensorAlert, OUTPUT);

    pinMode(PRED,OUTPUT);
    pinMode(PGREEN,OUTPUT);

    Serial.begin(9600);
}

void loop(){
    //*****CONTROL SYSTEM******//
    if (Serial.available() > 0){
        int command = Serial.read();
        if(command == '1'){
            while(command == '1'){
                //******VEHICULAR AND PEDESTRAIN SYSTEM******//

                //Greenled on
                green_light();

                /*
                 * When greenlight is on determine cars speed...
                 * When the car's speed is beyond maximum alert the police...
                 * But because it is a test product we just print something to the screen...
                 */

                //*****MONITORING SYSTEM********//
                if(ledState == HIGH){
                    sensorReading = analogRead(sensor);
                    Serial.println(sensorReading);
                    if(determine_car_speed(sensorReading) > carSpeed){
                        //ledState = !ledState;
                        //digitalWrite(sensorAlert, ledState);
                        Serial.println("Call the police");
                        Serial.println("There is a lunatic on our roads");
                    }
                }

                delay(DELAY_GREEN);

                //Yellow light on
                amber_light();
                digitalWrite(PRED, HIGH);
                delay(DELAY_YELLOW);

                //Red light on
                red_light();

                //******STILL MONITORING******//
                // monitor humans...
                if(ledState == HIGH){
                    if(sensorReading < 450){
                        DELAY_RED = DELAY_RED + 2000;
                    }
                }

                delay(DELAY_RED);
            }
        } else if(command == '2'){
            while(command == '2'){
                // activate only the yellow light...
                // boolean instruction = true;
                all_lights_off();
                blinking(YELLOW);
                // instruction = false;
            }
        } else{
            Serial.println("*****************************************");
            Serial.println("You cant be in charge of this system");
            Serial.println("You fed me the wrong input");
            Serial.println("*****************************************");
        }
    }
}

/***************************************************************
***************************FUNCTIONS****************************/

void all_lights_off(){
    digitalWrite(GREEN, LOW);
    digitalWrite(YELLOW, LOW);
    digitalWrite(RED, LOW);
    digitalWrite(PRED, LOW);
    digitalWrite(PGREEN, LOW);
}

void green_light(){
    all_lights_off();
    ledState = HIGH;

    // vehicular light...
    digitalWrite(GREEN, ledState);

    // pedestrain light...
    digitalWrite(PRED, ledState);
}

void amber_light(){
    all_lights_off();
    ledState = HIGH;
    digitalWrite(YELLOW, ledState);
}

void red_light(){
    all_lights_off();
    ledState = HIGH;

    //vehicular light...
    digitalWrite(RED, ledState);

    //pedestrain light...
    digitalWrite(PGREEN, ledState);
}

// monitoring functions...
float determine_car_speed(int reading){
    float speedResult;
    speedResult = reading / 120;
    return speedResult;
}

void blinking(int PIN){
    digitalWrite(PIN, HIGH);
    delay(1000);
    digitalWrite(PIN, LOW);
    delay(1000);
}
/***************************************************************/

{% endhighlight %}
