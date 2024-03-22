# Multisim

    This document is written in English, only because my TOEFL test is on tomorrow morning. If you have difficulties with language, simply use translation plugins.

    Before reading passage, let's assume that you have comprehended some basic concept of electronics, e.g. RC filter, opamps, MOSFETs, and so on.

- [Multisim](#multisim)
  - [Start with an RC filter](#start-with-an-rc-filter)
  - [Transient Simulation](#transient-simulation)
  - [](#)


## Start with an RC filter

Let's build a first-order RC filter to go through some basic operations.

If you are reading this passage, I'm pretty sure that you can definitely find out how to drag components onto the schematic, so I'm just showing the screenshots without explaining what to do.

![](Multisim.assets/RCfilter1.png)
![](Multisim.assets/RCfilter2.png)
![](Multisim.assets/RCfilter3.png)

    Kindly reminder: don't forget to set a GROUND, in other words, reference point for EVERY circuit.

## Transient Simulation

As human beings, when we try to investigate some new stuff, we intrinsically wish to see "how it goes". In a more scientific language, that's to say "what's the result of it in the time domain".

In Mulisim, time domain analysis is called **Transient Simulation**, which is one of the most frequently used function.

1. Select Transient Simulation as the active analysis.
![](Multisim.assets/Transient1.png)


2. Determine the parameters for a transient simulation.
  ![](Multisim.assets/Transient2.png)

  - **Initial conditions**:
    - **Set to zero**, all energy-storing components (capacitors and inductors) store nothing at t=0.
    - **User defined**, literally, user defined.
    - **Calculate DC operating point**, what's normally what it does when you choose 'Determine automatically'. The software assume the steady state is reached at t=0.
    - **Determine automatically**, choose it as a start.

  - **End time**: How long will the simulation go. In our example, the source runs at 100kHz, so a 1e-4 seconds run is long enough to observe the output waveform.
  - **TMAX**: Time step of the simulation. A shorter TMAX leads to better resolution and heavier CPU usage, which means slower simulation speed.

  Once you are done, just save it.

3. Put some probes on it.
  ![](Multisim.assets/Transient3.png)

  - The 'V' probes measures the **potential** of certain nodes.
  - The 'A' probes measures the **current** flowing through certain branches.
  - The 'W' probes should be put directly ON the component, it measures the **power consumption**.
  - The '$\pm$V' probes measures the **voltage drop** between two nodes.
  - The 'A/V' probes also measures the power, but it does it in a traditional voltage-current style.

        Notice that probes can be used to define the output of any analysis, not only the transient simulation.

4. Run it. (Click the green 'play' button)
  ![](Multisim.assets/Transient4.png)

That's what you should get if everything goes right. You can see clearly that the output voltage (the green line) is somewhat smaller and lags the source voltage.

In the 'Output View' window, you can polish the waveform with several settings. Normally, the background color, the line width and grids are what we start with. After that, you can export the waveform as an image, or the whole raw data in .csv format. Sometimes I export the csv file and do some further analysis in MATLAB.

## 