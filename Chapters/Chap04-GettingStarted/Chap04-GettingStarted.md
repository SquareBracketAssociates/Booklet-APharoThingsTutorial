## Installation


In this chapter you will learn how to:

- Run Pharo IoT in a Raspberry Pi that has Raspbian already installed;
- Install Pharo IoT and Raspbian from scratch in headless mode \(without keyboard/mouse/screen\);
- Run and use Pharo IoT IDE on your Linux, Windows or Mac OSX computer.


When you buy a new Raspberry Pi, the Operating System is not factory installed. The first step you need to do to start with Pharo IoT is to install an Operating System on your Raspberry Pi.

To maintain compatibility between this tutorial and your Raspberry, we recommend installing the Raspbian operating system. Raspbian is the Foundation’s official supported operating system, a Linux OS based on Debian Stretch to run on ARM processors.

### Running Pharo IoT in a Raspberry that has Raspbian


If you already have Raspbian running on your Raspberry Pi, you can simply use the Pharo IoT zero-conf. Open a terminal window in your Raspberry Pi \(local or remote SSH\) and enter the following commands:

```
wget -O - get.pharoiot.org/server | bash
```


That way you will download the server side and extract the files to the `pharoiot-server` folder. Go to the folder `cd pharoiot-server` and run the `./pharo-server`.

```
cd pharoiot-server
./pharo-server
```


If everything is alright, you will see this message: `‘a TlpRemoteUIManager is registered on port 40423’`. This means that you have TelePharo running on your Raspberry on TCP port 40423.

Now you can use Pharo IoT on your computer to connect to your Raspberry and create IoT applications remotely.

If you still do not have Raspbian installed on your Raspberry Pi or want to know how to install everything in headless mode, the next procedure will explain how to do this.

### Installing Raspbian and Pharo IoT from scratch \(Raspberry Pi Headless Setup\)


There are many options to install Raspbian on your Raspberry. The most common way is to download the ISO image from the official Raspberry website and follow the steps to install it. Basically, they are: copy an ISO image to an SD card, insert it in Raspberry Pi, turn On the Rasp and use a keyboard/mouse/screen to use it as a normal computer.

But we will not use keyboard/mouse/screen to install Raspbian and run Pharo IoT! We do not need them.

We will use a third-party program to perform these tasks automatically:

- Install Raspbian Full OS
- Setting the Raspberry Pi hostname
- Set boot to console
- Enable the I2C and SPI modules
- Connect it in your WiFi network
- Download Pharo IoT \(requires Rasberry Pi connected on the internet\)
- and start the Pharo IoT server at every boot


#### PiBakery


![User interface of PiBackery.](figures/pharothings-pharoiot-pibackery-raspberry-headless.png width=90&label=piBackery)

- Download the PiBakery: `https://www.pibakery.org/download.html` and install it on your computer. It’s a large file, around 2GB, because it has the Raspbian OS ready to install on your SD Card;
- Download the configuration file: `http://get.pharoiot.org/pibakeryPharoIoT.xml` and use the button Import in PiBakery to import these configurations;
- Change your hostname and WiFi configuration in PiBakery;
- Insert the SD card into your machine, click Write and select the Operation System Raspbian Full;
- After the process, insert the SD into Raspberry and wait about 3 minutes to complete the automatic configuration. Time depends on the speed of your internet;
- You can now find your Raspberry by the Hostname you defined above.


You do not need to do anything else on your Raspberry Pi. It is already loaded with Pharo IoT starting at every boot on the TCP port 40423. Now you can use the Pharo IoT client in your computer to connect in your Raspberry and create IoT applications remotely. 

### Run Pharo IoT IDE on your Linux, Windows or Mac OSX computer.


At this point, you should have the Pharo IoT running on your Raspberry Pi, as we saw in the previous tutorial. Now we will see how to run Pharo IoT IDE on Windows, Linux, and Mac. In this way, you can connect in you Raspberry using Pharo IoT IDE to write your applications on a remote environment.

