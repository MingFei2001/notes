# Data Representation

## References
- [Yet another insignificant programming note - A Tutorial on Data Representation](https_www3.ntu.edu.sg/?url=https%3A%2F%2Fwww3.ntu.edu.sg%2Fhome%2Fehchua%2Fprogramming%2Fjava%2FDataRepresentation.html)

## Number Systems
Multiple number systems exist for different purposes.

- Humans use decimal (base 10) and duodecimal (base 12) because we count with 10 fingers and sometimes 2 big toes.
- Computers use binary (base 2) since transistors have two states: on or off.
- Hexadecimal (base 16) and octal (base 8) are used in computing as compact forms to represent binary numbers.

### Decimal (base 10)

The decimal number system, also known as base 10, uses 10 symbols:

```text
0, 1, 2, 3, 4, 5, 6, 7, 8, 9
```

It uses positional notation:
- Each position is represented as `d × 10^(n-1)`,
- where `d` is the digit and `n` is the position from right to left.
- The least significant digit (smallest place value) is on the rightmost side: start with `d × 10^0`.
- When the count exceeds the current digit, a new digit is added to the left and `n` increases by 1.

```text
1234 = 1 × 10^(4-1) + 2 × 10^(3-1) + 3 × 10^(2-1) + 4 × 10^(1-1)
     = 1000 + 200 + 30 + 4
```

### Binary (base 2)

The binary number system uses two symbols (bits): `0` and `1`

It uses similar positional notation as decimal:
- Each position is represented as `d × 2^(n-1)`,
- where `d` is the digit (bit) and `n` is the position from right to left.
- The least significant bit is on the rightmost side: start with `d × 2^0`.
- When the count exceeds the current digit, a new bit is added to the left and `n` increases by 1.

Example:

```text
10110B = 1×2^4 + 0×2^3 + 1×2^2 + 1×2^1 + 0×2^0
       = 16 + 0 + 4 + 2 + 0
       = 22
```

- Binary numbers are typically denoted by a prefix such as `0b` (e.g., `0b1010`), or a suffix `B` (e.g., `1010B`), to distinguish them from other number systems.
- A binary digit is called a bit, and eight bits make a byte (8-bit units are common in computing).

Computers use the binary (base 2) number system because they are built from digital components called transistors, which operate in two states: on and off. These states correspond to the presence or absence of an electrical charge (or electron) in the silicon circuitry, making binary representation natural for computers.

### Hexadecimal (Base 16)

Hexadecimal number system uses 16 symbols:
```text
0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F 
```
commonly referred as hex digits.

It uses similar positional notation as decimal and binary:
- Each position is represented as `d × 16^(n-1)`,
- where `d` is the digit (bit) and `n` is the position from right to left.
- The least significant bit is on the rightmost side: start with `d × 16^0`.
- When the count exceeds the current digit, a new bit is added to the left and `n` increases by 1.

For example:
```text
A3EH = A00H + 30H + EH
     = 10×16^2 + 3×16^1 + 14×16^0
     = 2560 + 48 + 14 = 2622
```

- Hexadecimal are often denoted with a suffix `H` or prefix `0x`/`0X` in some programming language.
- Computers use the binary system for internal operations, but reading and writing binary is difficult and error-prone for humans.
- Hexadecimal serves as a compact representation of binary bits for this reason.
- Each hexadecimal digit is equivalent to 4 bits, or half a byte.

### Conversion between number systems
Here are some math to convert them.

#### Hexadecimal (Base 16) to Binary (Base 2)
Replace each hex digit into equivalent of 4 binary bits

```text
A3C5H = 1010 (A/10) 0011 (3) 1100 (C/12) 0101 (5) B
102AH = 0001 (1) 0000 (0) 0010 (2) 1010 (A/10) B
```

#### Binary (Base 2) to Hexadecimal (Base 16)
- Split them into 4 from right to left, add 0 to the left as padding when necessary
- then convert each group to an equivalent of Hex digit
- basically reverse operation of `Hex` to `Bin` conversion

```text
1001001010B = 0010 (2) 0100 (4) 1010 (A/10) B = 24AH
10001011001011B = 0010 (2) 0010 (2) 1100 (C/12) 1011 (B/11) B = 22CBH
```

#### Base r to Decimal (Base 10)
Knowing `d*10^(n-1)` and expand it to the entire group:

```text
A1C2H = (A)10×16^3 + 1×16^2 + (C)12×16^1 + 2x16^0
      = 40960 + 256 + 192 + 2 = 41410

10110B = 1×2^4 + 1×2^2 + 1×2^1 
       = 16 + 4 + 2 = 22
```

#### Decimal to Base r
Use repeated division/remainder. For example:
```bash
# To convert 261 to hex
261/16 => quotient = 16 , remainder = 5
16/16  => quotient = 1  , remainder = 0
1/16   => quotient = 0  , remainder = 1
```
Collect all remainder in reverse order,
261 (base 10) = 105H (base 16)

The same applies to any 2 base system.

####
