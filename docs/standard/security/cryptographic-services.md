---
title: "Cryptographic Services | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net"
ms.reviewer: ""
ms.suite: ""
ms.technology: dotnet-standard
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
  - "jsharp"
helpviewer_keywords: 
  - "cryptography [.NET Framework]"
  - "pattern of derived class inheritance"
  - "digital signatures"
  - "asymmetric cryptographic algorithms"
  - "digital signatures, public-key systems"
  - "public keys"
  - "decryptioin [.NET Framework]"
  - "private keys"
  - "MAC algorithms"
  - "cryptographic algorithms"
  - "private keys, overview"
  - "encryption [.NET Framework]"
  - "security [.NET Framework], encryption"
  - "cryptographic services"
  - "symmetric cryptographic algorithms"
  - "hash"
  - "message authentication codes"
  - "derived class inheritance"
  - "cryptography [.NET Framework], about"
  - "random number generation"
ms.assetid: f96284bc-7b73-44b5-ac59-fac613ad09f8
caps.latest.revision: 34
author: "mairaw"
ms.author: "mairaw"
manager: "wpickett"
---
# Cryptographic Services
<a name="top"></a> Public networks such as the Internet do not provide a means of secure communication between entities. Communication over such networks is susceptible to being read or even modified by unauthorized third parties. Cryptography helps protect data from being viewed, provides ways to detect whether data has been modified, and helps provide a secure means of communication over otherwise nonsecure channels. For example, data can be encrypted by using a cryptographic algorithm, transmitted in an encrypted state, and later decrypted by the intended party. If a third party intercepts the encrypted data, it will be difficult to decipher.  
  
 In the .NET Framework, the classes in the <xref:System.Security.Cryptography?displayProperty=fullName> namespace manage many details of cryptography for you. Some are wrappers for the unmanaged Microsoft Cryptography API (CryptoAPI), while others are purely managed implementations. You do not need to be an expert in cryptography to use these classes. When you create a new instance of one of the encryption algorithm classes, keys are autogenerated for ease of use, and default properties are as safe and secure as possible.  
  
 This overview provides a synopsis of the encryption methods and practices supported by the .NET Framework, including the ClickOnce manifests, Suite B, and Cryptography Next Generation (CNG) support introduced in the [!INCLUDE[net_v35_long](../../../includes/net-v35-long-md.md)].  
  
 This overview contains the following sections:  
  