#### Download


We think of everything to make your IoT journey so much easier. To use the Pharo IoT IDE, you just need to download one zip file, extract and run the application pharo-ui.

```
get.pharoiot.org/multi.zip
```


You can download this same file in your Windows, Linux or Mac OSX. After download, double click on pharo-ui \(Linux, Mac\) or pharo.bat \(Windows\).

#### Pharo User Interface


When you run pharo-ui or pharo.bat, maybe some security warning will be shown. Just allow to run the program and Pharo will open as shown in Picture *@pharoUi@*;

![User interface of Pharo.](figures/pharothings-pharoiot-pharoide.png width=90&label=pharoUi)

You can see a quickstart guide window with some code to copy and past in Playground. You can also click in Open in Playground below the code to open a new playground with that code.

#### Connecting from your computer to Raspberry Pi


If you followed the tutorial to run Pharo IoT in you Raspberry, you should have now your Raspberry connected in your WiFi network running TelePharo in TCP 40423 port.

Now with the server running in your Raspberry, you can connect your local Pharo in it. In your local machine, open the Playground and connect using the Raspberry IP address or the Hostname that you set before. 

#### Connecting in Pharo IoT server by Hostname


```
ip := NetNameResolver addressForName: 'pharoiot-01'.
remotePharo := TlpRemoteIDE connectTo: (TCPAddress ip: ip port: 40423).
```


#### Connecting in Pharo IoT server by IP


```
remotePharo := TlpRemoteIDE connectTo: (TCPAddress ip: #[192 168 1 200] port: 40423).
```


#### Working remotely


If you don’t receive any error, this means that you are connected. Now you can call the Remote Playground, Remote System Browser, and Remote Process Browser:\)

```
remotePharo openPlayground.
remotePharo openBrowser.
remotePharo openProcessBrowser.
```


#### Inspect the remote Raspberry Pi GPIO board


You can inspect the physical board of your Raspberry Pi. So for your board model you need to choose an appropriate board class. For Raspberry, it will be one of the RpiBoard subclasses. Currently, you can use the following classes according to the models:

- RpiBoardBRev1: Raspberry Pi Model B Revision 1
- RpiBoardBRev2: Raspberry Pi Model B Revision 2
- RpiBoard3B: Raspberry Pi Model B+, Pi2 Model B, Pi3 Model B, Pi3 Model B+


With the chosen class evaluate the following code to open an inspector:

```
remoteBoard := remotePharo evaluate: [ RpiBoard3B current].
remoteBoard inspect.
```


And will be open the inspector showing the PIN scheme, as shown in Figure *@PharoThingsinspector@*

![Remote GPIO inspector](figures/PharoThingsInspector.png width=70&label=PharoThingsinspector)

#### GPIOs


The board inspector shows a layout of pins similar to Raspberry Pi docs. But here it is a live tool which represents the current pins state. The evaluation pane in the bottom of the inspector provides bindings to gpio pins which you can script by #doIt/printIt commands, as shown in Figure *@PharoThingsinspector@*.

Digital pins are shown with green/red icons which represent high/low \(1/0\) values. In case of output pins you are able to click on the icon to toggle the value. Icons are updated according to pin value changes. If you click on physical button on your board the inspector will show the updated pin state by changing its icon color.

#### Saving the remote image


```
remotePharo saveImage.
```


#### Disconnect all remote sessions


```
TlpRemoteIDE disconnectAll.
```


Now that you are connected remotely in your Raspberry Pi, you can use sensors, buttons, LEDs, send data to the Cloud and create powerful applications.

### In the next chapter


Now that we have installed the Operation System and Pharo IoT on Raspberry Pi, we can play with LEDs, sensors, LCD displays and more. 

In the next chapter we will see how turning on/off a LED using PharoThings.
