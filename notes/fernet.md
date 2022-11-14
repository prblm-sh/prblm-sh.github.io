### Fernet Symmetric Encryption
The Fernet encryption scheme is an implementation of `symmetric`, aka `secret key`, encryption. Fernet supports both randomly generated tokens, as well a password for encrypted messages, and is considered a secure standard protocol.

Fernet is built on standard cryptography primitives, using [AES]({% link notes/AES.md%}) in CBC Mode with a 128-bit key, and [PKCS7]({% link notes/PKCS7.md%}) for [padding]({% link notes/padding.md%}). Authentication is handled via [HMAC]({% link notes/HMAC.md%})
 with SHA256. [IV]({% link notes/IV.md%}) values are (typically) handled automatically via `os.urandom()`

##### References
[Cryptography.io Fernet](https://cryptography.io/en/latest/fernet/)


#empty #cryptography #encryption #python #fernet #symmetricEncryption