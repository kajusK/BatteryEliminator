# Battery eliminator for NavComm NC-55A

The aim of this project is to eliminate battery from the NavComm NC-55A airband
radio by providing a regulated voltage supply directly through the battery
contacts of the radio.

## Design constraints

- Radio uses 2 cell Li-Ion battery used -> 7.4 V nominal working voltage
- Radio current consumption: 1 A for Tx, 70 mA for Rx
- Input voltage range 8-28 V of aircraft power bus (12 V nominal)
- Good input voltage noise filtering is needed - spikes on engine startup, etc.

## Design

### Input protection

- Polarity protection using **SBR3U40S1F** diode
- Transient protections using **SMDJ28CA** 28V 3kW TVS diode
- Shorts protection using a fuse - assuming 1 A output and 80 % efficiency at minimum working voltage, 1.3 A hold current at 80°C is needed. The **C1Q 1.25** will be used.

### Input filtering

A simple SMD ferrite bead followed by 100 nF and Buck converter input capacitors
provides input filtering to reduce amount of noise going from the DC/DC supply
to the aircraft power bus.

### DC-DC converter

As the voltage difference is quite high (worst case 28 - 7.4 = 20.6 V / 1 A
output), the LDO is not a good choice here, switching supply is needed.

- Buck topology
- ~8-30+ V input voltage range
- more than 1 A output current
- Large output current range (light load mode is possible)

The **AP63200WU-7** was selected as it matches all requirements and
requires a minimum amount of external parts.

- 2 A output current limit
- 3.8 - 32 V input voltage
- 500 kHz switching frequency with spread spectrum to reduce EMI
- integrated MOSFETs
- PFM mode for high efficiency on low output currents
- Overvoltage and overtemperature protection

The feedback voltage reference is 0.8 V, a 75k and 9k1 5% resistors will be used,
giving output voltage (worst case) between 6.77 and 8.09 V which is well within
input voltage range of the radio, a 1 % resistors would be better though.

The inductor will be (assuming 50 % ripple current of 2 A - 1 A):

$$ L = \frac{V_{out}(V_{in}-V_{out})}{V_{in}\Delta I_L f_{sw}} = \frac{7.4 (28-7.4)}{28*1*500000} = 10.9 \mu H $$

The datasheet recommended maximum inductance od 10 $$\mu H$$, so let's stick with
this. The recommended RDC is 100 $m\Omega$ and saturation current 35 % higher than peak one.
The **SRR6028-100Y** was selected.

### Output filtering

Output is fed through a ferrite bead to a parallel combination of small ceramic
capacitor and a large capacitor with higher ESR to provide some dumping.

### Output protection

The output of the switching regulator is separated from radio by a fuse
and a TVS diode to protect the circuit from ESD when radio is not connected.

