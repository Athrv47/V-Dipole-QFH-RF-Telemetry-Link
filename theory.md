## 1. Wavelength, frequency and band plan

$$\lambda = \frac{c}{f}, \qquad c = 3\times10^8 \text{ m/s}$$

| Frequency | Use | λ | λ/2 | λ/4 |
| :--- | :--- | :--- | :--- | :--- |
| 135.6 MHz | local HackRF obs | 2.212 m | 1.106 m | 0.553 m |
| 137.000 MHz | nominal | 2.190 m | 1.095 m | 0.547 m |
| 137.100 MHz | NOAA-19 | 2.188 m | 1.094 m | 0.547 m |
| 137.500 MHz | QFH design freq (ref) | 2.181 m | 1.092 m | 0.545 m |
| 137.620 MHz | NOAA-15 | 2.176 m | 1.088 m | 0.544 m |
| 137.900 MHz | METEOR M N2 / local obs | 2.176 m | 1.088 m | 0.544 m |
| 137.9125 MHz | NOAA-18 | 2.176 m | 1.088 m | 0.544 m |

### Interference environment in 137–138 MHz (per the V-dipole guide)

| Signal | Frequency | Note |
| :--- | :--- | :--- |
| NOAA-15 | 137.620 MHz | APT |
| NOAA-18 | 137.9125 MHz | APT |
| NOAA-19 | 137.100 MHz | APT |
| METEOR M N2 | 137.900 MHz | APT-class |
| ORBCOMM | 137.560 MHz | strong, plus wideband 57k6 — sits just below NOAA-15 |
| VDL MODE 2 | 136.975 MHz | aeronautical — don't confuse with NOAA-19 |

Propagation context: NOAA birds are near-polar LEO, so passes are northbound or southbound; a fixed N–S pointing dipole does not need a rotator. Expect ~2 good passes per satellite per day (≥ ~9 for three sats), up to ~10–15 minutes each, typically ≥ 20° max elevation with a visible window (our QFH guide found signal usable once above ~9°).

## 2. Half-wave dipole (V-Dipole) — design values used

The classic rule from the V-dipole guide (matches the standard 468/f in feet):

$$L_{(m)} = \frac{147}{F_{(MHz)}} \;\Rightarrow\; L_{137.5} = \frac{147}{137.5} \approx 1.068 \text{ m} = 106.8 \text{ cm}$$

So each leg including feedwire ≈ **53.4 cm** (leg length must include the connecting wire back to the coax; keep ≤ ~1.5 cm).

Design notes taken from the guide, which our build follows:

- **Wing angle**: bend the two legs into a **120°** included angle ("wings") — turns the figure-8 pattern into a filled/"figure-0" pattern, filling in the deep side nulls for off-axis passes.
- **Element material**: 1/8 in (3.25 mm) aluminum rods; do **not** use ferromagnetic materials (skin-effect losses).
- **Feedpoint**: choc-block (terminal block) at centre; 50 or 75 Ω coax; N or PL-259/BNC conventions as needed.
- **Orientation**: horizontal, pointed N–S (peak pattern N/S, rising/setting passes start and end at opposite horizons).
- **Height matters**: the guide ran 8 m AGL on a mast; nVHF penetration is good but clear horizon >> ideal.
- **Coax count**: the guide shows how much cable loss shows up; the reference used ~25 m of mixed cable (RG-213/H-2000/RG-214/RG-58) and still decoded; we keep the run short and preferably good cable.

## 3. QFH — design values used (137.5 MHz, PVC build)

