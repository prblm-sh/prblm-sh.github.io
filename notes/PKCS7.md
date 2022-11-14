### Public Key Cryptography Standards #7
PKCS#7 - Cryptographic Message Syntax - A standard for storing signed and/or encrypted data.
Padding is in whole bytes, the value of which is equal to the number of bytes needed to pad the remainder of the block. i.e, if an encrypted block needs 3 bytes of padding, the value of those bytes will be `03`.

#### References
[PKCS#7](https://en.wikipedia.org/wiki/PKCS_7)
[PKCS](https://en.wikipedia.org/wiki/PKCS)

#cryptography #encryption #PKCS #PKCS7