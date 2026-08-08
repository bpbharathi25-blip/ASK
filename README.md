# ASK & FSK
# Aim
 Write a simple Python program for the modulation and demodulation of ASK and FSK.
# Tools required
 Google coLab
# Program
# ASK
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Butterworth Low-Pass Filter
def butter_lowpass_filter(data, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, data)

# Parameters
fs = 1000                 # Sampling frequency (Hz)
f_carrier = 50            # Carrier frequency (Hz)
bit_rate = 10             # Bit rate (bits/sec)
T = 1                     # Duration (seconds)

# Time vector
t = np.linspace(0, T, int(fs * T), endpoint=False)

# Generate random binary message
bits = np.random.randint(0, 2, bit_rate)

# Number of samples per bit
bit_duration = fs // bit_rate

# Message signal
message_signal = np.repeat(bits, bit_duration)

# Carrier signal
carrier = np.sin(2 * np.pi * f_carrier * t)

# ASK Modulation
ask_signal = message_signal * carrier

# Coherent ASK Demodulation
demodulated = ask_signal * carrier

# Low-pass filter
filtered_signal = butter_lowpass_filter(demodulated, f_carrier, fs)

# Recover bits
decoded_bits = (filtered_signal[::bit_duration] > 0.25).astype(int)

# Print results
print("Original Bits :", bits)
print("Decoded Bits  :", decoded_bits)

# Plotting
plt.figure(figsize=(12, 8))

# Message signal
plt.subplot(4, 1, 1)
plt.plot(t, message_signal, color='blue')
plt.title("Message Signal")
plt.grid(True)

# Carrier signal
plt.subplot(4, 1, 2)
plt.plot(t, carrier, color='green')
plt.title("Carrier Signal")
plt.grid(True)

# ASK signal
plt.subplot(4, 1, 3)
plt.plot(t, ask_signal, color='red')
plt.title("ASK Modulated Signal")
plt.grid(True)

# Decoded bits
plt.subplot(4, 1, 4)
plt.step(np.arange(len(decoded_bits)), decoded_bits, where='mid', marker='o', color='magenta')
plt.title("Decoded Bits")
plt.ylim(-0.2, 1.2)
plt.grid(True)

plt.tight_layout()
plt.show()
```
# FSK
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

def butter_lowpass_filter(data, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, data)


fs = 1000
f1 = 30
f2 = 70
bit_rate = 10
T = 1

t = np.linspace(0, T, int(fs * T), endpoint=False)

bits = np.random.randint(0, 2, bit_rate)
bit_duration = fs // bit_rate
message_signal = np.repeat(bits, bit_duration)

carrier_f1 = np.sin(2 * np.pi * f1 * t)
carrier_f2 = np.sin(2 * np.pi * f2 * t)

fsk_signal = np.zeros_like(t)

for i, bit in enumerate(bits):
    start = i * bit_duration
    end = start + bit_duration
    freq = f2 if bit else f1
    fsk_signal[start:end] = np.sin(
        2 * np.pi * freq * t[start:end]
    )

ref_f1 = np.sin(2 * np.pi * f1 * t)
ref_f2 = np.sin(2 * np.pi * f2 * t)

corr_f1 = butter_lowpass_filter(
    fsk_signal * ref_f1, f2, fs
)
corr_f2 = butter_lowpass_filter(
    fsk_signal * ref_f2, f2, fs
)

decoded_bits = []

for i in range(bit_rate):
    start = i * bit_duration
    end = start + bit_duration

    energy_f1 = np.sum(corr_f1[start:end] ** 2)
    energy_f2 = np.sum(corr_f2[start:end] ** 2)

    decoded_bits.append(
        1 if energy_f2 > energy_f1 else 0
    )

decoded_bits = np.array(decoded_bits)
demodulated_signal = np.repeat(decoded_bits, bit_duration)

plt.figure(figsize=(12, 12))

plt.subplot(6, 1, 1)
plt.plot(t, message_signal, color='b')
plt.title('Message Signal')
plt.grid(True)

plt.subplot(6, 1, 2)
plt.plot(t, carrier_f1, color='g')
plt.title('Carrier Signal for bit = 0 (f1)')
plt.grid(True)

plt.subplot(6, 1, 3)
plt.plot(t, carrier_f2, color='r')
plt.title('Carrier Signal for bit = 1 (f2)')
plt.grid(True)

plt.subplot(6, 1, 4)
plt.plot(t, fsk_signal, color='m')
plt.title('FSK Modulated Signal')
plt.grid(True)

plt.subplot(6, 1, 5)
plt.plot(t, demodulated_signal, color='k')
plt.title('Final Demodulated Signal')
plt.grid(True)

plt.tight_layout()
plt.show()
```
# Output Waveform
# ASK
<img width="1190" height="790" alt="image" src="https://github.com/user-attachments/assets/501522c7-00cc-4760-b7d3-c73b37e02ab5" />




# FSK
<img width="1201" height="1012" alt="image" src="https://github.com/user-attachments/assets/daf9c0b7-31a0-4d80-bd7c-9f85ebe7ee82" />




# Results
Thus, the ASK and FSK performed using python in Google CoLab
