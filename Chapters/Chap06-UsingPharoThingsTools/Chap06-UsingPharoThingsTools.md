## Using PharoThings Tools


Pharo is a new generation reflective language and object-oriented programming environment. 
During the previous chapter, you executed the code inside a remote inspector. 
To get started using OOP (Object-Oriented Programming) with classes, methods, and instances, we invite you to implement a simple application to blink the LEDs.

During this lesson, you will code directly on the raspberry. 
What is really interesting is that you can interact live with the device. This is really powerful to debug your application and sensors. 
Later we will show you how to get the code you develop remotely on the raspberry to your machine and the inverse. 
This way you will be able to version your code on git and test it on your Raspberry without the fear of losing everything if your board crashes.


### Developing a nicer LED blinker

The following part of this chapter and application was based on the exercise `Developing a Simple Counter`, of Week 1 of Pharo MOOC (https://mooc.pharo.org/). 
We strongly recommend that you read and do the "counter exercise" to better understand the concepts explained here. 
And, of course, do the MOOC to learn how to develop using Pharo and the OOP concept.

We will present briefly all the code of the Blinker class and after we will show you how to use the tools.

#### Snippet example.

Here is a little code snippet showing the API that we want to have.

```
| blinker |
blinker := Blinker new.
blinker delay: 1 second.
blinker start. 
blinker stop. 
```


Here is a short explanation of this code:

- In the first line, we declare the variable `blinker`. We can use any name. We will use this variable to refer to an instance of the `Blinker` class;
- In the second line, we instantiate the `Blinker` class \(with uppercase B\) and assign the created instance to the `blinker` variable. In this lesson, we will create this class and methods to control the LED;
- In the third line, we send some messages to the `blinker` object, for how long and how many times per second. This will make the GPIO behave according to the parameters sent.


Now we will develop all the mandatory methods to support this scenario.

Our implementation is a bit biased because we will not define a potentially infinite loop.
We will start with a version that only blink a limited amount of time to get started.

### Blinker class definition

We start by defining a class named `Blinker`. This class has five instance variables  `board`, `delay`, `pin`, `process`, `loopBody`.

- The instance variable `board` will refer to the current board. 
- `delay` will refer to the delay between two blinks.
- `pin` will be the pin controlling the led.
- `loopBody` will be a block representing the body of the blinker. 
- `process` will be a process based on the behavior. This process will be started and stopped by the user.



```
Object subclass: #Blinker
	instanceVariableNames: 'board process delay pin loopBody'
	classVariableNames: ''
	package: 'Blinker'
```



We define the method `delay:` as follows: 

```
Blinker >> delay: aDelay
	delay := aDelay
```



### Instance initialization


By default we decide to have a delay of 1 second. 
By initializing correctly the instance we will have a more robust code and avoid to force the developer to send the message `delay:`.

```
Blinker >> initialize

	self delay: 1 second.
```


Then we initialize a pin to be a digital pin. 

```
Blinker >> initialize

	self delay: 1 second.
	board := RpiBoard3B current.
	pin := board pinWithId: 4.
	pin beDigitalOutput.
```


Here we refer directly to the board and we will revisit this decision in the following chapter using `PotDevice` and PharoThings board.
Notice how the board can be queried to return one of its pin.

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

Since we would like to avoid to block the Raspberry when an instance of Blinker is working, we will use a Pharo process to execute the blinker behavior in a concurrent manner.
To create a process in Pharo, we should send the message `fork` or `forkNamed:` to a block closure.
So we revisit the initialization to store a block closure that will invoke the `blinkerBody` method. 
We will store the block closure in the instance variable, loopBody, so that we can kill the process and start a new one.
```
Blinker >> initialize 

	self delay: 1 second.
	board := RpiBoard3B current.
	pin := board pinWithId: 4; beDigitalOutput.
	loopBody := [ self blinkerBody ].
```



### Start / stopping

The `start` method forks a process and store it in the instance variable `process`.

```
Blinker >> start
	process := loopBody forkNamed: 'Blinker'
```


The method `stop` will just simply kills the process. 

```
Blinker >> stop 
	process terminate.
	process := nil
```



### Opening a remote class browser


First you should open a remote class browser using 

```
remotePharo openBrowser.
```


You can see that the class browser is running on the raspberry because the window of the class browser displays the IP address from where it is running.


### Create your own class remotely

Let's create our first class. 
To create a class in Pharo, we need first to create a package. 
Inside the package, you can create many classes and inside the classes, you can create many methods. The methods are organized in protocols, to become more easily navigate between them. Take a look in Figure *@RemoteBrowser@* to better understanding. *edit image, put name in windows

In your local playground, call the Remote System Browser of your Raspberry Pi. If you are already connected to your Raspberry Pharo, you do not need to run the first line below again. This will open a window as shown in Figure *@RemoteBrowser@*.

![REDORemote System Browser.](figures/pharothings-remote-system-browser.png width=100&label=RemoteBrowser)



### Create a package

Let's create a package using the Remote Browser. Right-click the package area and enter the package name, as shown in Figure *@CreatingPackage@*. In this example, we will create a package named `PharoThings-Lessons`.

![REDOCreating a package remotely.](figures/pharothings-creating-package.png width=100&label=CreatingPackage)

### Create a class

To create a new class, edit the default class template by changing the #NameOfSubclass to the name of the new class. In this example let's create the class `#Blinker`. Take care that the class name begins with a capital letter and that you do not remove the hash symbol \(#\) in front of NameOfSubClass. 

You must then fill in the names of the instance variables for this class. We need an instance variable called `led`. Be careful to leave the string quotes!

```
Object subclass: #Blinker
	instanceVariableNames: 'board process delay pin loopBody'
	classVariableNames: ''
	package: 'Blinker'
```


Now we need to compile it. Right click on the code area and select Accept option. The `Blinker` class is now compiled and added to the system, as shown in Figure *@CreatingClass@*.

![Creating a class remotely.](figures/pharothings-creating-class width=100&label=CreatingClass)

### Create a protocol

Let's create a new protocol to organize the methods. The first protocol we are going to create is `initialization`, as shown in Figure *@CreatingProtocol@*.

![REDOCreating a package remotely.](figures/pharothings-creating-protocol width=100&label=CreatingProtocol)

### Creating the delay: method


```
Blinker >> delay: aDelay
	delay := aDelay
```



### Creating an initialize method


Inside this protocol, we will create an `initialize` method. This means that every time we create a new object using this class, in this case, the `Blinker` class, this method will be executed to define some variable in the new object.

Let's use the instance variable `pin`, which we defined when we created the class. 
The instance variable is private to the object and accessible by any methods inside this class. These methods can access this variable to get or set any value to it.

```
Blinker >> initialize 

	self delay: 1 second.
	board := RpiBoard3B current.
	pin := board pinWithId: 4; beDigitalOutput.
	loopBody := [ self blinkerBody ].
```


Here is a short explanation of this code:

- The first line defines the name of the method;
- In the second line, we configure the GPIO that we will use. Note that we need the GPIO number and ID. The ID is required to communicate with Wiring Pi Library. You can see the `ID` and `GPIO number` in PotRemoteBoard inspector, as shown in Figure *@RemoteInspector@*;
- In the third line, we define the model of the Raspberry board and configure this GPIO as beDigitalOutput. This means that when the GPIO change to value:1, the power will go out of the GPIO to power the LED.



![REDOLooking for ID and GPIO number on Remote Inspector.](figures/pharothings-board-inspector-id-number.png width=70&label=RemoteInspector)

Compile your code \(cmd + S\) and the method will be shown in the remote browser, as shown in Figure *@InitializeMethod@*:

![REDOCreating the initialize method.](figures/pharothings-creating-method-initialize.png width=100&label=InitializeMethod)

#### Creating a method to do actions


Now let's create a method to control the object led inside the class Blinker. 
Let's take the code that we used in PotRemoteBoard inspector to do the LED to blink.
Create the protocol `operations` and inside this protocol, create the following method:

```
Blinker >> blinkerBody

	10 timesRepeat: [ 
		delay wait.
		pin toggleDigitalValue ]
```


Here is a short explanation of this code:

- In the first line, we define the message with timesRepeat: and waitForSeconds:. We inform the kind of value will be received, creating 2 variables: aNumber and anInteger;
- We replace these variables in the code and now we have the control to say how many times repeat and for how many seconds;
- We finished the code by putting everything inside a fork to create a process in Pharo. While the process is running, you can open the Remote Process Browser \(remotePharo openProcessBrowser\) and see the process. This is useful when you wanna kill the remote process.


Compile your code \(cmd + S\) and  the method will be shown in the remote browser, as shown in Figure *@OperationsMethod@*:

![REDOCreating an operation method.](figures/pharothings-creating-method-operations.png width=100&label=OperationsMethod)

### Using your new class


Now we can use the class that we created, the Blinker class. To do this, let's open the Remote Playground:

```
remotePharo openPlayground.
```


and run the code that we saw in the begin of this lesson:

```
	| blinker |
	blinker := Blinker new.
	blinker delay: 1 second.
	blinker start. 
```


Run this code, as shown in Figure *@RemotePlayground@* and... `cool`! Now your LED is blinking! And the better, you did this using object-oriented programming!

 You do not need to change your code every time you wanna change these parameters. Just change the messages you send to the object and it will behave as you want.

![REDORemote playground.](figures/pharothings-remote-playground-blinker.png width=100&label=RemotePlayground)

### For fun 

You can now add the possibility to change the number of times the LED is blinking defining a method named `blinkingTimes: anInteger` for example.


### Save your work


Don't forget to save your work remotely. To do this, run this command on your local playground:

```
remotePharo saveImage.
```


This expression will save the complete Pharo environment running on your remote system.
If your raspberry crashes you will get exactly the same environment that from the moment you saved it. 

### Conclusion


In this tutorial, you learned how to define packages, classes, and methods. The flow of programming that we chose for this first tutorial is similar to most of the programming languages.

With PharoThings you can remotely develop and manage your Raspberry GPIOs. Very easy and powerful.

In the next lesson, let’s use what we learned in this lesson and write a simple code to flow lights using 8 LEDs.
