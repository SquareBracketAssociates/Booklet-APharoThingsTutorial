## I2C Sensors
reboot
GTInspector enableStepRefresh.
remoteBoard := remotePharo evaluate: [ RpiBoard3B current].
remoteBoard inspect.
temperature.
b readTemperature.
c readCoordinates.