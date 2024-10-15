#  Fix My Speaker
My loudspeaker seems to have some electrical affair, _I believe_. 

Whenever it’s not playing audio, a humming noise is present. However, when normal audio output occurs, the humming disappears. This script generates an inaudible 440 Hz sine wave tone. It uses an 8 kHz sample rate (telephone-quality audio) and a low refresh rate while loop to minimize CPU consumption.

## Installation
The windows executable is generated with pytinstaller.

Place the .exe on Startup Folder on Windows to autostart upon user login.

### Code
```python
import numpy as np
import sounddevice as sd

sample_rate = 8000  
volume = 0.00001  # volume (0.0 to 1.0)
frequency = 440

def generate_tone(frequency, sample_rate, duration):
    t = np.linspace(0, duration, int(sample_rate * duration), endpoint=False) 
    tone = volume * np.sin(2 * np.pi * frequency * t)  
    return tone.reshape(-1, 1)  

tone_buffer = generate_tone(frequency, sample_rate, 5)

def callback(outdata, frames, time, status):
    if status:
        print(status)  
    outdata[:] = tone_buffer[:frames] 

with sd.OutputStream(samplerate=sample_rate, channels=1, callback=callback, blocksize=1024):
    print("Press Ctrl+C to stop playback.")
    while True:  
        sd.sleep(8000)  
```
