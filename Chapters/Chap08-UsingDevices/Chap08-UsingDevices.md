## Using Devices


In this chapter, we revisit previous code to take advantage of the architecture offered by PharoThings.

### \(We should discuss\) 

What do we do:
- we can show how to do it because this is super simple \(And maybe this can be done in the previous chapter\)
- in this chapter, we use a device subclass \(like the WaterAlarm\)


We will revisit the `Blinker` class you previously defined.

### REDO THE FOLLOWING WITH POTDEVICE Developing a nicer LED blinker


Here is a little code snippet showing the API that we want to have. 

```
| blinker |
blinker := Blinker new.
blinker delay: 1 second.
blinker start. 
blinker stop. 
```


Here is a short explanation of this code:

- In the first line, we declare the variable `blinker`. We can use any name. We will use this variable to refer to  an instance of the Blinker class;
- In the second line, we instantiate the Blinker class \(with uppercase B\) and assign the created instance to the `blinker` variable. In this lesson, we will create this class and methods to control the LED;
- In the third line, we send some messages to the `blinker` object, for how long and how many times per second. This will make the GPIO behave according to the parameters sent.


Now we will develop all the mandatory class and methods to support this scenario.

Our implementation is a bit biased because we will not define a potentially infinite loop.
We will start with a version that only blinks a limited amount of time to get started.

### Blinker class definition

We start by defining a class named `Blinker`. This class has four instance variables `process`, `behavior`, `delay` and `pin`.

- `delay` will refer to the delay between two blinks.
- `pin` will be the pin controlling the LED.
- `behavior` will be a block representing the body of the blinker. 
- `process` will be a process based on the behavior. This process will be started and stopped by the user.

 
 
!!todo We should have a board instance variable and avoid the global variable. 



```
Object << #Blinker
	slots: { #board . #process . #delay . #pin . #loopBody};
	package: 'Blinker'
```


- The instance variable `board` will refer to the current board. 
- The instance variable `pin` will be the pin to which the LEDLED is connected.
- The instance variable process will refer to the process executed to animate the LED.
- The instance variable `loopBody` will refer to the code that will be executed in the loop.


We define the method `delay:` as follows: 

```
Blinker >> delay: aDelay
	delay := aDelay
```



### A first instance initialization


By default we decide to have a delay of 1 second. 
By initializing correctly the instance we will have a more robust code and avoid forcing the developer to send the message `delay:`.

```
Blinker >> initialize
	super initialize.
	delay := 1 second.
```


Then we initialize a pin to be a digital pin. 

```
Blinker >> initialize
	super initialize.
	delay := 1 second.
	board := RpiBoard3B current.
	pin := pinWithId: 4; beDigitalOutput.
```


Here we refer directly to the board and for a pro version of the code we would make sure that the board is just an extra instance variable so that we avoid having a reference to a kind of global variable. 

### Defining the Blinker body


We define a method that specifies the blinking behavior of the LED.
The following method, named `blinkerBody`, is just a loop that is waiting for the delay and toggle the status of the pin.
Hence programming the blinking behavior of our little object.
```
Blinker >> blinkerBody

	10 timesRepeat: [ 
		delay wait.
		pin toggleDigitalValue ]
```


We create the method `blinkerBody` so that subclass and change specialize or change this behavior by just subclassing the class `Blinker`.

### Revisit the initialization

Since we would like to avoid blocking the Raspberry when an instance of `Blinker` is working, we will use a Pharo process to execute the blinker behavior in a concurrent manner.
To create a process in Pharo, we should send the message `fork` or `forkNamed:` to a block closure.
So we revisit the initialization to store a block closure that will invoke the blinkerBody method. 
We will store the block closure in the instance variable body so that we can kill the process and start a new one.
```
Blinker >> initialize 
initialize
	super initialize.
	delay := 1 second.
	board := RpiBoard3B current.
	pin := pinWithId: 4; beDigitalOutput.
	loopBody := [ self blinkerBody ].
```



### Start / stopping

The `start` method forks a process and stores it in the instance variable `process`.

```
Blinker >> start
	process := body forkNamed: 'Blinker'
```

The method `stop` will simply kill the process. 

```
Blinker >> stop 
	process terminate.
	process := nil
```



### Save your work


Don't forget to save your work remotely. To do this, run this command on your local playground:

```
remotePharo saveImage.
```


This expression will save the complete Pharo environment running on your remote system.
If your Raspberry crashes you will get exactly the same environment that from the moment you saved it. 

### Get your code from Rasp to your Pharo


Having all the code only on your Raspberry is definitively naive and not really efficient if you want to version it and avoid having to do all the manipulation via the remote connection. 

PharoThings can also get your changes from the Raspberry and load them in the Pharo running on your machine. 

```
remotePharo applyChangesToClient
```


This way you can package your code and publish it on GitHub or other versioning systems.

### Another use case

We proposed one way to model the blinker.
There are several possible alternatives.
We could just use a loop that is stopped only using the message `stop`. 
Change the proposed code to introduce this.

### Conclusion


In a subsequent chapter, you will learn how to write a breathing led. 
We suggest that once you have it running, you revisit it using the same approach that the one proposed in this chapter.
