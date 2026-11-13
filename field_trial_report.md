# Field Trial Report: METEOR M2-4 LRPT Satellite Reception

**Target Satellite:** METEOR M2-4 (NORAD ID: 59051)  
**Downlink Frequency:** 137.900 MHz  
**Receiver Hardware:** Great Scott Gadgets HackRF One  
**Antenna System:** In-house V-Dipole Antenna  
**Tracking Software:** N2YO & Gpredict  
**Demodulation & Processing:** SatDump  

---

## 1. Executive Summary

This report documents the field trial execution for receiving Low-Rate Picture Transmission (LRPT) telemetry from the METEOR M2-4 polar-orbiting meteorological satellite. Using an in-house constructed V-dipole antenna and a HackRF One software-defined radio, our station successfully established signal acquisition, achieving a peak Signal-to-Noise Ratio (SNR) of approximately $3\text{ dB}$ under adverse meteorological conditions. This document outlines the system configuration, operational hurdles, and subsequent optimization roadmap.

---

## 2. System Architecture & Tracking Methodology

Precision tracking and optimal front-end configuration are critical for successful 137 MHz weather satellite reception. The ground station was configured as follows:

* **Antenna Configuration:** An in-house fabricated 137 MHz V-dipole featuring quarter-wavelength elements ($53.4\text{ cm}$) aligned at a $120^\circ$ angle, horizontally oriented with the apex pointing South to maximize polar pass coverage.
* **Feedline:** Approximately $20\text{ meters}$ of coaxial cable connecting the antenna to the receiver.
* **Tracking Subsystem:** Real-time pass prediction and antenna pointing orientation were managed using **N2YO** and **Gpredict**.
* **SDR & Demodulation Parameters:**
  * **Hardware Center Frequency:** $137.600\text{ MHz}$ (utilizing a $300\text{ kHz}$ offset to isolate and mitigate the HackRF zero-IF DC spike).
  * **VFO Target Frequency:** $137.900\text{ MHz}$.
  * **Sample Rate:** $2.0\text{ MSPS}$ decimated to $250\text{ kHz}$.
  * **Gain Staging:** LNA set to $16\text{ dB}$ – $24\text{ dB}$, VGA set to $16\text{ dB}$ – $24\text{ dB}$, with internal RF amplification disabled to prevent front-end saturation.
  * **Pipeline:** SatDump configured for `METEOR M2-x LRPT 72k` OQPSK demodulation.

---

## 3. Field Trial Observations & Environmental Challenges

Testing was conducted across two distinct operational windows, introducing significant environmental variables that impacted link margin and signal clarity:

* **Environmental Conditions:** 
  * *Day 1 Pre-Test:* Severe localized thunderstorms and heavy atmospheric electrical activity.
  * *Day 2 Testing:* Heavy cloud cover and elevated humidity.
* **Signal Performance:** Despite the adverse meteorological conditions and the high attenuation introduced by the $20\text{-meter}$ coaxial cable run, the station successfully acquired the telemetry downlink, recording a peak SNR of approximately $3\text{ dB}$ during the overhead passes. 
* **Limitations:** The modest SNR reflected baseline atmospheric noise, precipitation static, and significant cable insertion loss, which limited full error-free frame synchronization and complete image reconstruction.

---

## 4. Conclusion & Future Optimization Roadmap

While initial signal acquisition validates the core hardware and software pipeline, reaching a stable decoding threshold requires system refinement. Our team plans to implement the following improvements for subsequent campaigns:

1. **Feedline Optimization:** Upgrade to low-loss coaxial cabling (e.g., LMR-400) or relocate the SDR closer to the antenna to minimize the $20\text{-meter}$ attenuation losses.
2. **Low-Noise Amplification:** Integrate a dedicated mast-mounted $137\text{ MHz}$ Low-Noise Amplifier (LNA) paired with a bandpass filter to boost signal strength prior to cable transmission.
3. **Weatherproofing & Deployment:** Refine the physical mounting of the in-house V-dipole to elevate it further above local ground clutter and structural interference.