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


## Software

This esp32 is being controlled by my home assistant installation and programmed by esphome.  
This lets me leverage an excellent ecosystem of libraries and features, namely:
* ntp time
* read / write variables from webui / mobile app

The software doesn't know the position of the hands, it only tracks the number of ticks (time_position) since midnight in a variable.  From home assistant I can update a variable clock_position which will tell the esp32 the current position of the hands.  Using fast and slow adjustments the esp32 will correct the time until the position of the hands match the current time.  

When daylight savings comes the clock with either wait one hour before resuming movement or, for one hour each second the clock will tick twice.  I found that any faster will cause too much blocking on the esp32 and destabilze the flow, it also risks the clock missing a tick or tock which would be mildly disasterous and not worth the risk.

### Flow
* Every 5s the current time is converted to "ticks" (total_tick) and compared to time_position. Depending on the outcome of that comparision variables are updated to indicate that the clock state is, fast, slow, or perfect.
* Every 2s check if variable from home assistant (clock_position) is set, if so set time_position = clock_position.
* Every 1s
  * check if the state is fast, if so do not tick
  * tick up to 2 times ( once in the normal case, twice if the current state is slow)

