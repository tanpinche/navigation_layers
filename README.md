Repo was forked to make some changes for use case when multiple sensors with non overlapping areas of detection are used. 

How layered costmap works:
each sensor has its own 2dcostmap layer. It updates that layer with information from each sensor reading.

The layered costmap updates by compiling all layers together. 

The navigation layer is written such that the method of combination is inherently maximum, meaning the maximum of the ultrasonic's layer and the preceeding layers are taken. 

This is so that obstacles detected in areas outside the ultrasonic's vision are not cleared by the zeros in the ultrasonic sensor's layer.

However there is a relatively common case scenario where this leads to problems. 

Scenario:
There is an obstacle outside of the ultrasonic sensor's detection area, and is detected by another sensor. 
ultrasonic sensor layer: no obstacle detected
other sensor: obstacle detected in area
mastercostmap: obstacle detected

the robot moves such that the obstacle is now within the ultrasonic sensor's detection area, and outside the other sensor's detection area.
ultrasonic sensor layer: obstacle detected
other sensor: obstacle detected
mastercostmap: obstacle detected

The obstacle is now moved-> the other sensor is unaware of this move and in its costmap, the obstacle is still present. Ultrasonic sensor detects the removal of obstacle.
ultrasonic sensor layer: no obstacle
other sensor: obstacle detected
mastercostmap: obstacle detected.


Code is added now to mark each cell detected in the most recent reading taken by the ultrasonic sensor.

When combining layers, the cells which have been recently updated will take precedence of other layers.

This means that the ultrasonic layers will keep updated information. and avoid the error above.
