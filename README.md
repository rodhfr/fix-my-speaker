#  Fix My Speaker
My loudspeaker seems to have some electrical affair, _I believe_. 

Whenever it’s not playing audio, a humming noise is present. However, when normal audio output occurs, the humming disappears. This script generates an inaudible 440 Hz sine wave tone. It uses an 8 kHz sample rate (telephone-quality audio) and a low refresh rate while loop to minimize CPU consumption.

The windows executable is generated with pytinstaller.
```python
import numpy as np
import sounddevice as sd

# Parameters
sample_rate = 8000  # lowest sample rate for basic audio
volume = 0.00001  # volume (0.0 to 1.0)
frequency = 440  # frequency of the tone in Hz

# Generate a constant tone
def generate_tone(frequency, sample_rate, duration):
    t = np.linspace(0, duration, int(sample_rate * duration), endpoint=False)  # time variable
    tone = volume * np.sin(2 * np.pi * frequency * t)  # sine wave formula
    return tone.reshape(-1, 1)  # reshape for output

# Pre-generate a tone buffer (5 seconds of tone)
tone_buffer = generate_tone(frequency, sample_rate, 5)

# Continuous playback function
def callback(outdata, frames, time, status):
    if status:
        print(status)  # print any status messages
    outdata[:] = tone_buffer[:frames]  # fill outdata with tone buffer

# Start the stream
with sd.OutputStream(samplerate=sample_rate, channels=1, callback=callback, blocksize=1024):
    print("Press Ctrl+C to stop playback.")
    while True:  # keep the main thread alive indefinitely
        sd.sleep(8000)  # sleep for 1000 milliseconds (1 second) to minimize CPU usage
```
