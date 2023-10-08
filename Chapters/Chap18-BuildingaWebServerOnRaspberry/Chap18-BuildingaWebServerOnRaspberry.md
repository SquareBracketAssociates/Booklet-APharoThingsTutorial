## Building a Webserver on Raspberry


In previous lessons, we learned how to control LEDs, sensors, LCD displays and how to use OOP to build a Mini-Weather Station. 
Now we are going to build a Webserver to interact with GPIOs.
We will used the HTTP server named Zinc available by default in Pharo. 
For more complex servers, Pharoers uses either Teapot a layer on top of Zinc or Seaside a full web application server.

We are going to build the webserver and pages together and you are going to link the page with an LED and BME280 sensor in a exercise.

### What do we need?


We will use the BME280 I2C sensor and an LED to be triggered through the web page.

#### Components


- 1 Raspberry Pi connected to your network \(wired or wireless\)
- 1 Breadboard
- 1 LED
- 1 BME280 temperature, humidity and pressure sensor
- Jumper wires


### Experimental procedure


First of all, you have to create a class to implement the web app for the weather station.

```
Object << #WeatherStationWebApp
   package: 'PharoThings-Lessons'
```


and implement the following methods:

The method `handleRequest:` will generate an answer that consists in an HTML page
that we define just after. 
```
WeatherStationWebApp >> handleRequest: request
	request uri path = #sensors
		ifFalse: [ ^ ZnResponse notFound: request uri ].
	^ ZnResponse ok: (ZnEntity html: self html)
```


```
WeatherStationWebApp >> temperature
	^ 0
```


```
WeatherStationWebApp >> pressure
   ^ 0
```


```
WeatherStationWebApp >> humidity
   ^ 0
```


```
WeatherStationWebApp >> value: request
	^ self handleRequest: request
```


### HTML page

We define now the HTML that will be returned by the server. 

```
WeatherStationWebApp >> html
   ^ '<html>
<head>
    <title>Mini Weather Station</title>
    <!-- Bootstrap core CSS -->
    <link href="https://getbootstrap.com/docs/4.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <main role="main">
        <section class="jumbotron text-center">
            <div class="container">
                <h1 class="jumbotron-heading">Mini Weather Station</h1>
                <p class="lead text-muted">Temperature: ', self temperature printString ,'°C</p>
                <p class="lead text-muted">Humidity: ', self humidity printString ,'%</p>
                <p class="lead text-muted">Pressure: ', self pressure printString ,' hPa</p>
                <p class="lead text-muted">Time: ', Time now print24 ,'</p>
                <p><a href="#" class="btn btn-success my-2">Switch button</a>
                </p>
            </div>
        </section>
    </main>
    <!-- Bootstrap core JavaScript -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js"></script>
</body>
</html>'
```


### Starting the server


Then you can start the Zn webserver in remote playground like that:

```
sensorServer := WeatherStationWebApp new.
ZnServer startDefaultOn: 8080.
ZnServer default delegate map: #sensors to: sensorServer.
```


You can access the page using your web browser in the url: http://ip-of-raspberry:8080/sensors

This page looks like the Picture *@WebPage@*. 

![Web Page.](figures/pharothings-webpage.png width=85&label=WebPage)

### Calling your program methods from the web page


As you saw, we have a button inside the page to switch something \(the button is not working yet\). Now we are going create some methods to make this button works. 

First step, create 1 instance variable `ledValue`. We are going to use this variable to keep the LED value when we click on the webpage button:

```
Object << #WeatherStationWebApp
   slots: { #ledValue}; 
   package: 'PharoThings-Lessons'
```


Now let's create a method to change the value of this variable when it is called:

```
WeatherStationWebApp >> toggleLED
   ledValue = 1
      ifTrue: [ ledValue := 0 ]
      ifFalse: [ ledValue := 1 ]
```


Let's create too a method to change the color of the button:

```
WeatherStationWebApp >> buttonColor
   ^ ledValue = 1
      ifTrue: [ 'success' ]
      ifFalse: [ 'danger']
```


And we need to call this method on the HTML code, inside the method html. So replace the line of the html button code adding a new method calling, line 17 of WeatherStationWebApp >> html:

```
<p><a href="switchLED" class="btn btn-', self buttonColor,' my-2">Switch button</a></p>
```


We are creating a new path `#switchLED` to be used to call the method to change the value of an LED. It is calling the method `switchLED` and returning the same webpage `sensors`:

```
WeatherStationWebApp >> handleRequest: request
   request uri path = #sensors
      ifTrue: [ ^ ZnResponse ok: (ZnEntity html: self html) ].
   request uri path = #switchLED
      ifTrue: [ 
         self toggleLED.
         ^ ZnResponse redirect: #sensors ].
   ^ ZnResponse notFound: request uri 
```


Now we need to map the new path to our class. To do that run this command in the remote playground:

```
ZnServer default delegate map: #switchLED to: sensorServer.
```


And now access you page and you can see the button changing the color when you click on it. 

### Exercises


Now we have the web server running on the Raspberry Pi and showing you a webpage. But all the values are hardcoded. Your mission is integrate it with the BME280 sensor and an LED.

1 - Add the instance variables `sensor` and `led` in the class. You will use it to instanciate the sensor and the LED.

2 - Create the method initialize to start a instance of the sensor and other of LED.

3 - Replace the hardcode in the methods `temperature`, `pressure` and `hunidity` to the `sensor` object.

4 - Add the `led` object in the method `toggleLED` and make the phisical LED turn on/off when you press the button on the webpage.

Now you should have your page showing the value of the sensor \(temperature, humidity and pressure\) and turning on/off the LED using the webpage. 








