# How to tell if two hosts share a network

In this TIL I'm going to explain how to determine if two devices with IP addresses 159.58.55.195 (A) and 159.58.33.3 (B) live on the same network, given a netmask of 255.255.224.0 (M)

### 1) Rewrite the addresses and mask in binary
Write out each of the addresses in binary, one atop the other, like so:

```
A: 10011111 00111010 00110111 11000011
B: 10011111 00111010 00100001 00000011
M: 11111111 11111111 11100000 00000000
```

### 2) Bitwise AND the addresses with the mask
The netmask tells us the depth to which the addresses must match if they are to live on the same network. We make this determination by performing a bitwise AND operation on each address using the mask.

Because subnet masks will always take the binary form of consecutive 1s followed by consecutive 0s, we can simply draw a line after the following 1, like so:

```
A: 10011111 00111010 001 | 10111 11000011
B: 10011111 00111010 001 | 00001 00000011
M: 11111111 11111111 111 | 00000 00000000
```

Because there are only zeros in the mask tail, our AND operation will yield a 0 on every tail bit in each address. Therefore, our resultant addresses are:

```
A: 10011111 00111010 00100000 00000000
B: 10011111 00111010 00100000 00000000
```  

### 3) Check for equality
If we translate our resultant binary addresses back into their IPV4 format, we get:
```
A: 159.58.32.0
B: 159.58.32.0
```

If both of the resultant addresses are the same, as they are here, they live on the same network. Considering a four-layer network model, this means both address they live on the same link and can communicate directly with each other, without requiring a third-party router to sit between them.
