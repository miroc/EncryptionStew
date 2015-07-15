# EncryptionStew
encryption methods used for iOS version of Social PaL, Aalto University

### ECKeyPairManager
Key pair manager singleton class for elliptic curve Diffie–Hellman (ECDH). ECDH key generation based on [GMEllipticCurveCrypto](https://github.com/ricmoo/GMEllipticCurveCrypto) from Richard Moore

### RSAKeyPairManager
Asymmetric key pair manager for RSA keys in iOS. Known issue see [issue 1](https://github.com/aceisScope/OminiShareiOS/issues/1) in OmniShare. 


### KeyGenerator
Key generator includes: 

1. a function to random generate 20-Byte key to be used for HMAC SHA1 or MD5 

2. a key derivation function based on PBKDF2. 

3. a HMAC SHA1 function with inputs and key in NSData format. Note most of the current solutions on SO for NSString+HMAC can only takes NSString as the key parameter, however if the key is a random generated byte array it's not practical to convert it to NSString.

### Secure Remote Password wrapper for iOS
Based on [csrp](https://github.com/hoccer/csrp) by Tom Cocagne from Hoccer GmbH.
The structure, algorithm and values mentioned in the codes are from a compatible Python module called [pysrp](http://pythonhosted.org/srp/srp.html). Please refer to this man page for notations.
It requires OpenSSL as the only dependency.

SRPClient is a class for SRP client side. 

1. It generates I and A, then sends them to server(host). Alongside A it also creates an instance of `SRPUser`
2. It receives salt and B, generate session key and M
3. It sends M to host, receives H(A,M,K) and verifies if session key matches that in host 

SRPHost is a class for SRP host(server) side.

1. It generates salt and a password verfier 
2. It receives A from client, generate B and seesion key. Then sends salt and B to cliet. Alongside B it also creates an instance of `SRPVerifier`
3. It receives M, verifies if session key matches that in client, and sends H(A,M,K) back to server

#### Sample Usage
```objective-c
    const char * username = "testuser";
    const char * password = "password";
    
    const char *identity = 0;
    
    const unsigned char *byte_A = 0;
    const unsigned char *byte_B = 0;
    const unsigned char *byte_s = 0;
    const unsigned char *byte_M = 0;
    const unsigned char *byte_HAMK = 0;
    
    int len_A = 0;
    int len_B = 0;
    int len_s = 0;
    
    [[SRPClient client] generateClientByteAWithPassword:username andPassword:password];   // -------- step 1
    
    byte_A = [[SRPClient client] getABytes];
    len_A = [[SRPClient client] getALength];
    
    identity = [[SRPClient client] getIdentity];
    
    [[SRPHost host] generateSaltAndVerificationWithIdentity:identity andPassword:password];    // -------- step 2
    [[SRPHost host] generateSessionKeyWithBytesA:byte_A lengthA:len_A];            // ----------- step 3
    
    byte_B = [[SRPHost host] getBytes];
    len_B = [[SRPHost host] getBLength];
    
    byte_s = [[SRPHost host] getSaltBytes];
    len_s = [[SRPHost host] getSaltLength];
    
    [[SRPClient client] generateSessionKeyWithBytesB:byte_B lengthB:len_B salt:byte_s lengthSalt:len_s]; // -------- step 4
    
    byte_M = [[SRPClient client] getMBytes];
    
    int isHostVerified = [[SRPHost host] verifySessionWithBytesM:byte_M];  // -------- step 5
    NSLog(@"host verify %d",isHostVerified);
    
    byte_HAMK = [[SRPHost host] getHAMKBytes];
    int isClientVerified = [[SRPClient client] verifySessionWithBytesHAMK:byte_HAMK];  // -------- step 6
    NSLog(@"client verify %d",isClientVerified);
```