-   [Cryptographic Primitives](#primitives)  
  
-   [Secret-Key Encryption](#secret_key)  
  
-   [Public-Key Encryption](#public_key)  
  
-   [Digital Signatures](#digital_signatures)  
  
-   [Hash Values](#hash_values)  
  
-   [Random Number Generation](#random_numbers)  
  
-   [ClickOnce Manifests](#clickonce)  
  
-   [Suite B Support](#suite_b)  
  
-   [Related Topics](#related_topics)  
  
 For additional information about cryptography and about Microsoft services, components, and tools that enable you to add cryptographic security to your applications, see the Win32 and COM Development, Security section of this documentation.  
  
<a name="primitives"></a>   
## Cryptographic Primitives  
 In a typical situation where cryptography is used, two parties (Alice and Bob) communicate over a nonsecure channel. Alice and Bob want to ensure that their communication remains incomprehensible by anyone who might be listening. Furthermore, because Alice and Bob are in remote locations, Alice must make sure that the information she receives from Bob has not been modified by anyone during transmission. In addition, she must make sure that the information really does originate from Bob and not from someone who is impersonating Bob.  
  
 Cryptography is used to achieve the following goals:  
  
-   Confidentiality: To help protect a user's identity or data from being read.  
  
-   Data integrity: To help protect data from being changed.  
  
-   Authentication: To ensure that data originates from a particular party.  
  
-   Non-repudiation: To prevent a particular party from denying that they sent a message.  
  
 To achieve these goals, you can use a combination of algorithms and practices known as cryptographic primitives to create a cryptographic scheme. The following table lists the cryptographic primitives and their uses.  
  
|Cryptographic primitive|Use|  
|-----------------------------|---------|  
|Secret-key encryption (symmetric cryptography)|Performs a transformation on data to keep it from being read by third parties. This type of encryption uses a single shared, secret key to encrypt and decrypt data.|  
|Public-key encryption (asymmetric cryptography)|Performs a transformation on data to keep it from being read by third parties. This type of encryption uses a public/private key pair to encrypt and decrypt data.|  
|Cryptographic signing|Helps verify that data originates from a specific party by creating a digital signature that is unique to that party. This process also uses hash functions.|  
|Cryptographic hashes|Maps data from any length to a fixed-length byte sequence. Hashes are statistically unique; a different two-byte sequence will not hash to the same value.|  
  
 [Back to top](#top)  
  
<a name="secret_key"></a>   
## Secret-Key Encryption  
 Secret-key encryption algorithms use a single secret key to encrypt and decrypt data. You must secure the key from access by unauthorized agents, because any party that has the key can use it to decrypt your data or encrypt their own data, claiming it originated from you.  
  
 Secret-key encryption is also referred to as symmetric encryption because the same key is used for encryption and decryption. Secret-key encryption algorithms are very fast (compared with public-key algorithms) and are well suited for performing cryptographic transformations on large streams of data. Asymmetric encryption algorithms such as RSA are limited mathematically in how much data they can encrypt. Symmetric encryption algorithms do not generally have those problems.  
  
 A type of secret-key algorithm called a block cipher is used to encrypt one block of data at a time. Block ciphers such as Data Encryption Standard (DES), TripleDES, and Advanced Encryption Standard (AES) cryptographically transform an input block of *n* bytes into an output block of encrypted bytes. If you want to encrypt or decrypt a sequence of bytes, you have to do it block by block. Because *n* is small (8 bytes for DES and TripleDES; 16 bytes [the default], 24 bytes, or 32 bytes for AES), data values that are larger than *n* have to be encrypted one block at a time. Data values that are smaller than *n* have to be expanded to *n* in order to be processed.  
  
 One simple form of block cipher is called the electronic codebook (ECB) mode. ECB mode is not considered secure, because it does not use an initialization vector to initialize the first plaintext block. For a given secret key *k*, a simple block cipher that does not use an initialization vector will encrypt the same input block of plaintext into the same output block of ciphertext. Therefore, if you have duplicate blocks in your input plaintext stream, you will have duplicate blocks in your output ciphertext stream. These duplicate output blocks alert unauthorized users to the weak encryption used the algorithms that might have been employed, and the possible modes of attack. The ECB cipher mode is therefore quite vulnerable to analysis, and ultimately, key discovery.  
  
 The block cipher classes that are provided in the base class library use a default chaining mode called cipher-block chaining (CBC), although you can change this default if you want.  
  
 CBC ciphers overcome the problems associated with ECB ciphers by using an initialization vector (IV) to encrypt the first block of plaintext. Each subsequent block of plaintext undergoes a bitwise exclusive OR (`XOR`) operation with the previous ciphertext block before it is encrypted. Each ciphertext block is therefore dependent on all previous blocks. When this system is used, common message headers that might be known to an unauthorized user cannot be used to reverse-engineer a key.  
  
 One way to compromise data that is encrypted with a CBC cipher is to perform an exhaustive search of every possible key. Depending on the size of the key that is used to perform encryption, this kind of search is very time-consuming using even the fastest computers and is therefore infeasible. Larger key sizes are more difficult to decipher. Although encryption does not make it theoretically impossible for an adversary to retrieve the encrypted data, it does raise the cost of doing this. If it takes three months to perform an exhaustive search to retrieve data that is meaningful only for a few days, the exhaustive search method is impractical.  
  
 The disadvantage of secret-key encryption is that it presumes two parties have agreed on a key and IV, and communicated their values. The IV is not considered a secret and can be transmitted in plaintext with the message. However, the key must be kept secret from unauthorized users. Because of these problems, secret-key encryption is often used together with public-key encryption to privately communicate the values of the key and IV.  
  
 Assuming that Alice and Bob are two parties who want to communicate over a nonsecure channel, they might use secret-key encryption as follows: Alice and Bob agree to use one particular algorithm (AES, for example) with a particular key and IV. Alice composes a message and creates a network stream (perhaps a named pipe or network e-mail) on which to send the message. Next, she encrypts the text using the key and IV, and sends the encrypted message and IV to Bob over the intranet. Bob receives the encrypted text and decrypts it by using the IV and previously agreed upon key. If the transmission is intercepted, the interceptor cannot recover the original message, because he does not know the key. In this scenario, only the key must remain secret. In a real world scenario, either Alice or Bob generates a secret key and uses public-key (asymmetric) encryption to transfer the secret (symmetric) key to the other party. For more information about public-key encryption, see the next section.  
  
 The [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] provides the following classes that implement secret-key encryption algorithms:  
  
-   <xref:System.Security.Cryptography.AesManaged> (introduced in the [!INCLUDE[net_v35_long](../../../includes/net-v35-long-md.md)]).  
  
-   <xref:System.Security.Cryptography.DESCryptoServiceProvider>.  
  
-   <xref:System.Security.Cryptography.HMACSHA1> (This is technically a secret-key algorithm because it represents message authentication code that is calculated by using a cryptographic hash function combined with a secret key. See [Hash Values](#hash_values), later in this topic.)  
  
-   <xref:System.Security.Cryptography.RC2CryptoServiceProvider>.  
  
-   <xref:System.Security.Cryptography.RijndaelManaged>.  
  
-   <xref:System.Security.Cryptography.TripleDESCryptoServiceProvider>.  
  
 [Back to top](#top)  
  
<a name="public_key"></a>   
## Public-Key Encryption  
 Public-key encryption uses a private key that must be kept secret from unauthorized users and a public key that can be made public to anyone. The public key and the private key are mathematically linked; data that is encrypted with the public key can be decrypted only with the private key, and data that is signed with the private key can be verified only with the public key. The public key can be made available to anyone; it is used for encrypting data to be sent to the keeper of the private key. Public-key cryptographic algorithms are also known as asymmetric algorithms because one key is required to encrypt data, and another key is required to decrypt data. A basic cryptographic rule prohibits key reuse, and both keys should be unique for each communication session. However, in practice, asymmetric keys are generally long-lived.  
  
 Two parties (Alice and Bob) might use public-key encryption as follows: First, Alice generates a public/private key pair. If Bob wants to send Alice an encrypted message, he asks her for her public key. Alice sends Bob her public key over a nonsecure network, and Bob uses this key to encrypt a message. Bob sends the encrypted message to Alice, and she decrypts it by using her private key. If Bob received Alice's key over a nonsecure channel, such as a public network, Bob is open to a man-in-the-middle attack. Therefore, Bob must verify with Alice that he has a correct copy of her public key.  
  
 During the transmission of Alice's public key, an unauthorized agent might intercept the key. Furthermore, the same agent might intercept the encrypted message from Bob. However, the agent cannot decrypt the message with the public key. The message can be decrypted only with Alice's private key, which has not been transmitted. Alice does not use her private key to encrypt a reply message to Bob, because anyone with the public key could decrypt the message. If Alice wants to send a message back to Bob, she asks Bob for his public key and encrypts her message using that public key. Bob then decrypts the message using his associated private key.  
  
 In this scenario, Alice and Bob use public-key (asymmetric) encryption to transfer a secret (symmetric) key and use secret-key encryption for the remainder of their session.  
  
 The following list offers comparisons between public-key and secret-key cryptographic algorithms:  
  
-   Public-key cryptographic algorithms use a fixed buffer size, whereas secret-key cryptographic algorithms use a variable-length buffer.  
  
-   Public-key algorithms cannot be used to chain data together into streams the way secret-key algorithms can, because only small amounts of data can be encrypted. Therefore, asymmetric operations do not use the same streaming model as symmetric operations.  
  
-   Public-key encryption has a much larger keyspace (range of possible values for the key) than secret-key encryption. Therefore, public-key encryption is less susceptible to exhaustive attacks that try every possible key.  
  
-   Public keys are easy to distribute because they do not have to be secured, provided that some way exists to verify the identity of the sender.  
  
-   Some public-key algorithms (such as RSA and DSA, but not Diffie-Hellman) can be used to create digital signatures to verify the identity of the sender of data.  
  
-   Public-key algorithms are very slow compared with secret-key algorithms, and are not designed to encrypt large amounts of data. Public-key algorithms are useful only for transferring very small amounts of data. Typically, public-key encryption is used to encrypt a key and IV to be used by a secret-key algorithm. After the key and IV are transferred, secret-key encryption is used for the remainder of the session.  
  
 The [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] provides the following classes that implement public-key encryption algorithms:  
  
-   <xref:System.Security.Cryptography.DSACryptoServiceProvider>  
  
-   <xref:System.Security.Cryptography.RSACryptoServiceProvider>  
  
-   <xref:System.Security.Cryptography.ECDiffieHellman> (base class)  
  
-   <xref:System.Security.Cryptography.ECDiffieHellmanCng>  
  
-   <xref:System.Security.Cryptography.ECDiffieHellmanCngPublicKey> (base class)  
  
-   <xref:System.Security.Cryptography.ECDiffieHellmanKeyDerivationFunction> (base class)  
  
-   <xref:System.Security.Cryptography.ECDsaCng>  
  
 RSA allows both encryption and signing, but DSA can be used only for signing, and Diffie-Hellman can be used only for key generation. In general, public-key algorithms are more limited in their uses than private-key algorithms.  
  
 [Back to top](#top)  
  
<a name="digital_signatures"></a>   
## Digital Signatures  
 Public-key algorithms can also be used to form digital signatures. Digital signatures authenticate the identity of a sender (if you trust the sender's public key) and help protect the integrity of data. Using a public key generated by Alice, the recipient of Alice's data can verify that Alice sent it by comparing the digital signature to Alice's data and Alice's public key.  
  
 To use public-key cryptography to digitally sign a message, Alice first applies a hash algorithm to the message to create a message digest. The message digest is a compact and unique representation of data. Alice then encrypts the message digest with her private key to create her personal signature. Upon receiving the message and signature, Bob decrypts the signature using Alice's public key to recover the message digest and hashes the message using the same hash algorithm that Alice used. If the message digest that Bob computes exactly matches the message digest received from Alice, Bob is assured that the message came from the possessor of the private key and that the data has not been modified. If Bob trusts that Alice is the possessor of the private key, he knows that the message came from Alice.  
  
> [!NOTE]
>  A signature can be verified by anyone because the sender's public key is common knowledge and is typically included in the digital signature format. This method does not retain the secrecy of the message; for the message to be secret, it must also be encrypted.  
  
 The [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] provides the following classes that implement digital signature algorithms:  
  
-   <xref:System.Security.Cryptography.DSACryptoServiceProvider>  
  
-   <xref:System.Security.Cryptography.RSACryptoServiceProvider>  
  
-   <xref:System.Security.Cryptography.ECDsa> (base class)  
  
-   <xref:System.Security.Cryptography.ECDsaCng>  
  
 [Back to top](#top)  
  
<a name="hash_values"></a>   
## Hash Values  
 Hash algorithms map binary values of an arbitrary length to smaller binary values of a fixed length, known as hash values. A hash value is a numerical representation of a piece of data. If you hash a paragraph of plaintext and change even one letter of the paragraph, a subsequent hash will produce a different value. If the hash is cryptographically strong, its value will change significantly. For example, if a single bit of a message is changed, a strong hash function may produce an output that differs by 50 percent. Many input values may hash to the same output value. However, it is computationally infeasible to find two distinct inputs that hash to the same value.  
  
 Two parties (Alice and Bob) could use a hash function to ensure message integrity. They would select a hash algorithm to sign their messages. Alice would write a message, and then create a hash of that message by using the selected algorithm. They would then follow one of the following methods:  
  
-   Alice sends the plaintext message and the hashed message (digital signature) to Bob. Bob receives and hashes the message and compares his hash value to the hash value that he received from Alice. If the hash values are identical, the message was not altered. If the values are not identical, the message was altered after Alice wrote it.  
  
     Unfortunately, this method does not establish the authenticity of the sender. Anyone can impersonate Alice and send a message to Bob. They can use the same hash algorithm to sign their message, and all Bob can determine is that the message matches its signature. This is one form of a man-in-the-middle attack. See [NIB: Cryptography Next Generation (CNG) Secure Communication Example](http://msdn.microsoft.com/en-us/8048e94e-054a-417b-87c6-4f5e26710e6e) for more information.  
  
-   Alice sends the plaintext message to Bob over a nonsecure public channel. She sends the hashed message to Bob over a secure private channel. Bob receives the plaintext message, hashes it, and compares the hash to the privately exchanged hash. If the hashes match, Bob knows two things:  
  
    -   The message was not altered.  
  
    -   The sender of the message (Alice) is authentic.  
  
     For this system to work, Alice must hide her original hash value from all parties except Bob.  
  
-   Alice sends the plaintext message to Bob over a nonsecure public channel and places the hashed message on her publicly viewable Web site.  
  
     This method prevents message tampering by preventing anyone from modifying the hash value. Although the message and its hash can be read by anyone, the hash value can be changed only by Alice. An attacker who wants to impersonate Alice would require access to Alice's Web site.  
  
 None of the previous methods will prevent someone from reading Alice's messages, because they are transmitted in plaintext. Full security typically requires digital signatures (message signing) and encryption.  
  
 The [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] provides the following classes that implement hashing algorithms:  
  
-   <xref:System.Security.Cryptography.HMACSHA1>.  
  
-   <xref:System.Security.Cryptography.MACTripleDES>.  
  
-   <xref:System.Security.Cryptography.MD5CryptoServiceProvider>.  
  
-   <xref:System.Security.Cryptography.RIPEMD160>.  
  
-   <xref:System.Security.Cryptography.SHA1Managed>.  
  
-   <xref:System.Security.Cryptography.SHA256Managed>.  
  
-   <xref:System.Security.Cryptography.SHA384Managed>.  
  
-   <xref:System.Security.Cryptography.SHA512Managed>.  
  
-   HMAC variants of all of the Secure Hash Algorithm (SHA), Message Digest 5 (MD5), and RIPEMD-160 algorithms.  
  
-   CryptoServiceProvider implementations (managed code wrappers) of all the SHA algorithms.  
  
-   Cryptography Next Generation (CNG) implementations of all the MD5 and SHA algorithms.  
  
> [!NOTE]
>  MD5 design flaws were discovered in 1996, and SHA-1 was recommended instead. In 2004, additional flaws were discovered, and the MD5 algorithm is no longer considered secure. The SHA-1 algorithm has also been found to be insecure, and SHA-2 is now recommended instead.  
  
 [Back to top](#top)  
  
<a name="random_numbers"></a>   
## Random Number Generation  
 Random number generation is integral to many cryptographic operations. For example, cryptographic keys need to be as random as possible so that it is infeasible to reproduce them. Cryptographic random number generators must generate output that is computationally infeasible to predict with a probability that is better than one half. Therefore, any method of predicting the next output bit must not perform better than random guessing. The classes in the [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] use random number generators to generate cryptographic keys.  
  
 The <xref:System.Security.Cryptography.RNGCryptoServiceProvider> class is an implementation of a random number generator algorithm.  
  
 [Back to top](#top)  
  
<a name="clickonce"></a>   
## ClickOnce Manifests  
 In the [!INCLUDE[net_v35_short](../../../includes/net-v35-short-md.md)], the following cryptography classes let you obtain and verify information about manifest signatures for applications that are deployed using [ClickOnce technology](http://msdn.microsoft.com/library/abab6d34-c3c2-45c1-a8b6-43c7d3131e7a):  
  
-   The <xref:System.Security.Cryptography.ManifestSignatureInformation> class obtains information about a manifest signature when you use its <xref:System.Security.Cryptography.ManifestSignatureInformation.VerifySignature%2A> method overloads.  
  
-   You can use the <xref:System.Security.ManifestKinds> enumeration to specify which manifests to verify. The result of the verification is one of the <xref:System.Security.Cryptography.SignatureVerificationResult> enumeration values.  
  
-   The <xref:System.Security.Cryptography.ManifestSignatureInformationCollection> class provides a read-only collection of <xref:System.Security.Cryptography.ManifestSignatureInformation> objects of the verified signatures.  
  
 In addition, the following classes provide specific signature information:  
  
-   <xref:System.Security.Cryptography.StrongNameSignatureInformation> holds the strong name signature information for a manifest.  
  
-   <xref:System.Security.Cryptography.X509Certificates.AuthenticodeSignatureInformation> represents the Authenticode signature information for a manifest.  
  
-   <xref:System.Security.Cryptography.X509Certificates.TimestampInformation> contains information about the time stamp on an Authenticode signature.  
  
-   <xref:System.Security.Cryptography.X509Certificates.TrustStatus> provides a simple way to check whether an Authenticode signature is trusted.  
  
 [Back to top](#top)  
  
<a name="suite_b"></a>   
## Suite B Support  
 The [!INCLUDE[net_v35_short](../../../includes/net-v35-short-md.md)] supports the Suite B set of cryptographic algorithms published by the National Security Agency (NSA). For more information about Suite B, see the [NSA Suite B Cryptography Fact Sheet](http://go.microsoft.com/fwlink/?LinkId=100111).  
  
 The following algorithms are included:  
  
-   Advanced Encryption Standard (AES) algorithm with key sizes of 128, 192, , and 256 bits for encryption.  
  
-   Secure Hash Algorithms SHA-1, SHA-256, SHA-384, and SHA-512 for hashing. (The last three are generally grouped together and referred to as SHA-2.)  
  
-   Elliptic Curve Digital Signature Algorithm (ECDSA), using curves of 256-bit, 384-bit, and 521-bit prime moduli for signing. The NSA documentation specifically defines these curves, and calls them P-256, P-384, and P-521. This algorithm is provided by the <xref:System.Security.Cryptography.ECDsaCng> class. It enables you to sign with a private key and verify the signature with a public key.  
  
-   Elliptic Curve Diffie-Hellman (ECDH) algorithm, using curves of 256-bit, 384-bit, and 521-bit prime moduli for the key exchange and secret agreement. This algorithm is provided by the <xref:System.Security.Cryptography.ECDiffieHellmanCng> class.  
  
 Managed code wrappers for the Federal Information Processing Standard (FIPS) certified implementations of the AES, SHA-256, SHA-384, and SHA-512 implementations are available in the new <xref:System.Security.Cryptography.AesCryptoServiceProvider>, <xref:System.Security.Cryptography.SHA256CryptoServiceProvider>, <xref:System.Security.Cryptography.SHA384CryptoServiceProvider>, and <xref:System.Security.Cryptography.SHA512CryptoServiceProvider> classes.  
  
 [Back to top](#top)  
  
<a name="cng"></a>   
## Cryptography Next Generation (CNG) Classes  
 The Cryptography Next Generation (CNG) classes provide a managed wrapper around the native CNG functions. (CNG is the replacement for CryptoAPI.) These classes have "Cng" as part of their names. Central to the CNG wrapper classes is the <xref:System.Security.Cryptography.CngKey> key container class, which abstracts the storage and use of CNG keys. This class lets you store a key pair or a public key securely and refer to it by using a simple string name. The elliptic curve-based <xref:System.Security.Cryptography.ECDsaCng> signature class and the <xref:System.Security.Cryptography.ECDiffieHellmanCng> encryption class can use <xref:System.Security.Cryptography.CngKey> objects.  
  
 The <xref:System.Security.Cryptography.CngKey> class is used for a variety of additional operations, including opening, creating, deleting, and exporting keys. It also provides access to the underlying key handle to use when calling native functions directly.  
  
 The [!INCLUDE[net_v35_short](../../../includes/net-v35-short-md.md)] also includes a variety of supporting CNG classes, such as the following:  
  
-   <xref:System.Security.Cryptography.CngProvider> maintains a key storage provider.  
  
-   <xref:System.Security.Cryptography.CngAlgorithm> maintains a CNG algorithm.  
  
-   <xref:System.Security.Cryptography.CngProperty> maintains frequently used key properties.  
  
 [Back to top](#top)  
  
<a name="related_topics"></a>   
## Related Topics  
  
|Title|Description|  
|-----------|-----------------|  
|[Cryptography Model](../../../docs/standard/security/cryptography-model.md)|Describes how cryptography is implemented in the base class library.|  
|[Walkthrough: Creating a Cryptographic Application](../../../docs/standard/security/walkthrough-creating-a-cryptographic-application.md)|Demonstrates basic encryption and decryption tasks.|  
|[Configuring Cryptography Classes](../../../docs/framework/configure-apps/configure-cryptography-classes.md)|Describes how to map algorithm names to cryptographic classes and map object identifiers to a cryptographic algorithm.|