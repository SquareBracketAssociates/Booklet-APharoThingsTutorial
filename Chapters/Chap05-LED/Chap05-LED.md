## Turning LED on/off


One of the classic analogies in electronics to “Hello World” is turn on a LED(Light-Emitting Diode) or lamp. In this first lesson, we will learn how to connect correctly an LED to your Raspberry Pi and how to use PharoThings to control this LED by turning it on and off.

Coding in Pharo is very simple, but it is very powerful and you can control all the GPIOs of your Raspberry Pi remotely.

If you did not yet see how to install the PharoThings on your Raspberry Pi and how to control it remotely, you can find the instructions in Instalation chapter.

### What do we need?


In this lesson we will use a very simple setup.

#### Components


- 1 Raspberry Pi connected to your network(wired or wireless)
- 1 Breadboard
- 1 LED
- 1 Resistor(330 ohms)
- Jumper wires


### Experimental theory


Before constructing any circuit, you must know the parameters of the components in the circuit, such as their operating voltage, operating circuit, etc.

#### The LED


LEDs(abreviation of Light-Emitting Diode) is a semiconductor that produce light when current flows throught it. Light is produced by an effect called electroluminescence.

To turn on the LED, we need to send the correct voltage and current to it. The voltage and current can’t be too high, otherwise, the LED will burn, or in some cases, damage the Raspberry.

Typically, the forward voltage of an LED is between 1.8 and 3.3 volts. It varies by the color of the LED. A red LED typically drops 1.8 volts, but voltage drop normally rises as the light frequency increases, so a blue LED may drop from 3 to 3.3 volts.

Most 3mm and 5mm LEDs will operate close to their peak brightness at a drive current of 20 mA. This is a conservative current: it doesn’t exceed most ratings(your specs may vary, or you may not have any specs–in this case, 20 mA is a good default guess).

In this experiment, the operating voltage of the LED is between 1.5V and 2.0V and the operating current is between 10mA and 20mA.

#### The Resistor


We must always use resistors to connect LEDs up to the GPIO pins of the Raspberry Pi to limit the voltage and current between the LED and the Raspberry to a safe value.

A small change in voltage can produce a huge change in current(see more: LED Current vs. Voltage)

In this experiment, we will use a 330ohm resistor. To identify the correct resistor, follow one of the following color sequences, depending on the number of bands:

- If there are four colour bands, they will be Orange, Orange, Brown, and then Gold;
- If there are five bands, then the colours will be Orange, Orange, Black, Black, Brown.


It does not matter which way round you connect the resistors(in this experiment). Current flows in both ways through them. This means that you can connect the resistor at the positive pole or the negative pole of the LED, as well as starting with the first or last color, as shown in Picture *@Ledpolarity@*.

But the LEDs will only work if the power is supplied correctly(if the polarity is correct). You will not burn the LEDs if they connect the wrong way - they just will not turn on.

![LED polarity and resistors. %width=60&label=Ledpolarity](figures/pharothings-led-polarity-resistors.png)

#### The Breadboard


A breadboard is used to build prototyping of electronics. With a breadboard, it is not necessary to use solder, this way you can reuse the board. This makes it easy to use to create temporary prototypes and experiment with circuit design.

The holes in the breadboard are connected following a pattern, as shown in Picture *@Breadboard@*.

- The red(+) and blue(-) rail are connected horizontally;
- The holes in the middle are vertically connected.


![Breadboard scheme. % width=60&label=Breadboard](figures/pharothings-breadboard.png)

### Experimental procedure


Now we will build the circuit. This circuit consists of an LED that lights up when power is applied, a resistor to limit current and a power supply(the Rasp).

- Connect the Ground PIN from Raspberry into breadboard blue rail(-). Raspeberry Pi models with 40 pins has 8 GPIO ground pins. You can connect with anyone. In this experiment we will use the PIN6(Ground);
- Then connect the resistor from the blue rail on the breadboard(-) to a column on the breadboard, as shown in Picture *@physicalLed@*;
- Now push the LED legs into the breadboard, with the long leg(with the kink) on the right;
- And insert a jumper wire connecting the rigth column and the PIN#7(GPIO4).


