# Controling an analog clock with an esp32

## Why
We were given a very cool analog clock that we want to hang in a place that would make annoying to adjust twice a year for daylight savings time.  
This seemed like a good opportunity to come up with something overly complicated and fun to remotely control an analog clock that could automatically adjust the clock for daylight savings time and keep the clock's time adjusted throughout the year. 

## Understanding how a clock works. 

First I disassembled the clock and soldered two wires at the point where the clock control circuit is connected to the coil.   I wanted to reproduce the signal being sent by this circuit using the esp32. 

![alt text][tapped-in]

[tapped-in]: https://github.com/moxiknox/clock/blob/main/photos/tapped-in.jpg "wires tapped into clocks circuit"

![alt text][reassembled]

[reassembled]: https://github.com/moxiknox/clock/blob/main/photos/assembled.jpg "wires tapped into clocks circuit and reassembled"

To these two wires I connected an oscilliscope and found this signal: 
Approximately +1.3v / -1.3v every 1 second
![alt text][signal]

[signal]: https://github.com/moxiknox/clock/blob/main/photos/signal.jpg "oscilliscope signal"



## Reproducing the signal

To reproduce that signal I'm using a stepper motor driver.  
![alt text][motor-driver]

[motor-driver]: https://github.com/moxiknox/clock/blob/main/photos/motor-driver.png "motor-driver"

Driven from the esp32 3.3v vout and two gpio pins this motor driver will produce a +3.3v and -3.3v signal at the frequency I needed.   Unfortuantely that at that voltage the movement of the clock is quite violent.  I was concerned about the longevity of the mechanisim.

To step down that voltage I used 51ohm and 47ohn resistors to create voltage dividers. This stepped the voltage down to 1.7v.

## Assemebed prototype

The fully assembled prototype.  The clock tapped wires would be connected to the two little red wires in the bottom left corner. 
![alt text][prototype]

[prototype]: https://github.com/moxiknox/clock/blob/main/photos/prototype.jpg "Assembled prototype"