Reference: the PVC QFH build (137.5 MHz); dimensions from the jcoppens QFH calculator (http://jcoppens.com/ant/qfh/calc.en.php) with the Hole Cutout Guide for the hole drilling template.

Structure: two coax-driven half-wave loops on **one cylinder axis**, phased in quadrature → RHCP end-fire along the axis.

- **Body**: 2 in PVC tube, ~4 ft (two 2 ft sections joined), radius 30.55 mm.
- **Supports**: 1/2 in PVC — 3 × **292 mm** (outer loop, "blue"), 3 × **276.1 mm** (inner loop, "green").

Support midpoint positions (measured from the edge of the 2 in body):

$$m_{outer} = \frac{292}{2} - 30.55 \approx 115.4 \text{ mm}, \qquad m_{inner} = \frac{276.1}{2} - 30.55 \approx 107.6 \text{ mm}$$

- **Elements**: 12 AWG solid copper wire (~15 ft total); two loops (outer "Big Loop" B1/B2, inner "Small Loop" S1/S2) driven in 90° phase offset (one loop is fed directly and the second via a shorted stub/section producing the quadrature).
- **Polarization handedness**: winding counter-clockwise seen from above = RHCP (the satellite handedness — do not mirror the layout).
- **Wiring topology (reference build)**:
  - Top: S1 and B1 to coax centre/inner conductor; S2 to coax shield.
  - Bottom: S1, B1(?), B2 to coax shield (as written in the guide — this exact junction is disputed in the comments there; the circuit shows dots only at true junctions, not crossings — use the guide's wiring diagram as the source of truth, not the prose).
  - Important: the coax cable is electrically **part of the antenna** (an *infinite balun*), not just a feedline attached to it.
- **Materials/practical**: drill a clean straight hole (press/dremel), tight fit for good glue strength (poor fit → failed joints), PVC cement solvent welds, 12 AWG is heavy — pliers + flux + lots of heat, tag/marker tape positions to keep outer/inner straight.
- The guide's measured performance: signal visible from ~9° elevation; good pass 10–15 min; correct/incorrect axial ratio changes decode quality dramatically.

### 3.1. QFH performance expectation

Depending on build accuracy, a real QFH gives perhaps **1–2 dB** over a simple dipole — i.e., it is not automatically the better antenna in absolute terms, but it always has the right polarization for the satellite (omni along the axis, sharp on the horizon). This matches the project's observation of noise/latency in bidirectional tests.

## 4. Polarization and cross-polarization

Reference table (cross-pol isolation between polarization pairs, dB):

| X-pol ↓ / Rx → | V | H | RHCP | LHCP |
| :--- | :--- | :--- | :--- | :--- |
| Vertical | 0 | −20 | −3 | −3 |
| Horizontal | −20 | 0 | −3 | −3 |
| RHCP | −3 | −3 | 0 | −20 |
| LHCP | −3 | −3 | −20 | 0 |

Rules of thumb:

- Any linear antenna pays **3 dB** against a CP satellite signal, always.
- The wrong CP handedness costs ~**20 dB** — catastrophic.
- Opposite linear costs ~**20 dB**.

**Practical consequence (from the V-dipole guide):** a horizontal V-dipole rejects all the vertical-polarized broadcast/aeronautical RFI (e.g., planes at 10–12 km at 25 W transmitting AM on aeronautical allocations near 136–138 MHz) by ~20 dB — a free noise-figure win. A QFH does **not** offer this rejection; it is −3 dB to both horizontal and vertical RFI. This is why a simple horizontal V-dipole is a perfectly rational antenna for this band.

**Axial ratio** (quality of CP):

- Perfect CP: AR = 1 (0 dB).
- AR better than **1 dB** is a good QFH; real DIY builds often end up elliptical.
- Test method (from the V-dipole guide): transmit using the CP antenna, measure received signal on a linear dipole in the two orthogonal orientations at far-field; the ratio of the two measured powers = axial ratio.

**Fraunhofer (far-field) distance** for that check:

$$L_{FF} = \frac{(2D)^2}{\lambda}$$

where D is the largest antenna dimension. For the QFH (~0.6–0.7 m overall height) at 137 MHz, \(L_{FF} \approx (2\times0.6)^2/2.19 \approx 0.66\) m — but practically, keep several λ distance for a clean measurement.

## 5. Friis transmission equation / link budget

Power received over a free-space path:

$$P_r = P_t\, G_t\, G_r \left(\frac{\lambda}{4\pi R}\right)^2$$

Free-space path loss:

$$\mathrm{FSPL} = 20\log_{10}(d_{km}) + 20\log_{10}(f_{MHz}) + 32.45$$

Worked example (137 MHz, slant range 800 km):

$$\mathrm{FSPL} \approx 58.1 + 42.7 + 32.45 \approx 133 \text{ dB}$$

Reference performance points (from the V-dipole guide measurements):

- Minimum S/N ≈ **22 dB** → clean, noise-free picture (below this: black noise bars).
- Peak pass S/N ≈ **45 dB** (headroom during the overhead phase).
- A bare-bones DVB-T dongle + ~25 m of mixed coax decoded NOAA pictures — the antenna and LNA matter more than the receiver.

## 6. Noise and SNR

Thermal noise floor (kTB):

$$N = k\, T\, B, \qquad k = 1.381\times10^{-23} \text{ J/K}, \; T \approx 290 \text{ K}$$

| Bandwidth | N | N (dBm) |
| :--- | :--- | :--- |
| 40 kHz (APT-class passband) | 1.6×10⁻¹⁶ W | ≈ −128 dBm |
| 2 MHz (HackRF obs) | 8.0×10⁻¹⁵ W | ≈ −111 dBm |

$$\mathrm{SNR_{dB}} = 10\log_{10}\left(\frac{P_{signal}}{P_{noise}}\right)$$

The ~71 dB figure from the V-Dipole close-range test is one measurement at one range/orientation — not a spec; it degrades with range, off-axis angle, cable loss and RFI, as observed in the two-way tests.

**Noise contributors** (observed sources to chase): local RFI, SDR gain staging (RF/IF/BB), aeronautical AM transmitters, ORBCOMM, cable loss, impedance mismatch, antenna alignment, grounding/shielding, and USB/clock noise from the SDR itself.

## 7. Doppler shift

LEO weather satellites run v ≈ 7.4 km/s at ~800–850 km altitude. Maximum shift at 137 MHz:

$$\Delta f = \frac{v}{c} f \approx \frac{7.4\times10^3}{3\times10^8}\times 137\times10^6 \approx \pm 3.4 \text{ kHz}$$

Pass structure: right-rising (+3.4 kHz) → overhead zero-Doppler (crossing) → right-setting (−3.4 kHz). GPredict/STK compute the pass curve so the SDR can be tuned ahead of time or continuously corrected; uncompensated, the tone drifts through a WFM demodulator and degrades decode near pass boundaries.

APT signal shape: an FM carrier modulated by a **2400 Hz subcarrier**; the audio WAV is resampled to **11025 Hz** before WXtoImg (`sox <in> <out> rate 11025`), and WXtoImg does a PLL / map-slant calibration pass (disable PLL in Options → Image → map slant, then re-enable) to settle on the decoder's AFC.

## 8. Sampling and GNU Radio chain theory

- **Nyquist:** fₛ > 2·fₘₐₓ. The 48 kHz audio chain is comfortable for APT-class bandwidths; the SDR chain runs at 1.92 MSps.
- Our flowgraphs: 48 kHz audio → **Rational Resampler, interpolation 40** → 48 kHz × 40 = **1.92 MHz** baseband rate → osmocom Sink (HackRF One, 135.602 MHz), RF/IF/BB gains in-flowgraph, parallel QT GUI FFT sink.
- Because the whole chain is a flowgraph, every parameter is re-tunable in software — see [GNU Flowchart.md](GNU%20Flowchart.md).
- Frequency Mod block ≈ phase deviation ∝ message: \(\phi(t) = 2\pi f_c t + 2\pi k_f \int_0^t m(\tau)\,d\tau\) with k_f the block sensitivity parameter.
- Carson's rule (sets the OTA bandwidth you must leave room for):

$$\mathrm{BW} \approx 2\left(\Delta f + f_m\right)$$

## 9. Impedance matching, SWR and mismatch loss

$$\Gamma = \frac{Z_L - Z_0}{Z_L + Z_0}, \qquad \mathrm{SWR} = \frac{1+|\Gamma|}{1-|\Gamma|}$$

$$\text{Return loss} = -20\log_{10}|\Gamma|, \qquad L_{mm} = -10\log_{10}\left(1-|\Gamma|^2\right)$$

Targets: keep SWR ≤ 1.5:1 from 137–138 MHz, i.e. |Γ| ≤ 0.2 (mismatch loss ≈ 0.2 dB). Hand-bent elements won't hit this exactly; compensate with element-length trimming and feed-line chokes, not with hope.

## 10. APT basics and decode requirements

- NOAA APT: satellite image subcarrier on an FM carrier in 137–138 MHz; two lines per second.
- The signal appears as **nine distinct peaks** on a wideband waterfall — that is how you find it by eye.
- Calibration: WXtoImg map-slant + PLL sync; sox resample to 11025 Hz; GPredict ephemeris + TLE refresh in LTE (Two-Line Element) — the VHF guide noted the instructable's "LTE" is really TLE.
- Required S/N ≈ 22 dB (black bars below that); 45 dB typical at culmination.

## 11. Currently measured numbers (from the project report)

| Item | Value / status |
| :--- | :--- |
| V-Dipole close-range SNR | ≈ 71 dB (single test, not spec) |
| QFH ↔ V-Dipole link | Audio both directions; noise/latency present |
| Local obs band | ≈ 135.6 MHz; 137.1 / 137.9 MHz also observed |
| SDR | HackRF One primary; Pluto SDR; USRP B200 Mini |
| Offline decode | Successful on pre-recorded NOAA file (SatNogs) |
| Live satellite pass | Not yet achieved |

## 12. References

- [GS (3).pdf](<GS (3).pdf>) — project report
- [Construction.md](Construction.md) — build photos and video
- [GS (3).pdf](<GS (3).pdf>) — project report (Megha Jha, ECE, Central University of Jammu)
- [Construction.md](Construction.md) — build photos and videos
- [GNU Flowchart.md](GNU%20Flowchart.md) — AM/FM transmit flowgraphs
- [hardware](hardware) / [software](software) / [results](results) — hardware/software/methodology notes
