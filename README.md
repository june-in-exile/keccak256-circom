# Keccak256 Circom

A pure Circom implementation of the Keccak256 hash function for zero-knowledge proof applications.

## Features

- **Complete Keccak256 Implementation**: Full implementation of the Keccak-f[1600] permutation and SHA-3 sponge construction
- **Flexible Input Size**: Support for arbitrary message lengths (parameterized at compile time)
- **Optimized**: Efficient constraint usage with modular arithmetic optimization
- **Well-Tested**: Comprehensive test suite covering various input sizes
- **Documented**: Clear inline documentation for all components

## Installation

```bash
npm install keccak256-circom
```

Or using pnpm:

```bash
pnpm add keccak256-circom
```

## Quick Start

### Basic Usage

```circom
pragma circom 2.2.2;

include "keccak256-circom/circuits/keccak256/keccak256.circom";

template Main() {
    // Hash a 256-bit message
    signal input {bit} message[256];
    signal output {bit} hash[256];

    hash <== Keccak256(256)(message);
}

component main = Main();
```

### Using in Your Project

1. Install the package
2. Include the circuit in your Circom file
3. Instantiate the `Keccak256` template with your desired message bit length

```circom
include "keccak256-circom/circuits/keccak256/keccak256.circom";

// For a 512-bit message:
signal {bit} digest[256] <== Keccak256(512)(msg);
```

## API Reference

### Main Template: `Keccak256(msgBits)`

Computes the Keccak256 hash of a message.

**Parameters:**
- `msgBits`: Number of bits in the input message (compile-time constant)

**Inputs:**
- `msg[msgBits]`: Input message as bit array

**Outputs:**
- `digest[256]`: 256-bit hash output

### Component Templates

#### `Padding(msgBits, rateBits)`
Implements the 10*1 padding pattern required by Keccak.

#### `Absorb(msgBits, rateBits)`
Implements the absorb phase of the sponge construction.

#### `Squeeze(digestBits, rateBits)`
Implements the squeeze phase to extract the hash digest.

#### `KeccakF1600()`
The core Keccak-f[1600] permutation function applying 24 rounds of:
- θ (Theta): Column parity mixing
- ρ (Rho): Bit rotation
- π (Pi): Lane permutation
- χ (Chi): Non-linear transformation
- ι (Iota): Round constant addition

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

## Testing

Run the test suite:

```bash
npm test
```

The tests cover:
- Single bit hashing
- Multiple block sizes (256, 512, 1080, 1086, 1087, 1088, 1360 bits)
- Edge cases around block boundaries
- Correctness against reference implementation

## Project Structure

```
keccak256-circom/
├── circuits/
│   ├── keccak256/
│   │   ├── keccak256.circom      # Main entry point
│   │   ├── sponge.circom         # Sponge construction (Padding, Absorb, Squeeze)
│   │   └── keccakF1600.circom    # Core permutation function
│   └── utils/
│       ├── bits.circom           # Bit manipulation utilities
│       └── arithmetic.circom     # Arithmetic utilities
├── tests/
│   ├── keccak256.test.ts         # Main test suite
│   └── keccakF1600.test.ts       # KeccakF1600 unit tests
├── package.json
└── README.md
```

## Constraints

The number of constraints depends on the message length:

| Message Bits | Approximate Constraints |
|-------------|------------------------|
| 1           | ~500K                  |
| 256         | ~500K                  |
| 512         | ~500K                  |
| 1088        | ~970K (2 blocks)       |
| 2176        | ~1.44M (3 blocks)      |

*Note: Keccak256 uses a block size (rate) of 1088 bits. Messages requiring multiple blocks will proportionally increase constraints.*

## Performance Tips

1. **Choose appropriate message size**: Pad your message to just over the necessary length to avoid extra blocks
2. **Batch operations**: If hashing multiple messages, consider parallel circuit design
3. **Memory allocation**: For large circuits, increase Node.js heap size:
   ```bash
   NODE_OPTIONS='--max-old-space-size=8192' npm test
   ```

## Technical Details

This implementation follows the official Keccak specification:
- **Block size (rate)**: 1088 bits
- **Capacity**: 512 bits
- **State size**: 1600 bits (5×5 array of 64-bit lanes)
- **Rounds**: 24
- **Output**: 256 bits

The implementation uses optimizations including:
- Modulo-2 arithmetic for XOR operations
- Precomputed rotation offsets
- Efficient bit manipulation

## Dependencies

- `circomlib`: Standard Circom library for basic gates
- `circom_tester`: Testing framework for Circom circuits
- `snarkjs`: For generating and verifying proofs

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see LICENSE file for details

## References

- [Keccak Specification](https://keccak.team/keccak.html)
- [SHA-3 Standard (FIPS 202)](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.202.pdf)
- [Circom Documentation](https://docs.circom.io/)

## Author

June

## Acknowledgments

Built for zero-knowledge proof applications requiring secure hash functions within circuits.
