# Keccak256 Circom

A pure Circom implementation of the Keccak256 hash function for zero-knowledge proof applications.

## Background

This project is part of the [zkWill](https://github.com/june-in-exile/zkWill) project, which applies Zero-Knowledge Proofs (ZKP) to implement sealed wills. Test data and results for Keccak256 can be found in the zkWill repository. Based on actual test results, this Keccak256 implementation outperforms [vocdoni/keccak256-circom](https://github.com/vocdoni/keccak256-circom) in the following aspects:

- Regardless of the message size, our implemented circuit is smaller, with a maximum reduction of up to 24.5%.
- For messages within the same block, our circuit size does not increase as the message length grows.
- Our version supports messages of arbitrary length.

## Features

- **Complete Keccak256 Implementation**: Full implementation of the Keccak-f[1600] permutation and SHA-3 sponge construction
- **Flexible Input Size**: Support for arbitrary message lengths (parameterized at compile time)
- **Optimized**: Efficient constraint usage with modular arithmetic optimization
- **Well-Tested**: Comprehensive test suite covering various input sizes
- **Documented**: Clear inline documentation for all components

## Examples

### Hash a 256-bit Message

```circom
pragma circom 2.2.2;

include "keccak256-circom/circuits/keccak256/keccak256.circom";

template HashMessage() {
    signal input {bit} msg[256];
    signal output {bit} digest[256];

    digest <== Keccak256(256)(msg);
}

component main = HashMessage();
```

### Hash a Variable-Length Message

```circom
pragma circom 2.2.2;

include "keccak256-circom/circuits/keccak256/keccak256.circom";

template HashLongMessage() {
    // Hash a 1088-bit message (1 block)
    signal input {bit} msg[1088];
    signal output {bit} digest[256];

    digest <== Keccak256(1088)(msg);
}

component main = HashLongMessage();
```

## Constraints

The number of constraints depends on the message length:

| Message Bits    | Constraints |
| --------------- | ----------- |
| 1               | 115200      |
| 256             | 115200      |
| 512             | 115200      |
| 1086            | 115200      |
| 1087 (2 blocks) | 230400      |
| 1088 (2 blocks) | 230400      |
| 1360 (2 blocks) | 230672      |

_Note: Keccak256 uses a block size (rate) of 1088 bits with at least 2-bit padding. Messages requiring multiple blocks will proportionally increase constraints._

## Dependencies

- `circomlib`: Standard Circom library for basic gates
- `snarkjs`: For generating and verifying proofs

## References

- [Keccak Specification](https://keccak.team/keccak.html)
- [SHA-3 Standard (FIPS 202)](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.202.pdf)
- [Circom Documentation](https://docs.circom.io/)