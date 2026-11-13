## 1. Wavelength and band geometry

The VHF band targeted is ~135–138 MHz, centred on the weather-satellite allocation.

$$\lambda = \frac{c}{f}, \qquad c = 3\times10^8 \text{ m/s}$$

| Frequency | λ | λ/2 | λ/4 |
| :--- | :--- | :--- | :--- |
| 135.6 MHz (local obs) | 2.212 m | 1.106 m | 0.553 m |
| 137.0 MHz (nominal) | 2.190 m | 1.095 m | 0.547 m |
| 137.1 MHz (obs) | 2.188 m | 1.094 m | 0.547 m |
| 137.9 MHz (obs) | 2.176 m | 1.088 m | 0.544 m |

Principle: antenna elements are resonated by cutting them to fractions of λ. Both the half-wave dipole and the QFH below are sized off this relation.

## 2. Half-wave dipole (V-Dipole) theory

Two collinear quarter-wave radiators fed at the centre:

$$L = \frac{\lambda}{2} = \frac{c}{2f}$$

- **Feedpoint impedance** in free space ≈ **73 Ω** (so a 50 Ω coax is an acceptable, un-tuned mismatch for early experiments).
- **Radiation pattern**: a donut — figure-of-8 in the E-plane (nulls along the element axis), omnidirectional in the H-plane.
- **Gain** ≈ 2.15 dBi.
- **Resonance**: the element carries a current standing wave with a node at each open end; that is what λ/4 arms do at frequency.

Why V-Dipole as baseline: simplest geometry, easy to fabricate and tune in a lab, and the reference antenna against which the QFH is compared.

## 3. Quadrifilar Helix (QFH) theory

Four helical elements wound on a common cylinder, with adjacent elements fed **90° apart in phase**:

$$\phi_0 = 90^\circ = \frac{\pi}{2}$$

Elements 0 and 2 are one orthogonal pair; 1 and 3 the other. The phase combination produces a **circularly polarized** wavefront:

- Main lobe is along the cylinder axis — ideal for near-overhead satellite passes.
- RHCP by default; mirror the winding for LHCP.
- A CP antenna receives CP signals of either handedness with a **3 dB polarization-mismatch penalty**; a linear antenna pays that 3 dB always.
- Key quality metric: **axial ratio** (major/minor axis of the polarization ellipse). Ideal = 1 (0 dB). Hand fabrication trades some axial ratio for build simplicity.

## 4. Friis transmission equation / link budget

Power received over a free-space path:

$$P_r = P_t\, G_t\, G_r \left(\frac{\lambda}{4\pi R}\right)^2$$

Free-space path loss:

$$\mathrm{FSPL} = 20\log_{10}(d_{km}) + 20\log_{10}(f_{MHz}) + 32.45$$

Worked example (137 MHz, slant range 800 km):

$$\mathrm{FSPL} = 20\log_{10}(800) + 20\log_{10}(137) + 32.45 \approx 133 \text{ dB}$$

Link-budget intuition for the local tests: the ~71 dB SNR only holds close-in; it falls with the square of distance per Friis.

## 5. Noise and SNR

Thermal noise floor (kTB):

$$N = k T B, \qquad k = 1.381\times10^{-23} \text{ J/K}, \; T \approx 290 \text{ K}$$

| Bandwidth | N | N (dBm) |
| :--- | :--- | :--- |
| 40 kHz | 1.6×10⁻¹⁶ W | ≈ −128 dBm |
| 2 MHz | 8.0×10⁻¹⁵ W | ≈ −111 dBm |

Signal-to-noise ratio:

$$\mathrm{SNR_{dB}} = 10\log_{10}\left(\frac{P_{signal}}{P_{noise}}\right)$$

The ~71 dB figure from the V-Dipole close-range test is one measurement at one range/orientation — not a stable spec; it degrades with distance, off-axis angle, cable loss and local RFI (as seen in the two-way tests).

**Noise contributors to chase:** local RFI, SDR gain staging (RF/IF/BB), receiver bandwidth, cable loss, impedance mismatch, antenna alignment, grounding and shielding.

## 6. Doppler shift

