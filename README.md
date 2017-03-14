# Cellular Consumer Electronics
### Purpose
The range on my car starter is rather long, maybe 500ft line-of-sight. The purpose of the project is more of an exploration of the potential benefits of having my key fob connected to the cloud. There are limitation baked into the design of the key fob as is was initially presented:
- I have to be in range
- I have to physically press a clicky button
- I have to preemptively press the button before my trip
- ...

These limitations should be taken with a grain a salt. Having the option to start a car without actually being in the car is awesome. It works great (if I remember to have the heat on when I get out), its not buggy, there is zero barrier to entry. There is a lot to like about the design. 

My real motivation was partly the chase to do something I hadn't seen anyone publicly do yet, and have something in the physical space to test out what API's could do with it. The IoT!
### Steps Taken to Take Back Control
##### 1. Turn Clicky Buttons into Solid State Buttons
For this step, I took apart my spare key fob and laid out all the pieces. The fob uses two coil cells (+6V), and a PCB pad closure to connect the different antenna circuits. 
![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/29268450314_26b81df367_o.jpg)
The first tricky part in making the switchover was in understanding how [NPN transitors](https://en.wikipedia.org/wiki/Bipolar_junction_transistor) behave in this context. The goal is to have a persistent connection between the hot and ground connections but have a way to open and close the connection between the two sides electronically. NPN transistors are perfect for this. They offer a base pin that, when voltage is present, saturates the two sides and opens the circuit connection. 

**If you would like to play around with this, then checkout the Circuit Playground I setup as a proof-of-concept [circuit.io/transitor-gate-test](https://circuits.io/circuits/2788834-trasitor-gate-test)**

If you look carefully, every one of the pads has the same shape. The circular shape in the middle is the hot and the outside part connects to ground. In the photo I had one of the transistors soldered in backwards (I switched it when I noticed strange behavior). It was also the first time I soldered in a while so if you attempt to hack your own electronics you don't actually need mountains of solder. The only important part in wiring these components is that the collector is attached to the hot pad and the emitter to ground. _Those extra wire pieces attached to the groud pad are pieces of staples. There are three in the photo but when I realized that ground is the same everywhere for a circuit I just opted for one in the final prototype._
![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/30944340735_527af00ef0_o.jpg)

##### 2. Prepare the Micro Controller
I used the [particle electron](https://docs.particle.io/guide/getting-started/start/electron/) for this project. It is the more expensive development board that Particle offers. It has an onboard 3G antenna and Particle takes care of all the carrier fees (by that I mean I pay Particle and I don't have to hassle with multiple carriers or weird fees). The actual setup for this board is outside the scope of this doc, but I encourage those interested to checkout the [Docs](https://docs.particle.io/guide/getting-started/intro/electron/) that Particle provides. They do a great job cutting down the real barrier to entry in connected things. 
![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/30855970841_1fe563afc8_o.jpg)
There is a lot to talk about in dealing with the micro controller. A lot more than is relevant in this doc, so I'll just link [the test code repo](https://github.com/larryschirmer/electron_car_hack) and the [video of triggers I setup from the command line](https://www.flickr.com/photos/78229770@N05/30522003470/). In the test, instead of saturating the base pins of the key fob, I have an LED turning on/off. The bottom window in the video is my serial output and the top window is making actual cellular calls to the board and triggering the installed loops. It is a lot of fun and the sky is the limit, but there is no time for that, so lets just keep going here.

##### 3. Putting it all together
Everything works in theory. The project got held up for about a month while I worked through a single bug. I wrote an API that, through a URL endpoint, made calls to the Particle Platform, that called the board over 3G and triggered the onboard function to open the port that saturated the base pin to lock the car doors. The key fob would flash and the board would return "1" meaning "yes, I in fact did all that you asked, what more do you want of me", but the car doors would not lock. Why! I tried [darlington pairs](https://en.wikipedia.org/wiki/Darlington_transistor), I tried more voltage, and several different circuit configurations. You know, once I get on something, I can't let it go. Now that I have had it on my dash for a while I bet I could have just used a 10µF capacitor to smooth the output from the particle board. That is what I'll do before I solder the board into a more permanent proto. My solution was a hack. If one output from the board was not going to be enough, then two output pins will be used. So in the code I have two pins output in parallel to each of the NPN base pins. It's not pretty, a capacitor would be prettier. 
I also opted to use 4 AA batteries rather than the 2 coin cells. I get better Amp Hour capacity and better performance in the cold.

![](https://github.com/larryschirmer/cellular_keyfob/raw/master/pictures/32530252605_afac3fd83b_o.jpg)

### Moving Forward

There are a couple of sensors wired into the board right now: a [Temperature](https://www.adafruit.com/product/165) and [Light](https://www.adafruit.com/product/1384) sensor, but there is so much more I'd like to know about my car while I'm away. One piece primarily, I am asking my car to turn on while I am out of eye shot and have no idea if it did in fact turn on. Using another piece of equipment I plan to hook into the main system of my car and record RPM to check a `didTurnOn` state. Once in, I could have custom dash board report `gasMilesRemaining` or a system of trip recording.
