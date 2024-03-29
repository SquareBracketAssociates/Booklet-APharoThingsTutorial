## PharoThings App Architecture


We are going to see how is the architecture of an application developed using PharoThings. We are going to see how is easy to inspect objects remotely and how powerful this can be.

In this chapter, we will see the example application `PotWaterAlarm`, an application to monitor the humidity of the air and turn on/of an LED according to the percentage of humidity. This application is already created and is part of the PharoThings package.

### Connecting remote


Through your local Pharo image, let’s connect in the Pharo image running on Raspberry and open the `Remote Playground`. Run this code in your local playground:

```
remotePharo := TlpRemoteIDE connectTo: (TCPAddress ip: #[192 168 1 106] port: 40423).
remotePharo openPlayground.
```


### Installing the sensor and LED in the board


In the PharoThings application architecture, you need to have in mind that we install the sensors and other devices in a GPIO board. This means that you need to `installDevice` like a sensor or LED, in a board model, like `RpiBoard3B`. See figure *@SensorBME@*

![Sensor BME.](figures/image-1.png width=40&label=SensorBME)

In this example, we can have this logical of code:

```
bmeDevice := (RpiBoard3B current) installDevice: (PotBME280Device new).
```


We don’t need these parentheses, they are here only to become a bit easier to understand the logic of code. We are creating a new instance of the BME280 sensor `PotBME280Device` and installing it in the Raspberry 3B board `RpiBoard3B`, keeping it the variable `bmeDevice`. In this point, we can ask the object what is the humidity or temperature for example:

```
bmeDevice readHumidity.
bmeDevice readTemperature.
```


We can see the result just selecting the line and `Print it` using `CMD + P`. See figure *@SensorBMEPayground@*

![Sensor BME Playground.](figures/image-2.png width=90&label=SensorBMEPayground)

Let’s inspect this object and see what we have. Select the variable `bmeDevice` and `Inspect it`, `CMD + I`. See figure *@SensorBMEInspector@*

![Sensor BME Inspector.](figures/image-3.png width=70&label=SensorBMEInspector)

We can see the instance variables of the object and which board it is using. Note that in the value of the `board` variable we can see the IP and port of that board. This means we can use sensors of other remote boards in our application.

Follow the same logic, let’s create an instance of an LED. We need just to ask to the board the GPIO we want to use. See figure *@SensorLED@*

![LED.](figures/image-4.png width=40&label=SensorLED)

```
ledDevice := (RpiBoard3B current) pinLike: 4 gpio.
```


We are creating a new instance of the GPIO number 4 \(`4 gpio`\) using the Raspberry 3B board `RpiBoard3B`, keeping it the variable `ledDevice`. In this point, we can use the object and turn the LED on/off, for example. See figure *@SensorLEDPlayground@*

```
ledDevice beDigitalOutput.
ledDevice value: 1.
```


![LED Playgound.](figures/image-5.png width=80&label=SensorLEDPlayground)

Let’s inspect this object and see what we have. Select the variable `ledDevice` and `Inspect it`, `CMD + I`. See figure *@SensorLEDInspector@*

![LED Inspector.](figures/image-6.png width=70&label=SensorLEDInspector)

We can see the instance variables of this object, which `board` we are using, the `ioMode` of the pin and the gpio number of it.

### Initializing the alarmDevice


Now we are going to initialize the `alarmDevice`. In theory, it is an object representing your alarm device physically. So let’s initialize it with the objects we have. See figure *@AlarmDevice@* and *@AlarmDevicePlayground@*

```
alarmDevice := PotWaterAlarm tracking: bmeDevice signaling: ledDevice.
```


![Alarm Device Architecture.](figures/image-7.png width=70&label=AlarmDevice)

![Alarm Device Playground.](figures/image-8.png width=80&label=AlarmDevicePlayground)

Let’s inspect it again. We can see the instance variables with the objects that we create before and navigate inside they, type some commands in the inspector and see how they are working. See figure *@AlarmDeviceInspector@*

![Alarm Device Inspector.](figures/image-9.png width=90&label=AlarmDeviceInspector)

### Initializing the alarmApp


But the application still doesn’t work, you can see the `board` and `trackingProcess` variables empty. For last we need to install the alarmDevice in some board to bring life to our application and control it.

Let’s continue the logic and install the `alarmDevice` in the Raspberry board and put this in a variable `alarmApp`. See figure *@AlarmApp@* and *@AlarmAppPlayground@*

```
alarmApp := (RpiBoard3B current) installDevice: alarmDevice.
```


![Alarm App.](figures/image-10.png width=90&label=AlarmApp)

![Alarm App Playground.](figures/image-11.png width=80&label=AlarmAppPlayground)

And now we inspect the object `alarmApp` and we can see which board it is using and the process running. We can also navigate to the sensor and LED objects and type some code on it. See figure *@AlarmAppInspector@*

![Alarm App inspector.](figures/image-12.png width=100&label=AlarmAppInspector)

### Controlling the application


You can see the remote process running in your remote Pharo in Raspberry Pi, calling the Remote Process Browser. Type this command in your local playground:

```
remotePharo openProcessBrowser.
```


And you can see the process running, terminate it, see the code etc. See figure *@AlarmAppProcessBrowser@*

![Alarm App Process Browser.](figures/image-13.png width=70&label=AlarmAppProcessBrowser)

Anyway, the architecture of PharoThings provides some methods for you to better control your application. You can stop and start this application when you want, using the connect and disconnect methods. See figure *@AlarmAppPlaygroundComplete@*

```
alarmApp disconnect.
alarmApp connect.
```


![Alarm App Playground Complete.](figures/image-14.png width=80&label=AlarmAppPlaygroundComplete)

Now you can save your remote image and when you start the Pharo Runtime in your Raspberry Pi, this application is already running.
To save your remote image type in your local playground:

```
remotePharo saveImage.
```
