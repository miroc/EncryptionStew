# EncryptionStew
encryption methods used for iOS version of Social PaL, Aalto University

### ECKeyPairManager
Key pari manager singleton class for elliptic curve Diffie–Hellman (ECDH). ECDH key generation based on [GMEllipticCurveCrypto](https://github.com/ricmoo/GMEllipticCurveCrypto) from Richard Moore


### KeyGenerator
Key generator includes: 
1. a function to random generate 20-Byte key to be used for HMAC SHA1 or MD5 

2. a key derivation function based on PBKDF2. 

3. a HMAC SHA1 function with inputs and key in NSData format. Note most of the current solutions on SO for NSString+HMAC can only takes NSString as the key parameter, however if the key is a random generated byte array it's not practical to convert it to NSString.
