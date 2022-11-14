### IV - Initialization Vector
An IV in cryptography is some input or data used to provide an initial state for a cryptographic function. They are typically required to be random (i.e. random sample from cpu, temp sensors, microphone, etc combined) or psuedorandom (i.e. randomly chosen value, unix timestamp, etc.).
If the cryptographic scheme only requires the IV be only used once, it may be referred to as a [nonce]({% link notes/nonce.md%}), which may not necessarily be random or pseudorandom. 

#IV #encryption #cryptography 