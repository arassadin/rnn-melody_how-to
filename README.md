## About
This repository contains more detailed (and probably clear) description of how to use Magenta's RNN-Melody models with and without hardware MIDI keyboard. It guaranteed to work at least under Ubuntu 16.04.

This How-To based mostly on [this](https://github.com/tensorflow/magenta/blob/master/magenta/interfaces/midi/README.md) official one.

## Prerequisites
Common:

* Ubuntu 16.04
* [Magenta](https://github.com/tensorflow/magenta)

    The machine learning library from Google Brain team based on Tensorflow to combine AI and Art.

* RtMidi

    RtMidi is a MIDI interface routines. It should be already installed if you used official Magenta installation script.

* [QjackCtl](https://qjackctl.sourceforge.io/)

    GUI for the JACK controls.

Optional:

* [VMPK](http://vmpk.sourceforge.net/)

    The virtual MIDI keyboard that simulates the hardware one.

* [FluidSynth](http://www.fluidsynth.org/)

    Software synthesizer for the virtual MIDI devices.

## Installation
1. First, install Magenta manually following [this](https://github.com/tensorflow/magenta#automated-install) instructions.

2. Install RtMidi and related packages:

    `sudo apt install build-essential libasound2-dev libjack-dev`

    `sudo pip install --pre python-rtmidi`

3. Install QjackCtl:

    `sudo apt install qjackctl`

If you have no physical keyboard:

* Install VMPK:

    `sudo apt install vmpk`

* Install FluidSynth:

    `sudo apt install fluidsynth`

## Running
### Preparation
1. Launch *QjackCtl* and click the **Start** button.

2. Execute

    `sudo chown USER:USER /usr/share/sounds/sf2`

    where USER is your current system user (if you are working under the **root**, you probably don't needed in this step)

3. Run *FluidSynth*:

    `fluidsynth /usr/share/sounds/sf2/FluidR3_GM.sf2`

4. If you are planning to use physical keyboard, plug it to the computer. Otherwise, launch *VMPK*.

5. If you are using *VMPK*:
    * open `Edit` -> `Connections`
    * in the `Input MIDI Connection` choose `Midi Through`, in `Output MIDI Connection` choose `FLUID Synth`

6. In *QjackCtl* open Connections and make an apropriate routing:
    * in `Audio` tab choose `fluidsynth` on the left pad ("output ports") and `system` on the right one ("input ports"), then tap connect
    * if you are using a virtual keyboard, on the `ALSA` tab connect `VMPK Output` as an **output** port with the `FLUID Synth` as the **input** one
    * if you are using a physical keyboard, connect its **output** with the `FLUID Synth` **input**

Now you should be able to use (and hear) you keyboard (physical or virtual one) through the computer's audio output.

**Attention!** If you faced with some error during *QjackCtl* (and consequentally with the *FluidSynth*) startup, check in the `Parameters` -> `Interface` whether an appropriate sound card selected.

If everything ended up without troubles, you now have the appropriate software tuned to your hardware. The last preparation step is to download most sutable pre-learned model. Use one of the link bellow for download and store it in the MODEL_PATH:

* [Basic](http://download.magenta.tensorflow.org/models/basic_rnn.mag)
* [Lookback](http://download.magenta.tensorflow.org/models/lookback_rnn.mag)
* [Attention](http://download.magenta.tensorflow.org/models/attention_rnn.mag)
* [RL-Tuned](http://download.magenta.tensorflow.org/models/rl_rnn.mag)
* [Polyphony](http://download.magenta.tensorflow.org/models/polyphony_rnn.mag)

### Playing with the *Intellegence*

First, look at the list of setup MIDI ports:

`magenta_midi --list_ports`

You should see a list of available **input** and **output** ports, including both the controller (e.g., *VMPK*) and synthesizer (e.g. *FluidSynth*) like that:

    Input ports: 'Midi Through:Midi Through Port-0 14:0', 'VMPK Output:VMPK Output 128:0'
    Ouput ports: 'FLUID Synth (8017):Synth input port (8017:0) 131:0', 'Midi Through:Midi Through Port-0 14:0', 'VMPK Input:VMPK Input 129:0'
You need to know the VMPK **input** port and FluidSynth **output**. Note, that the *port* is the **full string name**.

The last step is to launch the application:

```bash
magenta_midi \
  --input_port=INPUT_VMPK_PORT \
  --output_port=OUTPUT_FLUID_PORT \
  --bundle_files=MODEL_PATH
```
You can find more bundle parameters [there](https://github.com/tensorflow/magenta/blob/master/magenta/models/melody_rnn/README.md).

## Ars longa. Vita brevis.
Magenta and its comunity doesn't stand still and the number of projects grows. [This](https://github.com/googlecreativelab/aiexperiments-ai-duet) project allows you to play with the neural network on a par. Also taste [this one](https://github.com/tensorflow/magenta/tree/master/magenta/demos/NIPS_2016) which gives you a most comprehensive suite for the musical improvisation.