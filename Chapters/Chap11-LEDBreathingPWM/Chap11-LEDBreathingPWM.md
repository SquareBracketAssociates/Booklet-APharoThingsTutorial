## Breathing LED with PWM**PWM support is currently broken in PharoThings.**In a previous lesson, we learnt how to switch on and off a LED. How can we fade a LED now? In order to do that,we will simulate an analog pin with a digital pin. This technique is known as PWM \(Pulse-Width modulation\). See more detailson Wikipedia: https://en.wikipedia.org/wiki/Pulse-width\_modulation### What do we need ?We are using the same set as the first lesson:#### Components- 1 Raspberry Pi connected to your network \(wired or wireless\)- 1 Breadboard- 1 LED- 1 Resistor 330ohms- Jumper wires#### Experimental theoryPWM is basically a control of the voltage by switching power on and off at a fast rate.This simulates a voltage between 0 and the maximum input voltage, which is not possible with the standard digital pin behavior.The Raspberry Pi has 4 GPIO pins that can be set in PWM mode.These pins are PharoThings GPIO pins 12, 32, 33 and 35.**Figure need to be change, to use GPIO18 instead of GPIO26.**![Fading a led with PWM](figures/pharothings-raspberry-pwm-led.jpeg width=70&label=PWMLed)### Experimental procedureThe setup is similar as the led example from chapter 2.However, choose a PWM GPIO pin as power input to the led.For example, choose GPIO18 \(id 12\).Remotely connect to your Raspberry Pi board and inspect the board.### Experimental codeIn the inspector, let's initialize the led and set the pin GPIO18 to be in PWM output mode.We also configure the pin to have a PWM behavior.```led := gpio18.
led function: PotPWMFunction new.
led bePWMOutput.```To change the state of the pin, we set a value between 0 and 1023:```led writePWMValue: 42.```Using the following code, you will make your led breathe:```10 to: 30
  do:[:i|
    led writePWMValue: i.
    100 milliSeconds wait].
30 to: 10 by: -1
  do:[:i|
    led writePWMValue: i.
    100 milliSeconds wait].```### Creating a class Now, write your own breathing led class, that you can instantiate and parameterise like the following:```| breathingLed |
breathingLed := BreathingLed new.
breathingLed breathingDelay: 10 milliSeconds.
breathingLed breatheFrom: 10 to: 30.
breathingLed startBreathing.```### Encapsulating behavior```BreathingLed >> breathingDelay: aDelay``````BreathingLed >> breatheFrom: start to: end``````BreathingLed >> startBreathing```