# Readout-Optimized Transmon Qubit as a Quantum Front-End for VLSI Decoding

**Top 5 Finalist, VLSID 2026 Design Contest (Qubit Design Track, C-DAC)**

A design-to-decode pipeline for a superconducting transmon qubit: the qubit and
its readout resonator are designed and characterized, the readout signal is
modeled through a cryogenic amplifier chain, and the measurements are turned into
soft-decision LLRs that drive an LDPC decoder.

Team project (4 members). Full details are in the
[design report](VLSID_Qubit_Design_Report.pdf), and a demonstration is on
[YouTube](https://youtu.be/GVw3HsZWGrE).

## Pipeline

```
Qubit + resonator layout (Keysight QuantumPro)
  -> EM simulation and energy-participation analysis
  -> Dispersive readout simulation (4 mK)
  -> Cryogenic amplifier model (gain, noise temperature, bandwidth)
  -> DSP: integration and I/Q alignment
  -> LLR generation, 8-bit fixed-point quantization
  -> LDPC decoder (Vivado simulation)
```

## Qubit design results

| Parameter | Value | Typical target |
|---|---|---|
| Qubit frequency | 5.99 GHz | 4 - 6 GHz |
| Readout resonator frequency | 7.7 GHz | |
| Anharmonicity | 299 MHz | 200 - 300 MHz |
| Qubit-resonator detuning | ~1.7 GHz | > 1 GHz (dispersive regime) |
| T1 (simulated) | 28.9 us | > 10 - 20 us |

## LLR generation and decoding

- Each measurement shot is integrated to a single I/Q point, and the points are
  rotated so the |0> and |1> clusters separate along the real axis.
- LLRs are computed from the distance to the decision boundary, normalized by the
  noise variance, then scaled by 16 (4 fractional bits) and clipped to 8-bit
  two's complement: `0x7F` is a strong 0, `0x81` a strong 1.
- The LLRs feed a 576-bit LDPC decoder, simulated in Vivado.

Decoder tests:

| Test | Input | Result |
|---|---|---|
| All-zero codeword | 576 LLRs for an all-zero codeword | All 576 output bits decoded as 0 |
| Error injection | Same, with 10 LLRs forced to `0x81` (indices 10, 50, 100, ..., 450) | All 576 output bits decoded as 0 |

## Limitations

- The cryogenic amplifier model is linear and leaves out frequency-dependent noise.
- ADC effects (bit depth, jitter) are only partly modeled.
- Qubit-resonator parameters were not swept exhaustively.

## Credits

The LDPC decoder RTL is from
[biren15/Design-and-Verification-of-LDPC-Decoder](https://github.com/biren15/Design-and-Verification-of-LDPC-Decoder).
