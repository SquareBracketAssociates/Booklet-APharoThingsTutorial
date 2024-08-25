## Flowing Lights
GTInspector enableStepRefresh.
remoteBoard := remotePharo evaluate: [ RpiBoard3B current].
remoteBoard inspect.
gpioArray do: [ :item | item beDigitalOutput ].
gpioArray do: [ :item | item toggleDigitalValue. (Delay forSeconds: 0.3) wait ]
] forkNamed: 'FlowingProcess'.
    gpioArray do: [ :item | item toggleDigitalValue. (Delay forSeconds: 0.1) wait ].
] ] forkNamed: 'FlowingProcess'.
    gpioArray reverseDo: [ :item | item toggleDigitalValue. (Delay forSeconds: 0.1) wait ].
] ] forkNamed: 'FlowingProcess'.
    gpioArray do: [ :item | item toggleDigitalValue. (Delay forSeconds: 0.1) wait ].
    gpioArray reverseDo: [ :item | item toggleDigitalValue. (Delay forSeconds: 0.1) wait ].
] ] forkNamed: 'FlowingProcess'.
[ 2 timesRepeat: [
    gpioArray do: action.
    gpioArray reverseDo: action.
] ] forkNamed: 'FlowingProcess'.
flowing := [ 2 timesRepeat: [
    gpioArray do: action.
    gpioArray reverseDo: action.
] ].
gpioArray do: [ :item | item beDigitalOutput ].
action := [ :item | item toggleDigitalValue. (Delay forSeconds: 0.1) wait ].
flowing := [ 2 timesRepeat: [
	gpioArray do: action.
	gpioArray reverseDo: action.
] ].
flowing forkNamed: 'FlowingProcess'.