The Figure *@physicalLed@* shows how the electric connection is made:

![Physical connection LED. % width=60&label=physicalLed](figures/pharothings-raspberry-led-resistor-lesson-01.png)

### Experimental code


With PharoThings, we have some tools for remote development. We'll explain about them in the next chapters, so for now, you just need to know that Pharo has four main tools: Remote Playground, Remote Board Inspector, Remote Process Browser and Remote Code Browser.

Now let's remotely connect from your computer to Raspberry and write some code in `Remote Board Inspector` to control GPIOs using PharoThings and then turn on/off the LED.

If you have not seen how to install PharoThings on your Raspberry Pi and how to control it remotely, see the Installation chapter.

#### Connecting remotely


Through your local Pharo image, let's connect in the Pharo image running on Raspberry and open the inspector. Run this code in your local playground:

```
remotePharo := TlpRemoteIDE connectTo: (TCPAddress ip: #[192 168 1 106] port: 40423).
remoteBoard := remotePharo evaluate: [ RpiBoard3B current].
remoteBoard inspect.
```


In your inspect window(Inspector on a PotRemoteBoard) you can see a scheme of pins similar to the Raspberry Pi docs. But here it is a live tool which represents the current pins state.

![Remote Board Inspector.  %width=60&label=remoteBoard](figures/pharothings-inspector-remote-board-raspberry.png)

Digital pins are shown with green/red icons representing high/low(1/0) values. In case of output pins you are able to click on the icon to toggle the value.

To control the LED we first introduce the named variable `led` which we assigned to GPIO4 pin instance:

```
led := gpio4.
```


Note that you can also directly use the variable `gpio4`.
Then we configure the pin to be in digital output mode and set the value:

```
led beDigitalOutput.
led value: 1.
```


It turns the LED on.

You can notice that gpio variables are not just numbers/ids. They are PharoThings models boards with first class pins. They are real objects with behaviour. For example you can ask pin to toggle a value:

```
led toggleDigitalValue.
```


Or ask a pin for current value if you want to check it:

```
led value.
```


### Blinking LEDs


Now we can play with the LEDs, turn them on and off. Let's use this basic setup to write some code on the inspector playground to blink the LED. Next, we will learn how to remotely create a very simple application using classes, methods, and instances to control the LED.


### Experimental code

In your inspect window(Inspector on a PotRemoteBoard), let's initialize the led and set the PIN#7 to be in digital output mode as we did in the last lesson:

```
led := gpio4.
led beDigitalOutput.
```


To blink the LED let's create a simple loop using the message `timesRepeat:` to change the value of the LED every 1 second. 
For this we create a delay using the message `Delay forSeconds: 1`.
To change the value of the pin from on to off and the inverse, we call the method `toggleDigitalValue`, as we saw previously:

```
6 timesRepeat: [
	led toggleDigitalValue.
 	1 second wait
	] 
```


Run this code, as shown in Figure *@RemoteBoardInspector@* and... `cool`! Now your LED is blinking!

![Remote board inspector. %width=70&label=RemoteBoardInspector](figures/pharothings-board-inspector-blink-led.png)

Change the values to repeat more times and to wait less time between toggling. This will cause the LED to blink faster.

```
10 timesRepeat: [
	led toggleDigitalValue.
 	0.5 second wait
	] 
```


### What did we learn?


With PharoThings you can remotely control all the GPIOs in your running board!

You can:
- Interact remotely with pins and boards;
- See the current pins state in real time;
- Run the code dynamically;
- Easy, powerful.


### In the next lesson

In this tutorial, you learned how to blink a LED by typing some code in the remote inspector. 
But with Pharo we can do more! 
In the next lesson, let’s start to use PharoThings tools and use the power of OOP to reuse abstractions.
Let's create a simple application, write classes and methods, all remotely.
