<h1> Review of Hamamastu C128880 Microspec Module </h1>

Craig Versek (Northeastern University) </br>
Parker Woodworth (Food+Future) </br>
Alex Meckes (Wyss Institute) </br>
Don Blair (Food+Future)

# Summary

The C12880MA has is an easy-to-use, highly portable sensor module, useful in the range of x to y nm.  We believe that the currently available datasheet for the C12880MA gives the impression that the device requires more sophisticated hardware than is in fact needed in order to effectively operate the device in a wide range of applications; two independent engineering teams initially avoided using it because it seems to require a protocol speed only available on custom FPGA chips.

Below, we demonstrate a basic application of the C12880MA that uses a $8 hobby microcontroller to effectively produce transmission spectra.  We recommend that the language in the datasheet and advertising be modified to reflect how truly accessible the module is, thus making the module immediate more appealing to a wider audience. 

# First impression: is our hardware too slow?

In our evaluation of the C12880MA module, we relied on the datasheet available [here](http://www.hamamatsu.com/resources/pdf/ssd/c12880ma_kacc1226e.pdf).  

The timing chart on page 7 of the C12880MA datasheet strongly suggests that a regular clock pulse is needed at all times in order to operate the device.  

<img src="pics/timing.png">

Our first impression (shared with another engineering team at MIT) was that reading off pixel values from the C12880MA with a microcontroller ADC would therefore need to be performed within this ongoing clock pulse schedule -- a constraint that would have required a faster and more advanced microcontroller architecture than any of us was comfortable developing. 

We eventually discovered [example code](https://github.com/groupgets/c12880ma), provided by [GroupGets](http://groupgets.com), for operating the C12880MA using the Arduino microcontroller.  After successfully running this code, we realized that we had misinterpreted the datasheet:  after an initial sequence of pulses to prepare the device and set the integration time, the C12880MA then allows individual pixel values to be read off via the ADC at whatever pace is convenient -- a constant clock pulse is not required.  

After discovering and studying this example, we were able to write our own C and Python code (described below) to control the device.

# Low-cost, accessible microcontroller setup for the C12880MA

Here we present code and hardware for interfacing with the C12880MA using low-cost microcontrollers.  The code we developed is compatible with a wide range of microcontrollers that utilize the [Arduino platform](http://arduino.cc).  Our setup uses the [Adafruit Feather](https://www.adafruit.com/feather):

<img src="pics/feather.png">

-- but our code is also compatible with:

- [Adafruit Feather](http://adafruit.com/feather) ($15) (tested)
- [Arduino Pro Mini](https://www.sparkfun.com/products/11113) ($10) (tested)
- The [Teensy 3.2](https://www.adafruit.com/product/2756?gclid=CjwKEAjwrMzHBRDW3saA88aT80MSJACbvo1TqquoNXciYPJN9wzcpkCm3hBvtnrP2_X41iHtpxki0BoCY4Hw_wcB) ($20) (tested)
- Other microntrollers compatible with the [Arduino IDE](https://www.arduino.cc/en/main/software) (untested)

# Wiring diagram

Our basic setup involves using a low-cost microcontroller to interface directly with the C12880MA.  Such a microcontroller can then be programmed to store spectral data locally to an SD card, broadcast spectral data over radio, over send values over a serial connection.  

We opted for the latter option:  we used the [SerialCommand]() protocol to send commands to the microcontroller, via a Python script that interacts with the relevant serial port on a laptop. The laptop is connected via USB serial to the microcontroller; the microcontroller is connected via wires to the microspec module, as per the C12880MA datasheet:

<img src="pics/wiring.png" width=800>

# Serial control via Python; Jupyter notebook analysis

Our 
[Custom firmware](https://github.com/open-eio/arduino-microspec) consists of Arduino code to control the C12880MA via its custom protocol:

- [c12880.cpp](https://github.com/open-eio/arduino-microspec/blob/master/microspec/c12880.cpp)
- [c12880.h](https://github.com/open-eio/arduino-microspec/blob/master/microspec/c12880.h)

As well as Arduino code to wrap this protocol, and allow for serial communication over USB: 

- [microscpec.ino](https://github.com/open-eio/arduino-microspec/blob/master/microspec/microspec.ino)

And, finally, Python code to interact with the serial protocol:

- [test.py](https://github.com/open-eio/arduino-microspec/blob/master/microspec/test.py)

# First Test:  flourescent bulb

Our first test of the C12880 was using a flourescent bulb, to see if we could capture a characteristic spectrum.  We used a [Jupyter notebook for capturing and analyzing the spectrum)](https://github.com/open-eio/arduino-microspec/blob/master/notebooks/jup_spec.ipynb).  The notebook allows for running the Python code above, which interacts with the C12880 module via USB.  

First, the notebook imports the relevant modules, and sets the integration time on the C12880 device:

<img src="pics/jup_setup.png">

Then, we generating the proper wavelength vector according to the calibration fitting parameters provided by Hamamastu, and capture and plot the spectrum:

<img src="pics/jupyter.png">

# Varying the integration time

We also used a similar setup in [another Jupyter notebook](https://github.com/open-eio/arduino-microspec/blob/master/notebooks/test.ipynb) to test our ability to vary the integration time in a straightforward way:

<img src="pics/jupyter_integrations.png">

# 3D Printed Transmission Spectroscopy Enclosure for the C12880MA

In order to perform some basic transmission spectroscopy experiments, we designed and printed a 3D enclosure for a protoboard version of our C12880 setup. The enclosure accomodates a vial that can be found on Amazon [here](http://www.discountvials.com/6-dram-glass-vial-w-cap-pkg-of-25/).

<img src="pics/vial.png"> <img src="pics/vial_dim.png">

We used [Onshape](http://onshape.com) to design the files -- these files are available for copying / modification [here](https://cad.onshape.com/documents/739ac82547d2fdc8c1c24add/w/56cff6a6baf330ba819338be/e/9e36cc1a7c1a2250e4320c5f).

<img src="pics/onshape.png">

The resultant 3D STL files (suitable for 3D printing) are also available online [here](https://github.com/food-future/spectrometer-enclosure-demo).

<img src="pics/body.png" width=300>

<img src="pics/lid.png" width=300>

We printed and assembled the 3D enclosure, including the protoboard with the C12880 module, and a USB cable for connecting to a laptop:

<img src="pics/3d.png" width=300>

The enclsoure also contains an inexpensive [Xenon bulb](
https://www.grainger.com/product/39A485?gclid=Cj0KEQjwiI3HBRDv0q_qhqXZ-N4BEiQAOTiCHvnqqGzGo3s8TXBJ5jYMR4ZyNdZsh2NQYnX4sIPMDyQaAnwu8P8HAQ&cm_mmc=PPC:GOOGLEPLAA-_-Lighting-_-Flashlights-_-39A485&AL!2966!3!166587837076!!!g!82128952917!&s_kwcid=AL!2966!3!166587837076!!!g!82128952917!&ef_id=WHJ-xAAAAIe7WhKm:20170404232517:s) as a light source:

<img src="pics/bulb.png">

# Basic Applications

A first test of our transmission spectroscopy setup using the C12880 involved testing vials of various alcoholic beverages.  In this case, our 'blank' was a vial container Cambridge tap water.

<img src="pics/beer.png">

Our final experiment involved assessing the attentuation of various oils, intending to compare various olive oils with canola oil:

<img src="pics/oils1.png" width=550>


# Conclusion

The C12880MA is a convenient, easy to use spectroscopy module.  It can be readily used with low-cost microcontrollers, and data can be obtained and analyzed using open source code running on a wide range of computer platforms.


