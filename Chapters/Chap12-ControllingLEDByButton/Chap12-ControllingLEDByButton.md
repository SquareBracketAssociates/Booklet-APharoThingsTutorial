## Controlling LED by Button
GTInspector enableStepRefresh.
remoteBoard := remotePharo evaluate: [ RpiBoard3B current].
remoteBoard inspect.
led beDigitalOutput.
button beDigitalInput.
button enablePullUpResister.
    led value: (button value=1) asBit
        ] repeat    
     ] forkNamed: 'button process'.