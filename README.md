# Cellular Consumer Electronics
### Purpose
The range on my car starter is rather long, maybe 500ft line-of-sight. The purpose of the project is more of an exploration of the potential benefits of having my keyfob connected to the cloud. There are limitation baked into the design of the keyfob as is was initially presented:
- I have to be in range
- I have to physically press a clicky button
- I have to preemtivly press the button before my trip
- ...

These limitations should be taken with a grain a salt. Having the option to start a car without actually being in the car is awesome. It works great (if I remember to have the heat on when I get out), its not buggy, there is zero barrior to entry. There is a lot to like about the design.
My real motivation was partly the chase to do something I hadn't seen anyone publically do yet, and have something in the physical space to test out what API's could do with it. The IoT! 
### Steps Taken to Take Back Control
##### 1. Turn Clicky Buttons into Solid State Buttons
For this step, I took apart my spare key fob and laid out all the pieces. The fob uses two coil cells (+6V), and a PCB pad closure to connect the different antenna circuits. 
![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/29268450314_26b81df367_o.jpg)
The first tricky part in making the switchover was in understanding how [NPN transitors](https://en.wikipedia.org/wiki/Bipolar_junction_transistor) behave in this context. The goal is to have a persistent connection between the hot and ground connections but have a way to open and close the connection between the two sides electronically. NPN transitors are perfect for this. They offer a base pin that, when voltage is present, saturates the two sides and opens the circuit connction. 

**If you would like to play around with this, then checkout the Circuit Playground I setup as a proof-of-concept [circuit.io/transitor-gate-test](https://circuits.io/circuits/2788834-trasitor-gate-test)**

If you look carefully, every one of the pads has the same shape. The circular shape in the middle is the hot and the outside part connects to ground. In the photo I had one of the transitors soldered in backwards (I switched it when I noteced strange behavior). It was also the first time I soldered in a while so if you attempt to hack your own electronics you don't actually need mountains of solder. The only imporatant part in wiring these components is that the collector is attached to the hot pad and the emitter to ground.
![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/30944340735_527af00ef0_o.jpg)
##### 2. Prepare the Micro Controller
