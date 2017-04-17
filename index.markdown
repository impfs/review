<h1> Review of Hamamastu C128880 Microspec Module </h1>

Craig Versek (Northeastern University; EIO) </br>
Parker Woodworth (Food+Future; Poly) </br>
Alex Meckes (Wyss Institute) </br>
Don Blair (Food+Future; Illuminate; IMPFS)

# Summary

The C12880MA has is an easy-to-use, highly portable sensor module, useful in the range of x to y nm.  We believe that the currently available datasheet for the C12880MA gives the impression that the device requires more sophisticated hardware than is in fact needed in order to effectively operate the device in a wide range of applications; two independent engineering teams initially avoided using it because it seems to require a protocol speed only available on custom FPGA chips.  Below, we demonstrate a basic application of the C12880MA that uses a $8 hobby microcontroller to effectively produce transmission spectra.  We recommend that the language in the datasheet and advertising be modified to reflect how truly accessible the module is, thus making the module immediate more appealing to a wider audience. 

# First impression: we're too slow?

In our evaluation of the C12880MA module, we relied on the datasheet available [here](http://www.hamamatsu.com/resources/pdf/ssd/c12880ma_kacc1226e.pdf).  

The timing chart on page 7 of the C12880MA datasheet strongly suggests that a regular clock pulse is needed at all times in order to operate the device.  

<img src="pics/timing.png">

Also: on the top of page 8 of the dataset, the example given uses the fastest integration time possible on the device. For those unfamiliar with the operation of spectroscopy modules (as we were), this exmaple may give the (false) impression that such timings are necessary in order to operate the device in all cases.  

The bottom of page 8 on the datasheet does indicate that the device the integration time is equal to the high period of the start pulse plus 48 cycles of clock pulses -- which is a clue that the integration time is capable of being varied, and that slower integration times, accessible by less powerful hardware, are possible. In retrospect, this was a strong (but insufficiently clear) clue that the module can be controlled by lower-power devices.

We eventually discovered [example code](https://github.com/groupgets/c12880ma), provided by [GroupGets](http://groupgets.com), for operating the C12880MA using the Arduino microcontroller.  After successfully running this code, we realized that we had misinterpreted the datasheet:  the C12880MA does, in fact, allow for slower microcontrollers to capture spectra, so long as faster integration times are not required; and a constant clock pulse is not necessary.  After an initial clocking phase that sets the integration time, the user can then use an ADC to measure the individual pixel values at whatever rate is convenient. 

For many applications, slower integration times may be sufficient;  we believe that updating the language in the datasheet to explain the protocol more fully -- perhaps including an additional example that uses slower integration times, appropriate for lower-power microcontrollers -- widen the potential audience for the sensor module.  

After discovering, and studying, this example, we were then able to write our own C and Python code (described below) to control the device.

# Low-cost, low-power microcontroller setup for the C12880MA

Here we present code and hardware for interfacing with the C12880MA using low-cost microcontrollers.  The code we developed is compatible with a wide range of microcontrollers that utilize the [Arduino platform](http://arduino.cc).  Our setup uses the [Adafruit Feather](https://www.adafruit.com/feather):

<img src="pics/feather.png">

but our code is also compatible with:

- [Adafruit Feather](http://adafruit.com/feather) ($15) (tested)
- [Arduino Pro Mini](https://www.sparkfun.com/products/11113) ($10) (tested)
- The [Teensy 3.2](https://www.adafruit.com/product/2756?gclid=CjwKEAjwrMzHBRDW3saA88aT80MSJACbvo1TqquoNXciYPJN9wzcpkCm3hBvtnrP2_X41iHtpxki0BoCY4Hw_wcB) ($20) (tested)
- Other microntrollers compatible with the [Arduino IDE](https://www.arduino.cc/en/main/software) (untested)

## Wiring diagram.  

[pic]

## Serial control of C12880MA via Python + Jupyter notebook analysis.  

[Custom firmware](https://github.com/open-eio/arduino-microspec).

Arduino code:

[c12880.cpp](https://github.com/open-eio/arduino-microspec/blob/master/microspec/c12880.cpp)

[c12880.h](https://github.com/open-eio/arduino-microspec/blob/master/microspec/c12880.h)

[microscpec.ino](https://github.com/open-eio/arduino-microspec/blob/master/microspec/microspec.ino)

Python code to interact with the C code:

[test.py](https://github.com/open-eio/arduino-microspec/blob/master/microspec/test.py)

[Simple test with florescent bulb (Jupyter notebook)](https://github.com/open-eio/arduino-microspec/blob/master/notebooks/jup_spec.ipynb)

[Varying Integration time test](https://github.com/open-eio/arduino-microspec/blob/master/notebooks/test.ipynb)

### Example using Jupyter to plot the spectrum of a florescent bulb:

<img src="pics/jup_setup.png">

<img src="pics/jupyter.png">

### Example testing integration times:

<img src="pics/jupyter_integrations.png">

# 3D Printed Transmission Spectroscopy Enclosure for the C12880MA

[STL files for enclosure](https://github.com/food-future/spectrometer-enclosure-demo)

<img src="pics/onshape.png">

[Onshape file](https://cad.onshape.com/documents/739ac82547d2fdc8c1c24add/w/56cff6a6baf330ba819338be/e/9e36cc1a7c1a2250e4320c5f)

<img src="pics/body.png" width=300>

<img src="pics/lid.png" width=300>

<img src="pics/3d.png" width=300>

[Xenon bulb](
https://www.grainger.com/product/39A485?gclid=Cj0KEQjwiI3HBRDv0q_qhqXZ-N4BEiQAOTiCHvnqqGzGo3s8TXBJ5jYMR4ZyNdZsh2NQYnX4sIPMDyQaAnwu8P8HAQ&cm_mmc=PPC:GOOGLEPLAA-_-Lighting-_-Flashlights-_-39A485&AL!2966!3!166587837076!!!g!82128952917!&s_kwcid=AL!2966!3!166587837076!!!g!82128952917!&ef_id=WHJ-xAAAAIe7WhKm:20170404232517:s)

<img src="pics/bulb.png">

# Basic Applications

<img src="pics/beer.png">

<img src="pics/oils1.png" width=550>



