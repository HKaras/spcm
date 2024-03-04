<div style="margin-bottom: 20px; text-align: center">
<a href="https://spectrum-instrumentation.com">
    <img src="https://spectrum-instrumentation.com/img/logo-complete.png"  width=400 />
</a>
</div>

# spcm
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PyPI - Version](https://img.shields.io/pypi/v/spcm)](https://pypi.org/project/spcm)
[![PyPi Downloads](https://img.shields.io/pypi/dm/spcm?label=downloads%20%7C%20pip&logo=PyPI)](https://pypi.org/project/spcm)
[![Follow](https://img.shields.io/twitter/follow/SpecInstruments.svg?style=social&style=flat&logo=twitter&label=Follow&color=blue)](https://twitter.com/SpecInstruments/)
![GitHub followers](https://img.shields.io/github/followers/SpectrumInstrumentation)

A high-level, object-oriented Python package for interfacing with Spectrum Instrumentation GmbH devices.

`spcm` can handle individual cards (`Card`), StarHubs (`Sync`), groups of cards (`CardStack`) and Netboxes (`Netbox`).

# Supported devices

See the [SUPPORTED_DEVICES.md](https://github.com/SpectrumInstrumentation/spcm/blob/master/src/spcm/SUPPORTED_DEVICES.md) file for a list of supported devices.

# Requirements
[![Python](https://img.shields.io/pypi/pyversions/spcm.svg)](https://badge.fury.io/py/spcm)
[![Static Badge](https://img.shields.io/badge/NumPy-1.25-green)](https://numpy.org/)

`spcm` requires the Spectrum Instrumentation [driver](https://spectrum-instrumentation.com/support/downloads.php) which is available for Windows and Linux. 
Please have a look in the manual of your product for more information about installing the driver on the different plattforms.

# Installation and dependencies
[![Pip Package](https://img.shields.io/pypi/v/spcm?logo=PyPI)](https://pypi.org/project/spcm)

Start by installing Python 3.9 or higher. We recommend using the latest version. You can download Python from [https://www.python.org/](https://www.python.org/).

You would probably also like to install and use a virtual environment, although it's not strictly necessary. See the examples [README.md](https://github.com/SpectrumInstrumentation/spcm/blob/master/src/examples/README.md) for a more detailed explanation on how to use `spcm` in a virtual environment.

To install the latest release using `pip`:
```bash
$ pip install spcm
```
Note that: this will automatically install all the dependencies (e.g. NumPy).

# Documentation
[![Documentation](https://img.shields.io/badge/api-reference-blue.svg)](https://spectruminstrumentation.github.io/spcm/spcm.html)

The API documentation for the latest [stable release](https://spectruminstrumentation.github.io/spcm/spcm.html) is available for reading on GitHub pages.

Please also see the hardware user manuals for your specific card for more information about the provided functionality.

# Using spcm

The `spcm` package is a high-level object-oriented programming library for controlling Spectrum Instrumentation devices.

## Examples
For detailed examples see the `src\examples` directory. There are several sub-directories each corresponding to a certain kind of functionality. See also the examples on [GitHub](https://github.com/SpectrumInstrumentation/spcm/tree/master/src/examples).


## Hardware interfaces

`spcm` provides the following classes for interfacing with the different devices.

| Name        | Description                                                                       |
|-------------|-----------------------------------------------------------------------------------|
| `Card`      | a class to control the low-level API of Spectrum Instrumentation cards. |
| `Sync`      | a class for controling StarHub devices.                  |
| `CardStack` | a class that handles the opening and closing of a combination of different cards either with or without a StarHub that synchronizes the cards. |
| `Netbox`    | a class that handles the opening and closing of the cards in a Netbox                          |


## Connect to a device
Opening and closing of cards is handled using the python [`with`](https://peps.python.org/pep-0343/) statement. This creates a context manager that safely handles opening and closing of a card or a group of cards.

### Using device identifiers
Connect to local cards:

```python
import spcm

with spcm.Card('/dev/spcm0') as card:

    # (add your code here)
```
Connect to remote cards (you can find a card's IP using the
[Spectrum Control Center](https://spectrum-instrumentation.com/en/spectrum-control-center) software):

```python
import spcm

with spcm.Card('TCPIP::192.168.1.10::inst0::INSTR') as card:
    
    # (add your code here)
```

Connect to a group of cards synchronized using a StarHub:

```python
import spcm

card_identifiers = ["/dev/spcm0", "/dev/spcm1"]
sync_identifier  = "sync0"

with spcm.CardStack(card_identifiers=card_identifiers, sync_identifier=sync_identifier) as stack:

    # (add your code here)
```
The `CardStack` object contains a list of `Card` objects in the `stack.cards` parameter and a `Sync` object in the parameter `stack.sync`.

### Using card type or serial number

Apart from connecting to a device directly through a device identifier it's also possible to connect to local devices using the card type or serial number. 

To find the first card of type analog out (`SPCM_TYPE_AO`) you can do the following:
```python
import spcm

with spcm.Card(card_type=spcm.SPCM_TYPE_AO) as card:
    
    # (add your code here)
```
See the register `SPC_FNCTYPE` in the reference manual of your specific device for the kind of card you're using.

If you want to connect to a device based on it's serial number, do the following:
```python
import spcm

with spcm.Card(serial_number=[your serial number here]) as card:
    
    # (add your code here)
```
See the register `SPC_PCISERIALNO` in the reference manual of your specific device for more information.

If the `device_identifier` is given that card is opened, if at the same time `card_type` or `serial_number` are given then these behave as an additional check too see if the opened card is of a certain type or has that specific serial number.

### Demo devices
To test the Spectrum Instrumentation API with user code without hardware, the Control Center gives the user the option to create [demo devices](https://spectrum-instrumentation.com/support/knowledgebase/software/How_to_set_up_a_demo_card.php). These demo devices can be used in the same manner as real devices. Simply change the device identifier string to the string as shown in the Control Center.

## Card Functionality
After opening a card, StarHub or group of card, specific functionality of the cards can be accessed through `CardFunctionality` classes. 

| Name                | Description                                                         |
|---------------------|---------------------------------------------------------------------|
| `Channels`          | class for setting up the in- or output stage of the channels of a card |
| `Clock`             | class for setting up the clock engine of the card                   |
| `Trigger`           | class for setting up the trigger engine of the card                 |
| `MultiPurposeIOs`   | class for setting up the multi purpose i/o of the card              |
| `DataTransfer`      | class for handling data transfer functionality                      |
| `Multi`             | class for handling multiple recording and replay mode functionality |
| `Sequence`          | class for handling sequence mode functionality                      |
| `TimeStamp`         | class for handling time stamped data                                |
| `Boxcar`            | class for handling boxcar averaging                                 |
| `BlockAverage`      | class for handling block averaging functionality                    |
| `PulseGenerators`   | class for setting up the pulse generator functionality              |
| `DDS`               | class for handling DDS functionality                                |

To use a specific functionality simply initiate an instance of one of the classes and pass a device object:

```python
import spcm

card : spcm.Card
with spcm.Card('/dev/spcm0') as card:
    if not card: raise spcm.SpcmException(text="No card found!")

    clock = spcm.Clock(card)
    # (or)
    trigger = spcm.Trigger(card)
    # (or)
    multi_io = spcm.MultiPurposeIOs(card)
    # (or)
    data_transfer = spcm.DataTransfer(card)
    # etc ...

```
Each of these functionalities typically corresponds to a chapter in your device manual, so for further reference please have a look in the device manual.

## Setting up the Clock engine

The Clock engine is used to generate a clock signal that is used as the source for all timing critical processes on the card.

### Sample rate
To get the maximum sample rate of the active card and set the sample rate to the maximum, this is an example using internal PLL clock mode:
```python
clock = spcm.Clock(card)
clock.mode(spcm.SPC_CM_INTPLL)
llMaxSR = clock.max_sample_rate()
llSampleRate = clock.sample_rate(llMaxSR)
print("Current sample rate: {}S/s".format(llSampleRate))
```

## Setting up the Trigger engine

### External trigger

The Trigger engine can be configured for a multitude of different configurations (see the hardware manual for more information about the specific configurations for your device). Here we've given an example for an external trigger arriving at input port ext0, that is DC-coupled. The card is waiting for positiv edge that excedes 1,5 V:

```python
trigger = spcm.Trigger(card)
trigger.or_mask(spcm.SPC_TMASK_EXT0) # set the ext0 hardware input as trigger source
trigger.ext0_mode(spcm.SPC_TM_POS) # wait for a positive edge
trigger.ext0_level0(1500) # Trigger level is 1.5 V (1500 mV)
trigger.ext0_coupling(spcm.COUPLING_DC) # set DC coupling
```

## Setting up the multi-purpose I/O lines

See the hardware manual, for the multi-purpose I/O lines functionality that can be programmed.

## Setting up a data transfer buffer for recording (digitizer) or replay (AWG)

### Recording
To transfer data to or from the card, we have to setup a data transfer object. This object allocates an amount the memory of the card (`memory_size`) and a Direct Memory Access (DMA) buffer on the host pc (`allocate_buffer`). Half of the samples are taken before the trigger, that is configured by the trigger engine, and half of the samples are recorded afterwards. Then the transfer from the card to the host pc is started and the program waits until the DMA is filled.

```python
# define the data buffer
data_transfer = spcm.DataTransfer(card)
data_transfer.memory_size(num_samples)
data_transfer.allocate_buffer(num_samples)
data_transfer.post_trigger(num_samples // 2)
# Start DMA transfer
data_transfer.start_buffer_transfer(spcm.M2CMD_DATA_STARTDMA)

# start card and wait until the memory is filled
card.start(spcm.M2CMD_CARD_ENABLETRIGGER, spcm.M2CMD_DATA_WAITDMA)

# (your code handling the recorded data comes here)
```

To access the recorded data, the data transfer object holds a NumPy array object `data_transfer.buffer` that is directly mapped to the DMA buffer. This buffer can be directly accessed by the different analysis methods in the NumPy package:

```python
import numpy as np

# The extrema of the data
alMin = np.min(data_transfer.buffer, axis=1)
alMax = np.max(data_transfer.buffer, axis=1)
```

This data can be further processed or plotted using, for example, [`matplotlib`](https://matplotlib.org/).

### Replay

The setup of the DMA for replay is very similar. First card memory is allocated with `memory_size` and then a DMA buffer is allocated (`allocated_buffer`) and made accessible though the NumPy object `data_transfer.buffer`, which can then be written to using standard NumPy methods. Finally, data transfer from the host PC to the card is started and the programming is waiting until all the data is transferred:
```python
data_transfer = spcm.DataTransfer(card)
data_transfer.memory_size(num_samples)
data_transfer.allocate_buffer(num_samples)
data_transfer.loops(0) # loop continuously
# simple linear ramp for analog output cards
data_transfer.buffer[:] = np.arange(-num_samples//2, num_samples//2, dtype=np.int16)

data_transfer.start_buffer_transfer(spcm.M2CMD_DATA_STARTDMA, spcm.M2CMD_DATA_WAITDMA)
```

## Multiple recording / replay

In case of Multiple recording / replay, the memory is divided in equally sized segments, that are populated / replayed when a trigger is detected. Multiple triggers each trigger the next segment to be populated or replayed.

The following code snippet shows how to setup the buffer for 4 segments with each 128 Samples:

```python
samples_per_segment = 128
num_segments = 4
multiple_recording = spcm.Multi(card)
multiple_recording.memory_size(samples_per_segment*num_segments)
multiple_recording.allocate_buffer(segment_samples=samples_per_segment, num_segments=num_segments)
multiple_recording.post_trigger(samples_per_segment // 2)
multiple_recording.start_buffer_transfer(spcm.M2CMD_DATA_STARTDMA)
```
Again there are half the samples before and half the samples after the trigger.

## Timestamps

See the example `6_acq_fifo_multi_ts_poll.py` in the examples folder `acquisition` for more information about the usage of TimeStamps. Moreover, detailed information about timestamps can be found in the corresponding chapter in the specific hardware manual.

To setup the timestamp buffer:

```python
ts = spcm.TimeStamp(card)
ts.mode(spcm.SPC_TSMODE_STARTRESET, spcm.SPC_TSCNT_INTERNAL)
ts.allocate_buffer(num_time_stamps)
```

The user can then use polling to acquire time stamp data from the card.

## Additional functionality

### Pulse generator

Please see the folder `pulse-generator` in the `examples` folder for several dedicated examples. In the following, there is a simple example for setting up a single pulse generator on x0.

Create a pulse generators object and get the clock rate used by the pulse generator. Use that to calculate the period of a 1 MHz signal and the half of that period we'll have a high signal (hence 50% duty cycle). The pulse generator will start if the trigger condition is met without delay and loops infinitely many times. The triggering condition is set to the card software trigger. See more details in the pulse generator chapter in the specific hardware manual.

```python
pulse_generators = spcm.PulseGenerators(card)
# get the clock of the card
pulse_gen_clock_Hz = pulse_generators.get_clock()

# generate a continuous signal with 1 MHz
len_for_1MHz = pulse_gen_clock_Hz // spcm.MEGA(1)
pulse_generators[0].mode(spcm.SPCM_PULSEGEN_MODE_TRIGGERED)
pulse_generators[0].period_length(len_for_1MHz)
pulse_generators[0].high_length(len_for_1MHz // 2) # 50% HIGH, 50% LOW
pulse_generators[0].delay(0)
pulse_generators[0].num_loops(0) # 0: infinite
pulse_generators[0].mux1(spcm.SPCM_PULSEGEN_MUX1_SRC_UNUSED)
pulse_generators[0].mux2(spcm.SPCM_PULSEGEN_MUX2_SRC_SOFTWARE) # started by software force command

# and write the pulse generator settings
pulse_generators.write_setup()

# start all pulse generators that wait for a software command
pulse_generators.force()
```

This will start the pulse generator to continuously output a 1 MHz signal.

### Sequence replay mode (AWG only)

Please see the example `3_rep_sequence.py` in the folder `generation` of the examples to see an example dedicated to the Sequence replay mode.

### DDS (AWG only)

Please see the examples in the dedicated examples folder `dds` for all the functionality provided by the DDS framework. Moreover, please also have a look at the corresponding hardware manual.

### Boxcar (Digitizer only)

See the corresponding chapter in the hardware manual for more information about boxcar averaging and the registers used.

### Block average (Digitizer only)

See the corresponding chapter in the hardware manual for more information about block averaging and the registers used.
