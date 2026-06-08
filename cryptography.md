# Foundations of Cryptography

> A practical, opinionated tour through modern cryptography — symmetric and asymmetric encryption, hashing, TLS, key management, PKI, and real-world deployments.

**Author:** Maksim Tsvetkov · **Originally published:** 11 October 2022 · Translated from Russian.

---

## Table of Contents

- [Symmetric Encryption](#symmetric-encryption)
  - [Binary digits and the XOR operation](#binary-digits-and-the-xor-operation)
  - [Key sizes](#key-sizes)
  - [Block ciphers](#block-ciphers)
  - [Stream ciphers](#stream-ciphers)
  - [Message Authentication Codes (MAC)](#message-authentication-codes-mac)
  - [Synchronization: clocks, sequences, and nonces](#synchronization-clocks-sequences-and-nonces)
  - [Hash functions](#hash-functions)
  - [Modes of operation](#modes-of-operation)
- [DES](#des)
- [The Vernam Cipher](#the-vernam-cipher)
- [Asymmetric Encryption](#asymmetric-encryption)
- [RSA](#rsa)
- [TLS](#tls)
  - [Intercepting session keys](#intercepting-session-keys)
- [Diffie–Hellman](#diffiehellman)
- [Authentication](#authentication)
- [Key Hierarchy](#key-hierarchy)
  - [Digital signatures](#digital-signatures)
- [Protocols](#protocols)
- [PKI and Key Storage](#pki-and-key-storage)
  - [A practical aside: archiving and protecting a file from the command line](#a-practical-aside-archiving-and-protecting-a-file-from-the-command-line)
  - [A practical aside: generating your own SSH key](#a-practical-aside-generating-your-own-ssh-key)
- [Applied Cases](#applied-cases)
  - [IoT](#iot)
  - [Base stations](#base-stations)
  - [Wi-Fi](#wi-fi)
  - [Cryptocurrencies](#cryptocurrencies)
  - [Mobile calls](#mobile-calls)
  - [Identification cards](#identification-cards)
  - [Email](#email)
- [A Catalogue of Attacks](#a-catalogue-of-attacks)
- [A Short Glossary of Terms](#a-short-glossary-of-terms)

---

## Symmetric Encryption

The link between your smartphone and your mobile carrier, your internet provider, or your bank runs on symmetric encryption. Your browser sends and receives data using symmetric encryption — not because it is the most secure scheme available, but because it is fast. Symmetric keys occupy a trivial amount of memory (256 bits is just 16 bytes). If you want to get a feel for it, try encrypting something with a tool such as BCTextEncoder.

Symmetric encryption means a single key is used both to encrypt and to decrypt data. Sender and receiver share the same key in order to exchange encrypted messages. The same applies when you encrypt files or a disk. iMessage, Signal, and WhatsApp all rely on symmetric encryption to keep your correspondence safe.

A symmetric algorithm depends on one identical key, which must be distributed securely between the two parties. Public-key cryptography, by contrast, works with two keys: one private and one public, the latter distributed openly across the internet. In the symmetric case the shared key is the same for sender and receiver, and that is precisely where the problems begin. As long as we have only Alice and Bob, one key suffices. But the moment we scale to a thousand contacts, we need roughly 500,000 unique keys — the growth is effectively exponential. The very idea of a shared key also fails to provide non-repudiation: since both Alice and Bob hold the same secret, both can generate identical message signatures, and neither can later prove the other was responsible.

In symmetric encryption the plaintext may be processed either in blocks or bit by bit. The DEA and AES algorithms use blocks of 64 and 128 bits respectively. Although AES has a fixed block size of 128 bits, it is better to choose 14 rounds for 256-bit keys. Truly random generation requires expensive computation, so pseudo-random generation is used instead to speed the process up. AES does not provide Perfect Forward Secrecy, which means that the moment an attacker obtains the shared key, they can decrypt everything.

### Binary digits and the XOR operation

If the algorithms of the pre-computer world could lean on letters, inside a computer we lean on numbers alone. Zeros and ones are bits; eight bits make a byte. Take XOR as the foundational example. Recall the powers of two: 2⁴ = 2 × 2 × 2 × 2 = 16. A key might be 128 bits long in the modern world, or 64 bits. The difference looks slight, yet the longer key is some 18 quintillion times more secure — doubling a key's length strengthens security enormously. In AES you may choose one of three key sizes. The key in AES-128 is small enough to be itself encrypted under a public key, which is exactly how hybrid encryption arises. If your key were only 10 bits long, it could be guessed in about a thousand attempts. A key of *n* bits gives 2ⁿ possibilities, so for *n* = 64 there are 2⁶⁴ possible keys, roughly 1.8 × 10¹⁹. Even combinations of that size, however, can be broken by purpose-built hardware. A 128-bit key is hard to brute-force. By contrast, a 2-bit hash function gives only four values — 00, 01, 11, 10 — while at a length of 512 the attacker faces 2²⁵⁶.

It is worth pausing on what XOR actually is, since the algorithms here invoke it constantly. XOR is one of the machine instructions for operating on bit sequences. A bit holds either 0 or 1; eight bits form a byte, and a word fits comfortably into two bytes, or 16 bits. At this low level of data handling there is none of the type system you find in high-level programming languages — there are only bits. Working in Intel assembly syntax, `XOR` is the kind of instruction the processor executes directly on the raw bit patterns you have surely seen as machine code: things like `0x31`, `0xC0`, or the binary form `0011000111000000`.

Applying XOR to a 128-bit key already lets the algorithm resist a brute-force attack; combining `XOR` with `ADD` on the key makes even frequency analysis unhelpful. The related bitwise instructions behave as follows. With `AND`, given two strings —

```text
AX 0001011010111111
BX 1100110010101000
```

— the result is `0000010010101000`. The `NOT` instruction simply inverts each bit: where there was a 0 there is now a 1. Applied to the first string above, `NOT` yields `1101101000010111`.

### Key sizes

Symmetric keys vary in size, but the most common lengths are 128 or 256 bits. Using a sound 256-bit symmetric scheme makes an organization's communications practically invulnerable to brute-force attack, provided the encryption key itself is strong.

### Block ciphers

The first kind of symmetric encryption is the block cipher, mentioned above. If the block size is too small it can be broken; if it is too large the cipher becomes inefficient. DES used a 64-bit block (perfectly reasonable for 1970) and a 56-bit key, which means more than 72 quadrillion possible keys. That sounds impressive, but 128 bits is better. With a block size of 128 and a message of 348 bits, we get 348 ÷ 128 = 2.71 blocks; after filling two blocks (256 bits), 92 bits are left empty and must be filled with something. Filling that empty space is called padding. Looking slightly ahead, padding is handled well in RSA-OAEP, a well-known scheme built on two hash functions.

Encryption also cannot conceal the length of a message. And if you encrypt with a block cipher in a naïve mode — ECB — then identical plaintext blocks map to identical ciphertext blocks. Because the blocks are fairly small, there will be many such repetitions, and they leak a great deal of information to an attacker, who can study the traffic, isolate a pattern, and use it for inference. The common countermeasures are to send decoy messages and to add padding.

### Stream ciphers

Where a block cipher takes a 128-bit block and transforms it as a unit, a stream cipher transforms the individual bit. The stream cipher is the second of the two most popular symmetric forms. It uses XOR, its key will most likely be 128 bits, and it is very fast in use. One implementation is RC4, which is quite weak from a security standpoint, so the practice is to use a fresh key for every packet. It is certainly unfit for exchanging payment data, but for a military field radio it is fine. RC4 can still be found in WEP.

### Message Authentication Codes (MAC)

Now to the MAC — and here we mean not the address assigned to network hardware at the factory, but the cryptographic MAC. It is short, easily computed, and the single most popular symmetric technique. Suppose an attacker intercepts a message encrypted with a stream cipher and flips a single bit. If the affected field were a date, it would change without anyone noticing, because it remains a perfectly valid date — merely a different one. It was 12/12/2022; now it is 11/12/2022. In other words, encryption on its own gives no guarantee that a message arrives unaltered. To check integrity on the receiving side, a MAC is verified — and the value differs between sender and receiver. A MAC is not a hash function, which by itself cannot confirm the origin of data, even though a MAC may be built upon a hash function and still be called a MAC. A MAC lengthens the message to be sent, which affects throughput; for that reason its length is usually kept within 32–64 bits — extra time spent, but extra security gained. The MAC has many standards; one of them is ISO/IEC 9797.

Encryption alone gives no hint of data origin, and where origin matters — and it does — symmetric encryption is paired with a MAC in CTR mode. My own approach is to encrypt first and then compute the MAC. If the MAC fails verification, the receiver simply rejects the message. This approach uses two distinct keys: two keys mean two operations, which is costly, though more secure. The scheme has a name — Galois Counter Mode (GCM) = CTR + MAC. Its output is the ciphertext plus the MAC, and active attacks are headed off by the MAC.

MACs provide data-origin authentication and, to a degree, defense against active attacks, because a symmetric key is involved. MACs are sometimes built from hash functions, but they are not hash functions. Hash functions, for their part, do not provide non-repudiation. Hash functions can serve as part of a digital signature, which *can* provide non-repudiation — but digital signatures are not hash functions either.

Integrity, then, requires more than encryption; it also requires authenticity (recall the date example). Integrity is about the absence of changes in transit; authenticity is the guarantee that the message comes from the expected sender. A MAC takes a context as input and produces a string of 64 or 128 bits. The popular attacks against it are the forgery attack and the key-recovery attack; a forgery attack lets a third party without the secret key generate a valid MAC.

### Synchronization: clocks, sequences, and nonces

To exchange keys, both sender and receiver need a clock. A computer has one, but simpler devices — smart cards, for instance — may not. The time must agree on both sides, yet sooner or later it drifts out of sync, a drift that even air temperature can influence. The time must therefore be synchronized against some standard. A MAC can handle this task. The optimal choice is a nonce mechanism (Number Used Only Once) — and, if we indulge in fantasy, a strontium atomic clock. The methods of time synchronization compare as follows:

| | Clock | Sequence | Nonce |
| --- | --- | --- | --- |
| Synchronization required | Yes | Yes | No |
| Communication latency | Yes | Yes | Yes |
| Integrity required | Yes | Yes | No |
| Minimum number of passes | 1 | 1 | 2 |
| Special requirement | Clock | Database | Random-number generator |

### Hash functions

Protecting data integrity against accidental errors is handled by CRC checks. Hash functions can be used to store passwords, since they never need to be reversed. A hash function has no key and is usually much smaller than the original message, which is why the same hash can belong to different files. Consider a bank-card PIN: only four digits, hence 10,000 combinations. If the bank has a million customers, then on average every hundredth customer shares a PIN. Yet even if an attacker buys a database of salted password hashes, it gains them nothing: recovering the password characters from the hash value is computationally very hard. Such hash functions resist inversion, and it is difficult to tell which *identical* inputs lie hidden behind an *identical* hash output. If the bank required five-digit codes, that would be 100,000 possible PINs — and the square root of 100,000 is a little over 316.

A hash function lets you confirm data integrity, but it is not a reliable integrity tool. It allows you to confirm that a file was not altered by a technical error — no more than that. Technically it is a simple comparison of two hashes, the computed one against the expected one. An attacker can sidestep this by generating the hash from an illegitimate file in the first place. Hash functions cannot detect malicious modification by an active attacker who also rewrites the hash to match the altered data.

A hash function can prevent this by relying on three properties: preimage resistance, second-preimage resistance, and collision resistance. A hash is quick and easy to compute and is a one-way function. Hash functions, again, do not provide non-repudiation.

In practice you will often need to create password hashes — MD5, say — and then attempt to crack them. The workflow runs roughly like this. First you need a stock of candidate passwords. In Kali, the tool `cewl https://www.pivpn.io/ -d 4 -m 6` will scrape words from a site, or you can take a ready-made list with `locate rockyou.txt` or `cat /usr/share/wordlists/rockyou.txt.gz`. To generate a hash on Linux, use:

```bash
echo -n "JLjh7567jHytjkkj/ddh" | md5sum | tr -d " -" >> md5_hashes.txt
```

which yields the MD5 digest of the password — for example `22c86261451dc6ff726b1f6aaf46f6a0`. For SHA-1 and SHA-256:

```bash
echo -n "headad2llo"    | sha1sum   | tr -d " -" >> sha1_hashes.txt
echo -n "hel897qwytulo" | sha256sum | tr -d " -" >> sha256_hashes.txt
```

Then confirm that a recovery tool such as hashcat is installed with `hashcat -h`, and run, for instance:

```bash
hashcat -m 0 -a 0 -o cracked.txt md5_hashes.txt rockyou.txt
```

The same hashing primitives also answer the everyday question of how to guarantee that files sent and received were not tampered with. In Kali you can hash a file with `sha256deep -r /home/kali/capture.pcap > hashes.txt`, which writes the digest into `hashes.txt`; you can locate the file with `ls -l hashes.txt` or read it with `cat hashes.txt`. Once you receive the original file, you compare the hashes. Different tools must give the same result, so the output of

```bash
sha256deep '/home/kali/capture.pcap'
sha256sum  '/home/kali/capture.pcap'
md5sum     '/home/kali/capture.pcap'
```

should be identical (within each algorithm).

### Modes of operation

AES supports several modes of encryption, and the mode — CBC, CFB, or CTR — imposes its own characteristics.

**CTR** requires counter synchronization. It turns any block cipher into a stream cipher: formally, when you run AES in CTR mode, blocks of random data are formed and combined with the original data to be encrypted, so that decryption is impossible without the key. In practice each of the 16 bytes is generated alike, making this a very conditional kind of pseudo-randomness. In essence it is XOR, which can be undone by autocorrelation.

**ECB** is suitable only for short messages: with the same plaintext and the same key you get the same ciphertext. When public-key encryption is used, ECB is in practice the mode employed — though only ever with a single "block" of plaintext. If the message is longer than 128 bits, ECB mode means that any repeated plaintext appears identically in the ciphertext, which lets an attacker infer structure. For that reason ECB is quite vulnerable.

**CBC** is slightly slower than ECB but considerably better. A vendor's price for a given mode can even differ — say, $100 for AES-128 in ECB but $1,000 for AES-256 in CBC. CBC mode is used sparingly nowadays, since SSH CBC ciphers are considered weak. We encrypt the message with AES and, by way of CBC, obtain a value for validating it.

A long text may be encrypted under a public key in CBC mode (in practice, of course, it is encrypted symmetrically, and the symmetric key is then encrypted under the public key). To decrypt, a password is entered to derive a symmetric key, which is used to decrypt the recipient's private key, which is used to decrypt the received encrypted symmetric key, which is finally used to decrypt the file.

CBC can also carry authentication (CBC-MAC), all on the block cipher. Sender and receiver share a secret key *k*; if it were one bit long, the attacker would have a 50% chance of guessing it, but with *n* bits the chance falls to 1/2ⁿ.

| Issue | ECB | CBC | CFB | CTR |
| --- | --- | --- | --- | --- |
| Easy to parallelize | Yes | No | No | Yes |
| Requires synchronization | No | No | No | Yes |
| Bit-error propagation | 1 block | 1 block + 1 bit | 1 block + 1 bit | 1 bit |
| Must implement both encrypt and decrypt | Yes | Yes | No | No |
| Requires padding | Yes | Yes | No | No |
| Position dependence | No | Yes | Yes | No |

---

## DES

DES, as a symmetric algorithm, always attracted controversy. Even so, it gave rise to much that was good. It was almost certainly adequate for a great many tasks in the 1970s and 1980s, and it set off an avalanche of research into block-cipher design. Today the consensus is that it was a sound algorithm let down by a short key length — a length introduced for political reasons. Modern cryptographic standards bring together people from many countries to produce methods that reflect the state of the art, and for that reason I consider them trustworthy. Even so, we cannot afford complacency, as the uproar over the standardization of the Dual_EC_DRBG random-bit generator reminds us.

---

## The Vernam Cipher

The Vernam cipher is an encryption algorithm beset with practical problems. Conceptually, however, it is ideal: it offers perfect secrecy, meaning that no information whatsoever can be extracted before decryption — in theory. The approach has three defining features: the number of keys equals the number of possible plaintexts; true randomness is required to generate the key; and the key may be used only once. Block ciphers do not increase the size of the data, and the Vernam cipher likewise operates bit by bit on XOR.

---

## Asymmetric Encryption

The principle of key generation differs greatly between symmetric and asymmetric cryptography. A symmetric key is just a random number, so a hardware source is best for generating a master key and pseudo-random numbers for local keys. Often a single key — call it *t* — is generated and used to deliver the rest. A useful rule of thumb: double the length of a symmetric key to obtain the equivalent hash-output length; this lets you withstand a preimage attack. Different algorithms generate keys differently, and vulnerabilities not infrequently lurk in the algorithm.

An asymmetric key is relatively harder to generate than a symmetric one. Asymmetric encryption is used in email. The asymmetry comes from the keys: one key encrypts, the other decrypts. The public key may be known to the entire world, and public-key encryption is a slow operation compared with symmetric encryption. The public key is the one you use to reach out from your machine to a server. Asymmetric cryptography also uses random numbers, but with a caveat. If we choose RSA, we must fix a key *d*; at 4608 bits the value is on the order of 2⁴⁶⁰⁸, though certain candidate values are discarded by particular rules.

It bears repeating that public-key encryption is used only during the TLS handshake, while the connection is first being set up (with SHA-384, for instance). Once the tunnel is established, faster symmetric cryptography takes over, and communication within the current session is encrypted under the symmetric keys just agreed. This is done for speed, since public-key cryptography demands far more computational power. The core purpose of asymmetric schemes is not to reduce the number of keys but to solve the key-distribution problem: you no longer need a trusted channel. Yet asymmetric encryption is slow, which is why the Diffie–Hellman protocol is used to derive a shared secret that is then encrypted with a symmetric algorithm.

To generate the simplest, lowest-level asymmetric key you need only an email address. These are exactly the certificates that hosts hand out "for free" for simple websites; a large online store, by contrast, must confirm its business registration. Keys can be generated on various devices, whether external or built-in. In real life, an HSM (Hardware Security Module) is used mainly in services for internet payments and the delivery of PINs over the internet. HSMs are not cheap — look up the prices of Luna Network HSMs and you will see. And if we want to be able to sign payments without revealing the amount, homomorphic encryption comes into play, letting us operate on data without decrypting it. Where we want similarity between pairs of objects to survive the hashing, that same homomorphic encryption is used.

When you buy something from a dubious online store, public-key cryptography — that is, asymmetric encryption — should always be in use. The protocol for buying anything online is SSL/TLS, which combines both kinds of cryptography. For the symmetric part, TLS uses AES or ChaCha20 (ChaCha20-Poly1305), or RC5/RC6 — RC5 with 2040 bits and RC6 with 4096. But our special attention will go to RSA. Almost always, the symmetric encryption in any network exchange runs on ChaCha20-Poly1305, which is included in TLS.

There are no "modes of operation" for public-key encryption, and essentially there is no need for any. Modes of operation mainly govern how to encrypt multi-block plaintexts safely; since public-key encryption is used to encrypt short symmetric keys, there is no need to define how to handle multi-block texts.

---

## RSA

Public-key cryptography is attractive for many applications, but it has its drawbacks. An attacker may have access to the ciphertext, and may also know the encryption key. The encryption algorithm may be proprietary — its details known only to the vendor — or it may be public, in which case the attacker knows how it works. Even then they know the algorithm but not the specific encryption and decryption process. And if you are a corporation that has built its own proprietary algorithm, it is still far from guaranteed that the details are absent from the internet: an attacker could buy a device containing the algorithm and reverse-engineer it. Hence a crucial requirement for any algorithm — its security must not rest on secrecy. It makes sense to choose a popular algorithm whose reliability has been confirmed by years of experience, by successful deployment between your network and your client's, and by the trust of experts. In short, the customary choice is RSA. It is a one-way function: easy to compute the encrypted result, hard to turn it back into the original values.

At the most primitive level, all RSA does is take two large prime numbers, multiply them, and form what is called the modulus. Every other number involved in the operations must lie between zero and the modulus. The algorithm is simple: first generate two large numbers, each at least 512 bits; multiply them; and choose a special property *e* (also a number). A worked illustration of forming the public-key pair:

```text
p = 45 and q = 84
n = pq = 45 * 84 = 3780
e = 25   (44 * 83 = 3652 / 25 = 146.08)
```

| RSA modulus length | Symmetric key length |
| --- | --- |
| 512 | 56 |
| 1248 | 80 |
| 2432 | 112 |
| 3248 | 128 |
| 15424 | 256 |

To speed up encryption, *e* is often chosen as 17, 257, or 65537 — all prime. The most popular is `0x10001` = 2¹⁶ + 1 = 65537, though 3, 5, 17, and 257 are also used, all of the form 2^(2ⁿ). This kind of value for *e* is preferred precisely because it is a power of two plus one, which makes computing `M^e mod n` fast. A larger *e* noticeably slows RSA down.

Different users may share the same exponent *e* as part of their public key, and that is not a problem. The important thing is to choose an *n* that is unlikely to coincide across many users. Because the moduli differ, the private key cannot be recovered from *e* alone.

If either of RSA's two functions turns out not to be one-way, consider RSA broken. Conceptually, it is not the key that is sent but an unencrypted message, while the little key stays on the sender's side — meaning the encryption and decryption keys differ. Take the message "3," which in bits is 00000011, raise it to the power 00000011⁴², and divide by 7,830,987,678. That is easy to compute but hard to reverse.

We obtain an enormous number, and it all seems secure. Let us do a little arithmetic. If a computer can perform a million operations per second, then a 30-bit key takes 2³⁰ ÷ 10⁶ — and since 2³⁰ is roughly 10⁹, the search takes about 10³ = 1,000 seconds, or 17 minutes. The larger the exponentiation, the more secure but the slower the encryption.

There is another algorithm — ElGamal. RSA is better than ElGamal in that it requires only a single exponentiation; on decryption, however, ElGamal wins on speed. If for some reason RSA does not appeal, ElGamal comes to the rescue. It is a public-key algorithm that can work over curves. It comprises three quantities: a special huge number, around 3072 bits, call it *p*; another number serving as a primitive, in the range between 1 and *p* − 1; and a private key, say 45. So with *p* = 23, *g* = 11, and a private key of 3, the result is y = 11³. The example looks simple, but encryption and decryption are more involved than in RSA. Breaking the algorithm without the private key is not impossible — nothing is — but I would call it very hard; to break ElGamal without the encryption key you would need to recover the ephemeral key. ElGamal is a reliable algorithm, while 3248-bit RSA is simply excellent. The discrete-logarithm problem becomes harder when it is defined over elliptic curves.

The RSA and ElGamal key length by modulus *n* would be 3248 (and for ElGamal we double the value), whereas RSA and ElGamal over an elliptic curve give a key length of 256.

When brainstorming how to break a system, we always think about attacks from the future. Thus the RSA exponent should be 1536 bits — a value considered secure against individual attackers — and 3072 against state-sponsored adversaries, looking ahead.

![A captured TLS exchange in which RSA and ECDSA appear](https://your-scorpion.ru/wp-content/uploads/2023/01/c098981ed779c129e509053b0934aa69.png)

In the image above you can see that at some point RSA was applied. But RSA should never be used for key exchange; here it is used to locate problems in the TLS connection. ECDSA is also visible.

One excellent example of a hybrid scheme, in my view, is ECIES. It is an authenticated public-key encryption scheme that uses a KDF (key-derivation function) to generate a separate media-access-control key and a symmetric encryption key from a shared ECDH secret. It is a more practical version of ElGamal, being hybrid — not fully asymmetric. ECDH works over elliptic curves. Because ECIES incorporates a symmetric cipher, it can encrypt any volume of data. In practice ECIES is used to keep keys safe on iOS. Its steps are:

- the inputs are the plaintext message and the recipient's ECC public key;
- an initialization vector (IV) is generated as random bytes;
- an ephemeral ECC key is generated as random bytes, the public part derived from the private;
- the ephemeral private key is combined with the recipient's public key — this is the ECDH shared secret;
- the shared secret is stretched with a KDF to produce two secret keys;
- one secret key encrypts the plaintext;
- the other secret key generates the MAC;
- the output is the ciphertext plus the IV plus the ephemeral public key plus the MAC.

---

## TLS

The most popular application of modern cryptography is encrypting network traffic. You send a message to a server, and an attacker intercepts it — yet cannot read its contents. That, in essence, is the core task of TLS. For network traffic, public certificates are built on SSL/TLS. If you want to create your own SSL certificate, there is the site [letsencrypt](https://letsencrypt.org/). In a complex case, certificates may be signed along a chain: RapidSSL → GeoTrust → Symantec → Broadcom → HP.

![A TLS handshake diagram](https://your-scorpion.ru/wp-content/uploads/2023/01/Group-33150.png)

The Transport Layer (TLS) lets a browser establish HTTPS — a secure connection between browser and server. You open your browser, and before the client–server dialogue begins, browser and server exchange TLS. TLS yields a secret key; the browser holds a list of root public keys, and the site has a certificate signed by one of them.

TLS is often called Secure Sockets Layer (SSL) because TLS is founded on SSL, one of the most widely used security protocols. SSL is a cryptographic protocol providing secure communication between user and server over an insecure network. TLS = SSL.

Encrypting absolutely everything asymmetrically is hard. To solve the performance problem, TLS uses hybrid encryption: the shared key for symmetric data encryption is sent from client to server encrypted under the server's public key; the server then decrypts it with its private key and uses it to exchange data with the client.

The obvious first problem is that TLS 1.0 and TLS 1.1 are obsolete protocols that should not be used — though in practice they are sometimes necessary. TLS 1.2 or TLS 1.3 should be the primary protocols, because these versions offer modern authenticated encryption (also known as AEAD). If you do not support TLS 1.2 or TLS 1.3 today, your security is inadequate. A stream cipher can be used, but a block cipher is the usual choice. TLS 1.3 supports GCM mode and ChaCha20-Poly1305.

TLS 1.3 did not appear without reason: every earlier version had vulnerabilities. Among the problems of earlier versions: error messages could hint at the first bit positions, betraying the MAC. Another attack is the protocol-downgrade attack, in which an outdated protocol is selected for data exchange through old browsers — particularly effective against mobile phones, where you can be forced down to 2G alone, which has vulnerabilities. Or a non-standard algorithm might be used: MD5 was never standardized, yet was popular in the past. (You can experiment with MD5 using a small utility such as HashMyFiles.) Alternative protocols include QUIC/SCTP and DTLS.

Although the encryption key sits openly on the internet, only the recipient with the decryption key can read the messages encrypted under it. Private companies place the key within an asymmetric-encryption arrangement, so that only authorized employees can read the messages. In asymmetric encryption, the public encryption key — usable by anyone to encrypt a message — is paired with a private encryption key that decrypts it. The process runs thus: the owner of the private key publishes the public key on the internet, or sends it to other users, who then use it to encrypt their email.

TLS uses asymmetric cryptography to securely generate and exchange a session key:

1. The client reaches the server over a secure URL (HTTPS…).
2. The server sends the client its certificate and public key.
3. The client checks it against a trusted root certificate authority to confirm the certificate is legitimate.
4. Client and server agree on the strongest encryption each can support.
5. The client encrypts the session (secret) key with the server's public key and sends it back.
6. The server decrypts the client's message with its private key, and the session is established.
7. The session key (symmetric encryption) is now used to encrypt and decrypt all data exchanged.

If there is no way to agree on a symmetric encryption key in advance, hybrid encryption or the Diffie–Hellman protocol is used. (Diffie–Hellman is only for working with keys.)

![A diagram of TLS record types](https://your-scorpion.ru/wp-content/uploads/2023/01/Group-47388.png)

In the image above one can pick out the ChangeCipherSpec, a simple single-byte value of `1`. The Alert is more involved: a value of `2` is a fatal error, on which TLS immediately kills the connection, while `1` is a warning.

So TLS has what is called a cipher suite — an additional set of cryptographic algorithms used to secure the TLS connection, negotiated during the handshake. The TLS record protocol uses symmetric cryptography to encrypt and protect the integrity of the exchanged data, using keys established during the handshake. There are many attacks on TLS, such as LogJam and FREAK. Many target version downgrade, so it is important to maintain optimal TLS settings. For block encryption, padding is added to the MAC before encryption, with a maximum padding size of 255 bytes. If an AES block comes to 16 bytes and the whole text — including MAC and padding — totals 79 bytes, then the padding length may be 1, 17, 33, and so on up to 161, because 79 + 161 = 240. With this knowledge one can already conclude that TLS suffices for communicating over a couple of protocols with a cloud server; once dozens of protocols are involved, you need a VPN.

### Intercepting session keys

A natural question is how the browser's session keys can be intercepted in the first place — for instance to inspect your own traffic in Wireshark. On Windows you can run a small script:

```bat
@echo off
killall chrome
set SSLKEYLOGFILE=F:\keylogfile.txt
start chrome
```

On macOS, in the terminal:

```bash
export SSLKEYLOGFILE=/user/Desktop/keylogfile.txt
open -a "Google Chrome.app"
```

Then, in Wireshark, point the configuration at the `keylogfile`.

![Configuring the key-log file in Wireshark](http://your-scorpion.ru/wp-content/uploads/2023/01/dda.png)

This will decrypt the traffic. You can then locate the frame of interest with a filter such as `frame contains "avito"` and study it.

---

## Diffie–Hellman

For TLS, the most common choice is ephemeral Diffie–Hellman (DHE), needed for the SSL handshake — or CHAP, or EAP. The essence of Diffie–Hellman is that client and server each hold a secret *K*, generated at random, and these *K* values are never transmitted over the network. RSA is layered on top for encryption. The result is the coveted *perfect forward secrecy*: if the secret keys are later compromised, past correspondence is not. The protocol does not account for authentication, which opens the door to a man-in-the-middle attack; this is solved with STS. Layering an STS protocol on top — whose full version provides a mutual key confirmation — brings you almost to an AKE (authenticated key exchange). Diffie–Hellman is preferred over RSA in TLS 1.3 precisely because it provides perfect forward secrecy.

Reality, as always, disappoints. PFS is often switched off so that — something else — will work; such advice appears in many official manuals. I know of a case where PFS was enabled on one router and disabled on the other, yet the tunnel came up and, somehow, it worked.

The algorithm runs like this. We have *p* = 14 and *g* = 173, the public components. The client takes a private key *a* = 25; the server does likewise with *b* = 78. Next the client applies the formula gᵃ = 173²⁵ mod *p*, and the server does the same with 173⁷⁸ mod *p*. In the end, server and client arrive at identical keys.

So the Diffie–Hellman protocol lets two parties exchange keys. If you are a bank that must store a key per customer, that is costly. Diffie–Hellman was devised back when there was no way to establish a secure connection in the first place. The protocol has several variants — static, ECDH, and ephemeral. The "station-to-station" protocol — the authenticated version of Diffie–Hellman — describes the construction best.

---

## Authentication

Authenticating the parties to a conversation means being confident of who you are talking to. Authentication matters especially in access control, which is part of broader cryptographic processes. If our task is to authenticate an employee with access to a room, we can give them a magnetic stripe which, together with a PIN, grants entry. Smart cards and tokens are likewise means of authentication. Do not confuse authentication (in the simple case, checking a password) with authorization (access to particular data): you can authenticate as an ordinary user and still have no authorized access to the admin panel. And the mere fact of providing data means nothing on its own — its freshness matters too.

In the common case, authentication requires a password, and that password must obviously be protected by cryptography. Many are familiar with **Salt** in Unix: a 12-bit number generated from the system clock, in the scheme known as DES+. Eight ASCII characters, seven bits each, are converted into a 56-bit DES key. But many systems modify the way passwords are stored and protected, and force the user to devise a strong one.

Authentication is needed whenever you must access an account. The browser asks for your login and password; the exchange happens after the TLS session is established, so the password cannot be intercepted. Similarly with card payment through a terminal, where a MAC is generated. There are several kinds of authentication. *Local* authentication — you entered a password on your own machine. *Direct* authentication — you enter a password to a server, and the password database is stored on that very server. *Indirect* authentication — a separate authentication server is used, with protocols such as RADIUS or Kerberos. *Ticket-based* authentication uses blocks of encrypted data, the way Kerberos works in Microsoft Active Directory. *Offline* authentication is where, on attempting to log in, you must present a certificate of an asymmetric key associated with the owner's name.

To prevent replay attacks against authentication, random-number generators are used. And in the world of IT a great deal runs on random numbers. When we say "random numbers" we mean a truly random probability, but in the computer world nothing is random: everything has structure, that structure is predictable, and therefore vulnerable. Does a 0 appear as often as a 1? Do 1s and 0s alternate with equal frequency? Does the string 1111 occur as often as 0000? If you thought of a uniform distribution of numbers, that has nothing to do with randomness. There are only two kinds of generator — non-deterministic and deterministic. The first relies on the physical world — noise, the bouncing of electrons — and requires special hardware. The deterministic kind produces pseudo-randomness: if you know the initial inputs you can predict the output, and identical inputs give identical outputs. A side-channel attack — breaking a system by its indirect signs — becomes possible.

An attack may also be physical. An RNG is the random-number-generation component in microcontrollers, used to create one-time passwords, to correct analog data, and for games. To obtain truly random numbers one might use sunlight, or a Geiger counter — since the background radiation level sits in a predictable range, yet the number generated is quite random. On a standard STM32, random numbers come from the reverse current of a diode, which flows from the diode onto a dedicated capacitor together with a small voltage; as long as the clock is running, generation works. A device usually has two generators, for security. Put yourself in the place of an attacker who has physically seized the device and is trying to influence the random-number generation with X-rays. Or a less sophisticated attacker simply files off the lid of the microcontroller and shines a laser into it to affect the generation — and they might succeed, obtaining several consecutive generations with the same output. For that reason a device protects itself with two independent generators, positioned inside the microcontroller so that it is impossible to influence both identically.

The standard requirement is data-origin authentication — confidentiality — which means a MAC and the key used to encrypt the data. To derive keys you can use the NIST schemes or HKDF, which is based on HMAC; PBKDF2 is an algorithm that increases resistance to brute force. A KDF may be based on a stream cipher, a block cipher, or a hash function: `SK → KDF → k1, k2, k3…`

---

## Key Hierarchy

A key hierarchy means that keys at a lower level are protected by keys at the level above. A three-tier hierarchy has a master key at the apex, which encrypts second-level keys; the second-level keys encrypt third-level keys. Second-level keys live shorter lives than first-level keys and are easier to change, and the third key is a session key. The most important thing is generating the master key, which is done in component form. Do you need to exchange a master key between two HSMs? Then either generate an identical key on both sides or exchange it via a special protocol.

### Digital signatures

The sender should receive confirmation of a message's delivery, and the receiver gains assurance that the sender is a particular person. That means everything was legitimate and the action is irreversible — important when signing, say, an apartment-rental agreement. The notion of an electronic signature can be interpreted as loosely as writing your name in the contact form to this article, but that is obviously a very weak signature. We would like to be able to identify the sender. An electronic signature is generated directly from the data it confirms, plus an additional secret parameter. A signature may pass through an arbiter, on whose side an extra MAC is added — but an extra participant in the data exchange is always a bad thing. The exchange may instead happen directly between bank and client, which is called asymmetry. An HSM is a special device in which one MAC can be used only by the client to create a MAC that the bank then verifies; in that case a judge could determine whether data bearing a given MAC was generated by the sender. Everyone must use a particular hash function, where RSA operates on blocks of bits — which makes RSA inefficient for signing large blocks of information. Each block is signed separately, and the blocks are in no way linked.

A real-world example drawing on the material above: issuing certificates to users. To issue a certificate you form a private and a public key plus a certificate-signing request, and submit it to a certificate authority, where a digital signature is formed with the CA's private key and returned to you. The method of delivering such a certificate to users varies with the size of the organization. In a small organization it may suffice to issue certificates through a corporate portal — though storing the private keys will raise questions. In a large organization, where much automation is required, we would more likely take the route of MDM with the SCEP protocol.

---

## Protocols

Encrypting data and keeping it on your own machine is interesting, but forwarding the data is more interesting still. What is a protocol? When you meet someone in the corridor, you say "how are you?" and smile — a protocol of behavior. In the computer world the protocol is TCP/IP for exchanging information between devices, and the world of cryptography has its own protocols. Protocols exist to solve the problem of transmitting data securely but, as always, we sacrifice something for performance. So encryption is standardized and called a protocol.

The basic structure of a cryptographic protocol:

1. what happens before the protocol;
2. who exchanges messages;
3. which information is present at which step;
4. what should happen at each step.

AKE protocols are very often adapted to the needs of a specific application. The two parties to an exchange need to be certain that on the other end of the wire there is no attacker — to exchange a shared symmetric key (ISO/IEC 9798-2:2019).

Another transport protocol is RPC (Remote Procedure Call), for invoking functions remotely on a server — very old. The RADIUS protocol handles authentication and authorization; instead of RADIUS one can use the newer Diameter. Such protocols simplify the administration of 802.11.

Other examples: PKCS for public keys, ISO/IEC 11770 for authentication, and SSL/TLS for a secure data-exchange channel — very often between a client and a web server. And it is not so very secure: SSL has no ability to control or even react to the content of a web page, only to its DNS address.

---

## PKI and Key Storage

Storing keys is, in itself, a bad idea. Ideally a key is generated on the fly — such implementations exist and work well — but generating a key requires a *seed*, and the seed itself must be stored somewhere. It is stored in the user's head, in the guise of a password or passphrase. And we may now recall that generating asymmetric keys is quite costly. The temptation arises to save the generated key inside the application's code, but that is insecure — better to use an HSM or a smart token.

It is a bad idea to reuse a key for the sake of convenience — for instance, to store on the device or in memory a PIN encrypted under a symmetric key. The encrypted PIN should only ever be encrypted with that key, never decrypted. Using the same symmetric key both to generate a MAC and to encrypt is always a bad idea; but if we decide to do so for optimization (a very common case), then derivation is needed. On top of that we layer SIV mode, reduce the encrypted-key string into other information, and add ANSI TR-31 — DES with an indication of the key's purpose.

So we gradually arrive at the question of key storage, since generating keys on the fly turned out to be hard. Keys have a life cycle of their own. Clearly, breaking the cryptography itself is not easy; it is far more promising for an attacker to gain access to a password through vulnerabilities in the key-storage system. Hence a dedicated infrastructure exists for keys — PKI, the key-storage system. PKI is a security technology that rests on the **X.509** standard and uses digital certificates. It can be realized as a separate device with a special cryptographic protocol, providing key backup. PKI usually works with asymmetry, because two keys are involved.

To be useful in practice, a public-key certificate must be created according to a predefined format. One very widely used format is the X.509 certificate, standardized in ISO/IEC 9594-8. X.509 certificates are used in various ways to support security on the internet, in particular within the TLS protocol.

Encryption lets you encrypt the data on a disk so that, even if the data is stolen, no harm is done. But the keys used to encrypt the disk must be stored somewhere — and if the key is lost, the data is lost. So we keep the key in a special TPM (Windows 11 requires a TPM chip in the computer). The key can automatically decrypt the data at the moment the computer starts. A key can be kept on a flash drive; some flash drives offer protection — the diskAshur Pro 500GB, for example, or the Kingston IronKey D300S.

There are also certificate authorities (CAs), which play three roles: creating certificates, revoking certificates, and confirming certificates. We need a strong binding between the owner of an asymmetric key and the key itself. But how can we be sure a CA is trustworthy? After all, a CA can do nothing but work with its own private keys. Increasing key lengths does not compensate for an insecure system, since overall security is only as strong as the weakest component. The same applies to the verifying computer — the one that uses the certificate. The private key, too, may be seized by an attacker. The conclusion: we cannot blindly trust the authorities. But here are a few worth considering: DigiCert, Entrust, thawte, Verisign, GoDaddy — or your own server. A PKI consists of a CMS (Certificate Management System), a VA (Validation Authority), a CA (Certification Authority), an RA (Registration Authority), DCs (Digital Certificates), and the end user.

It is always better to make sure a key comes from a trustworthy source, since a key is vulnerable both in transit and at rest. You need to know the owner's name, the asymmetric key itself, its validity period, and the digital signature. The most popular certificate format is X.509, as for PKI. The X.509 format is specified in a language called Abstract Syntax Notation One (ASN.1). The basic encoding rules involve three abbreviations: BER, DER, and CER, of which DER and CER are unambiguous. The main reason alternatives such as EMV certificates — Europay-MasterCard-Visa — were developed (rather than adopting X.509) was the need to minimize certificate length.

RFC 5280 describes how X.509 certificates are to be used in internet protocols — in the document's own terms, the X.509 *profile* (that is, a specification of how certain optional elements should be used). It contains a description of the X.509 format and discusses a range of other topics, including the use of certificate revocation lists (CRLs). CRLs solve a long-known problem of public-key certificates: once issued, they remain valid indefinitely. A certificate can of course include an expiry date, and the X.509 format explicitly allows this, but certificates are usually valid for several years. If the private key corresponding to the public key in a certificate is compromised, the certificate must be invalidated at once.

This problem can be solved with certificate revocation lists. A CRL is a list of all certificates issued by a given CA that are no longer valid. The CA must publish a new CRL at fixed, frequent intervals, which lets the CRL's users verify that the list they have received is current. But its principal role is to provide the data for authentication, for the hardest thing is key revocation. A self-signed certificate is secure in that it proves whoever created the certificate knows the private key corresponding to the public key within it. This can be useful, for example, when requesting that a CA create an "ordinary" certificate for a public key. The public key is contained within the certificate, so to speak. Better than a CRL is only OCSP. Self-certification is the certification of one's own public key.

It is almost impossible to control who has access to a public key. Fortunately, replacing a public key is fairly easy. But since we do not know who had access to the key, it is not certain we can guarantee delivery of the new key to all holders of the old one. An old key can be revoked in three ways: (1) a CRL, which is in effect a blacklist; (2) a whitelist; (3) a short certificate lifetime. To encrypt a key, a block cipher is used. For key exchange one can use quantum technology, where both sides must be able to exchange qubits through special devices; an attacker cannot eavesdrop on the signal without altering it. The protocol for transmitting qubits is called BB84 and has a distance limit of at most a few hundred kilometers. So transmitting keys via quantum means is already real today — the technology is called QKD.

Cryptographic material may be stored on the device, on the Secure *Enclave* processor in the case of iOS. For Android, as always, everything is more convoluted, but Bouncy Castle generally helps. Suppose your server has a private key "Kitty" and a public key "Nosey." During registration, the public key Nosey is sent to all devices. A device creates its own private key, "Scent," and associates it with the received public key — and ECDH takes place. Then, via SHA-256 over the server's public key Nosey and the private key Scent, two 128-bit values are formed: the signing key.

There is also identity-based encryption (IBE), in which a trusted third party (a TKC) participates in generating the certificate. Proof of identity *is* the public key, so no public-key certificate is required. The steps:

- **Encryption.** Masha obtains Somchan's public key PubB and encrypts a message with it, sending the ciphertext to Somchan.
- **Identification.** Somchan logs into his account and requests PrvtB.
- **Private-key extraction.** The TKC extracts PrvtB from PubB and supplies a special secret value of the TKC.
- **Private-key distribution.** Somchan receives PrvtB from the TKC.
- **Decryption.** Somchan uses PrvtB and reads Masha's message.

But there are conditions. Not every encryption algorithm is suitable for a TKC, and the TKC must always be online. This approach is used by large government institutions.

All of the above sounds reliable, yet in the end we still should not trust the signatures of root-server operators. There is always the possibility of forging a certificate by digging up the backbone fiber, splicing in a router, and reissuing the certificate. The private key would live on the router, and from there the attacker could decrypt everything passing through it. It sounds complicated, but when it comes to confrontation between states it is a more than realistic scenario.

**Key activation.** If a key is stored on the end user's computer, then activating it means using it. In the worst case the user must enter a code to activate the key. In a more complex case the key is generated on the fly or stored on an external device. But if you suddenly want to change a key out of schedule, that is quite expensive — especially a master key on an HSM, since you must regenerate every dependent key.

**Key destruction.** Sooner or later a key loses its relevance. As general advice, encryption should be managed centrally — in the simplest case at an HTTP gateway running Nginx and Certbot, which renews and deletes certificates in good time.

To consolidate: in symmetric encryption the same key is used for both encryption and decryption. When working with a public key there is no authentication, which means we cannot be sure that our colleague on the other end of the internet really is our colleague. It is a one-way function of that sort. To deliver a key, a device is often used — in other words, it is easier to hand the user a device with the key already installed than to load the key onto the device remotely.

### A practical aside: archiving and protecting a file from the command line

Since we are routinely working with files we wish to protect, it is worth knowing how to archive one from the command line. PowerShell has built-in functions, but I prefer the 7-Zip console executable. Download it, then point to the folder containing it — for example `cd D:\Download\0001-RTBT`. The `dir` command confirms that `7zr.exe` is in the folder. The command

```bat
.\7zr.exe a exfil.zip ..\New folder\ -p
```

creates the archive; in this example the `-p` flag also password-protects it. To extract:

```bat
.\7zr.exe e ..\file.zip
```

### A practical aside: generating your own SSH key

To create your own SSH key pair, it is enough to type `ssh-keygen` in any terminal. You will be prompted to save the private key on your device.

![ssh-keygen prompting where to save the key](https://your-scorpion.ru/wp-content/uploads/2024/05/ljwerkl.png)

Next, copy the path shown in the line *Your public key has been saved in (your path)*, type `cat ` followed by that path into the console, and you will see the generated public key. Add it to the device you wish to reach, then test the connection:

```bash
ssh -i "S:\folder1\folder2\.ssh\rsa_id" username@xx.xx.xx.xx
```

Now you can run commands such as `ls -R` to view files recursively.

---

## Applied Cases

### IoT

The pairing of SHA-256 with AES-128 (with the appropriate crypto-specifics such as salt, a packet counter, and so on) solves the security problem in IoT 99.(9)% of the time. Plain, already-familiar TLS (or DTLS) does the job. You can also bring in specialized hardware such as the cheap but capable ATSHA204A — making the system hard for a hacker to break. In LoRaWAN, user data is encrypted with AES-128 using a correspondingly sized 128-bit (16-byte) key. All of this can be implemented on dedicated chips like the ATSHA204A.

The SHA-2 family is currently the recommended minimum security level for IoT — along with Whirlpool, with its 512 bits and AES at its core. For SHA-256 there is a helpful [visualization](https://sha256algorithm.com/). Hash functions, like block ciphers, work in rounds: a fixed-size block is taken and compressed into a function of a defined length. There is also Merkle–Damgård, which underlies many hash functions. MD5, per RFC 1321, has a 128-bit hash function that is certainly not recommended for use. Where you see MD5, choose MD6, SHA-2, or SHA-3. SHA-3 is not a replacement for SHA-2; and Keccak — the hash function on which SHA-3 is based — transforms the input into something unreadable to an attacker.

A common question here is which hash to choose for a hobby project — is RIPEMD acceptable, for instance? RIPEMD uses two copies of MD4, both starting from the same initial value. Each MD4 instance has three rounds of compression and produces a 128-bit digest:

```text
F(X, Y, Z) = (X AND Y) OR (NOT X AND Z)
G(X, Y, Z) = (X AND Y) OR (X AND Z) OR (Y AND Z)
H(X, Y, Z) = X XOR Y XOR Z
```

Each of X, Y, Z here is a 32-bit value. Such simple construction looks too vulnerable; for anything serious, SHA-3 is the better choice. MD4 is more of a teaching exercise. SHA-3 is more reliable than SHA-2 at the same digest length — SHA3-256 is cryptographically stronger than SHA-256 — and it uses Keccak internally and is officially recommended in the United States; Keccak-256, in particular, is used in the Ethereum blockchain. BLAKE2b and its variants are also sound. RIPEMD-160 is used in Bitcoin and PGP, but simply reaching for RIPEMD-320 would be a poor idea, as its security has been called into question many times. In general I recommend SHA-2 and SHA-3 over RIPEMD. Never use MD5, SHA-0, or SHA-1, and likewise avoid Panama, HAVAL, SipHash, and Tiger; but do not disdain SHA-256, SHA3-256, Keccak-256, BLAKE2s, or RIPEMD-160.

### Base stations

Base stations use AES, a reliable algorithm whose use, even on minimally modern microcontrollers without a hardware-encryption block, incurs no significant overhead: on a 48 MHz Cortex-M3 a single 16-byte block encrypts in about 100 µs from scratch. Diffie–Hellman is not used here — too costly in performance for this class of device. Formally, the domestic Russian ciphers "Kuznyechik" and "Magma" may be used, though I have never encountered them in my own practice.

### Wi-Fi

Wi-Fi requires a secure channel and therefore uses WPA3 or WPA2, relying on a 256-bit encryption key and the Pre-Shared Key (PSK) authentication method. Wi-Fi uses a block cipher, with the key held in the router; in every case mentioned, the clients and the wireless access point use the same secret key. The updated WPA3 protocol adds built-in protection against brute-force attacks, an improved 192-bit cryptographic security suite, simpler device setup, and individualized encryption of information, all of which strengthens privacy on open Wi-Fi networks. Wi-Fi achieves authenticated encryption through CCM mode in WPA2 and GCM mode in WPA3.

You may occasionally still meet 3DES — ordinary DES that generates three distinct keys (which may be different from or identical to one another). But it is slow to encrypt, and its effective security can fall to 112 bits; in other words, the algorithm is leaky. AES is faster and better, even though it was originally designed for hardware. If the one trying to break in is the schoolboy next door, a 32-bit key will do. To defend against attacks on small organizations the key length was 80 bits in 2012 and 112 in 2022, and against quantum computers the key length is 256.

### Cryptocurrencies

Bitcoin's digital signature is ECDSA, and the signature can be verified by anyone holding the verification key. A public key is used to send cryptocurrency to a wallet. Bitcoin is essentially one-way, "offline" communication, like email. The DSA signature scheme borrows much from ElGamal, with the elliptic curve secp256k1. Cryptocurrencies use a private key to verify transactions and prove ownership of an address on the blockchain. If someone sends you one bitcoin (BTC), the private key is needed to "unlock" the transaction and prove that you now own that bitcoin.

### Mobile calls

Mobile calls rely on pre-distributed keys and do not need data-origin authentication. They use symmetric encryption and require a secure channel over a stream cipher. GSM calls are not encrypted fully end-to-end, but they are encrypted over much of their path, so that random people cannot simply listen in on phone conversations over the air, as one would a radio station. The exchange of encryption keys that establishes the secure link between your phone and the nearest cell tower happens at every new call setup; this exchange yields the keys that unlock the data for both your device and the tower. Because the mobile-telephony industry is very old by internet standards, you can still encounter the GEA-1 and GEA-2 encryption algorithms, used in the GSM networks of the first three generations, and they are quite vulnerable. They were supposed to fade away with LTE, but in fact many operators keep using them to this day for compatibility with old equipment.

Thanks to this weak protection, attacks can recover a key from intercepted traffic and use it to decrypt earlier traffic. How does this work? When two people call each other, a key is generated to encrypt the conversation. But that key may be used across several calls — it is not deleted the moment the call ends. Potentially, an attacker can call one of the two people who recently spoke, immediately after their conversation, and record the traffic; in this way the earlier conversation can potentially be decrypted with the obtained key.

For this reason mobile calls use a two-stage encryption process based on authentication and on encryption of the conversation. During authentication a certificate is generated, encrypted with RSA. But keep the backdoor in mind: it can be inserted either into the encryption algorithm or into the deterministic generator — witness Dual_EC_DRBG — or there is the ZUC algorithm, used by Chinese mobile operators.

### Identification cards

Identification cards provide visual identification, digital data, authentication, and a signing function. Obviously one needs to be able to confirm that an RFID card has not been modified since issue. For magnetic and bank cards the standard ISO/IEC 7810 applies, under which the name, card data, and additional information must fit into 250 bytes. For smart cards the standard ISO/IEC 7816 applies, and such cards are harder to clone than bank cards. They use public-key cryptography — RSA-1024 encryption, with newer cards up to 2048. Asymmetric key lengths used to be 1024 bits, but after several major cybersecurity incidents in the past the length is now 2048. Most cards hold a pair of authentication keys and a non-repudiation key pair. Vehicle entry, in its simplest form, is primarily an access-control mechanism and does not require establishing a secure data channel beyond what is needed to authenticate the subject; in a more complex variant a key hierarchy is included.

![A national eID card's certificate hierarchy](https://your-scorpion.ru/wp-content/uploads/2022/11/Group-33109.png)

More interesting than a parking-entry card is a citizen's identity document. For an ID card the root certificate will be RSA 2048-bit. The card holds five certificates: the root, the citizen's, the eID authentication certificate, the non-repudiation certificate, and the certificate of regulatory standards. Such a card is certified to X.509 version 3. It looks rather complex, and the eID issuance process involves several government bodies at once. A CRL is used to renew the certificate.

### Email

For encrypting email, both asymmetric and symmetric keys are used, and both methods provide the same level of security while working differently. The common goal is one: no one else can read the messages. Take Gmail as an example. The public key is embedded in the TLS/SSL certificate and is used to encrypt data from the sender. The private key resides in a separate file that must be stored securely on your server and can be used both to encrypt and to decrypt. The public key is embedded in the SSL certificate; the private key is kept on the server and held secret. To break an email message encrypted with asymmetric RSA — widely used to protect business correspondence — an attacker would need all the computing power currently on Earth, and even then it would take more than ten billion years.

Email protection is often associated with PGP (Pretty Good Privacy); desktop encryption, too, is frequently PGP, only called GPG. For cryptographic protection, S/MIME is added. PGP can not only encrypt and decrypt but also reduce a message's size. PGP is used to this day, especially under the brand Symantec Encryption Desktop, and it relies on the Web of Trust (WoT).

When encrypting, all headers below the application layer can be kept in the clear. It can be noted that some headers even at the application layer may be in the clear — for example, the email headers of encrypted email messages. Headers may be plaintext.

---

## A Catalogue of Attacks

There are many attacks; here is a list of the principal ones:

- Frequency Analysis
- Brute Force
- Trickery and Deceit
- One-Time Pad
- Plaintext
- Chosen-ciphertext
- Chosen-plaintext
- Chosen-key
- Rubber Hose
- Rainbow Table
- Ciphertext-only
- Man-in-the-middle
- Related-key
- Dictionary
- Timing
- Adaptive chosen-plaintext
- DROWN
- Side-channel
- Birthday
- Hash Collision
- DUNK
- Meet-in-the-middle
- Padding Oracle

Defending against every single attack is futile; the main thing is to adhere to the approaches set out in this article.

---

## A Short Glossary of Terms

| Term | Meaning |
| --- | --- |
| **Confidentiality** | In everyday, applied terms, the property that the message you send is encrypted and only the recipient can read it. |
| **Integrity** | The message has not been and will not be altered. |
| **Authentication** | We know for certain whom we are communicating with. |
| **Non-repudiation** | Sender and receiver cannot alter the message without clear signs of the change; usually achieved with timestamps and signatures. This automatically gives us authentication and integrity. (Authentication provides integrity, while confidentiality lives on its own.) |
| **Cryptography** | The transformation of information. |
| **Cryptanalysis** | The reverse: transforming encrypted information, without the original key, into something readable. |
| **Encryption** | The process that turns a message into a value unreadable to a human. |
| **Ciphertext** | The jumble of symbols transmitted over the internet. |
| **Decryption** | Transformation back into a human-readable format. |

---

<sub>Translated from the Russian original by Maksim Tsvetkov. Technical illustrations are hosted on the author's site. Worked numeric examples are reproduced as in the source and are intended as teaching illustrations rather than cryptographically valid parameter sets.</sub>