A LEO weather satellite (NOAA class, ~800–850 km, v ≈ 7.4 km/s) shifts the received frequency by:

$$\Delta f = \frac{v}{c}\, f$$

At 137 MHz:

$$\Delta f \approx \frac{7.4\times10^3}{3\times10^8}\times 137\times10^6 \approx \pm 3.4 \text{ kHz}$$

rising pass = frequency shifted high, peak = zero crossing, setting = shifted low. Hence the GPredict workflow: compute the pass schedule and Doppler curve ahead of time and pre-tune the SDR.

## 7. Sampling and GNU Radio chain theory

- **Nyquist criterion:** the sample rate must exceed twice the highest frequency present, fₛ > 2 fₘₐₓ.
- Audio chain: 48 kHz audio → **Rational Resampler, interpolation 40** → 48 kHz × 40 = 1.92 MHz baseband rate feeding the osmocom Sink.
- The osmocom (gr-osmosdr) Sink drives the HackRF One: center 135.602 MHz, 1.92 MSps, RF/IF/BB gains set in-flowgraph.
- Everything is a software flowgraph, so all parameters are re-tunable without touching hardware — see [GNU Flowchart.md](GNU%20Flowchart.md).
- Frequency Mod implements a phase deviation proportional to the message: \(\phi(t) = 2\pi f_c t + 2\pi k_f \int_0^t m(\tau)\,d\tau\), with k_f the block sensitivity.

## 8. FM theory and Carson's rule

For \(s(t) = A_c \cos(2\pi f_c t + \beta \sin 2\pi f_m t)\):

$$\beta = \frac{\Delta f}{f_m}, \qquad \mathrm{BW} \approx 2(\Delta f + f_m)$$

Deviation and bandwidth must stay inside the configured HackRF/OSMOCOM bandwidth and what SDR Console shows as used spectrum.

## 9. Impedance matching, SWR and mismatch loss

$$\Gamma = \frac{Z_L - Z_0}{Z_L + Z_0}, \qquad \mathrm{SWR} = \frac{1+|\Gamma|}{1-|\Gamma|}$$

$$\text{Return loss} = -20\log_{10}|\Gamma|, \qquad L_{mm} = -10\log_{10}\left(1 - |\Gamma|^2\right)$$

Practical fixes: 50 Ω everywhere, short good feedline, solid joints, ferrite chokes on USB/DC leads to kill common-mode noise, and symmetric hand-bent elements.

## 10. NOAA APT basics (137 MHz band)

- APT output is an FM carrier modulated by a 2400 Hz subcarrier carrying image lines (2 lines/s, ~256 px/line).
- Local observations centred on 135.6 MHz; satellite observations near 137.1 / 137.9 MHz.
- Decode path verified offline against a pre-recorded NOAA transmission from SatNogs via SatDump / WXtoImg — a dry run, not a live capture.

## 11. Currently measured numbers (from the report)

| Item | Value / status |
| :--- | :--- |
| V-Dipole close-range SNR | ≈ 71 dB (single test) |
| QFH ↔ V-Dipole link | Audio both directions; noise/latency present |
| Local obs band | ≈ 135.6 MHz; 137.1 / 137.9 MHz also observed |
| SDR | HackRF One primary; Pluto SDR; USRP B200 Mini |
| Offline decode | Successful on pre-recorded NOAA file (SatNogs) |
| Live satellite pass | Not yet achieved |

## 12. Open questions / things to refine

- [ ] Exact element dimensions and spacing as cut
- [ ] Measured SWR of both antennas at 137 MHz
- [ ] Feedline type, length, and feedpoint impedance
- [ ] Latency vs buffer size / sample rate
- [ ] RFI survey — which noise source is dominant
- [ ] QFH axial-ratio estimate from the two-direction link test
- [ ] Doppler compensation plan for the first live pass

## 13. References

- [GS (3).pdf](<GS (3).pdf>) — project report
- [Construction.md](Construction.md) — build photos and video
- [GNU Flowchart.md](GNU%20Flowchart.md) — AM/FM transmit flowgraphs
- [hardware](hardware) / [software](software) / [results](results) — hardware, software and methodology notes
