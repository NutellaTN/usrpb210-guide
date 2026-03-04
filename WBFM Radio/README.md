# WBFM Radio Receiver

This is a Wideband FM (WBFM) Radio Receiver built using GNU Radio and designed to work with software-defined radios such as the Ettus Research USRP B210. It demodulates standard commercial FM broadcast radio stations (87.5 MHz - 108.0 MHz) and plays the audio directly through your computer's speakers.

## Prerequisites
- USRP B210 connected to your computer.
- [GNU Radio](https://wiki.gnuradio.org/) installed on your system.
- UHD (USRP Hardware Driver) installed.

## How the Code Works

The Python script (`WBFM_Radio.py`) implements a DSP (Digital Signal Processing) flowgraph consisting of the following stages:

1. **USRP Source (`uhd.usrp_source`)**: 
   Captures the raw RF signal from the antenna. The center frequency defaults to **88.5 MHz**, but can be tuned between 87.5 - 108.0 MHz. The SDR sampling rate is set to **1.92 MS/s** (Mega-samples per second).

2. **Rational Resampler (`filter.rational_resampler_ccc`)**: 
   The raw 1.92 MS/s stream is too fast and contains too much bandwidth for just one FM station. This block decimates the signal by a factor of 8 (1.92 MS/s ÷ 8) down to **240 kS/s**, reducing processing load and singling out the channel bandwidth.

3. **WFM Demodulator (`analog.wfm_rcv`)**: 
   Performs the actual Wideband FM demodulation. It takes the 240 kS/s complex baseband signal and extracts the baseband audio. It simultaneously applies an internal decimation of 5, bringing the sample rate down further to **48 kS/s** (a standard audio sampling rate).

4. **Volume Control (`blocks.multiply_const_ff`)**: 
   The demodulated audio stream consists of float values. This block multiplies the stream by a variable (your volume slider) to control the amplitude.

5. **Audio Sink (`audio.sink`)**: 
   The 48 kS/s audio stream is delivered to your operating system's default audio playback device (speakers or headphones).

## User Interface (GUI)

The script uses PyQt5 (via GNU Radio's `qtgui` library) to provide a graphical user interface with real-time controls:
- **Frequency (MHz)**: Tunes the center frequency to find different radio stations.
- **RF Gain (dB)**: Adjusts the hardware amplifier in the SDR. If the signal is too weak, increase it; if the audio is distorted or clipping, lower it.
- **Volume**: A simple software multiplier to adjust the speaker volume.

## How to Run

1. Open **GNU Radio Companion** from your Start menu.
2. Open the `WBFM_Radio.grc` file.
3. Click the "Execute the flow graph" (Play) button in the toolbar.

