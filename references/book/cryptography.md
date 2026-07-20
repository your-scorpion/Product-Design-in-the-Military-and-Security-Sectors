# The Foundations of Cryptography

*Maksim Tcvetkov · Product Design and Security Lead*

---

## Symmetric Encryption

The traffic between your phone and your mobile carrier, your ISP, or your bank is protected by symmetric encryption. Your browser sends and receives data the same way — not because symmetric encryption is the most secure scheme available, but because it is fast. Its keys are tiny: a 256-bit key is just 32 bytes, a 128-bit key only 16. If you want to feel this in your hands, encrypt something with a tool like BCTextEncoder and watch how little there is to move around.

Symmetric encryption uses a single key for both encryption and decryption. Sender and receiver share that one key and use it to exchange protected messages. The same mechanism applies when you encrypt files or a whole disk. iMessage, Signal, and WhatsApp all lean on symmetric encryption to keep your conversations private.

A symmetric algorithm depends on that shared key being distributed securely between the two parties — which is precisely the catch. Public-key cryptography works with two keys instead: one private and one public, the latter free to travel across the internet. A symmetric key, by contrast, is identical on both ends, and that identity does not scale. With Alice and Bob you need one key. With a thousand contacts you need on the order of 500,000 — `n(n−1)/2` of them. The count climbs steeply. The shared-key model also fails to provide non-repudiation: because Alice and Bob hold the same key, either of them can produce the same signatures on a message, so neither can later be pinned to it.

In symmetric encryption the plaintext can be processed either in blocks or bit by bit. The DEA block is 64 bits; AES uses 128. AES has a fixed 128-bit block, but for 256-bit keys you should choose 14 rounds. Truly random generation is computationally expensive, so pseudo-random generation is used to keep things fast. One important limitation: AES does not give you Perfect Forward Secrecy, so the moment an attacker obtains the shared key, everything becomes decryptable.

### A word on bits, bytes, and XOR

Pre-computer ciphers could work with letters. Inside a computer we work only with numbers: zeros and ones are *bits*, eight bits make a *byte*, and a word fits comfortably in two bytes — sixteen bits. At this depth there are no data types the way high-level languages know them; there are only bit patterns. You have surely seen values like `0x31` or `0xC0` scrolling past, or a string such as `0011000111000000`. That is the level at which the machine actually computes.

XOR is one of the fundamental operations on those bit sequences, and it earns its place in any cipher. Applied across a 128-bit key, XOR alone already lets an algorithm resist brute force; combine it with an ADD step and even frequency analysis stops being useful. It helps to keep two siblings in mind. AND takes two strings and keeps a `1` only where both inputs are `1`:

```
AX  0001011010111111
BX  1100110010101000
AND 0000010010101000
```

NOT simply inverts every bit — a `0` becomes a `1` — so `NOT AX` above yields `1101101000010111`. XOR sits between them in spirit, flipping a bit wherever the two inputs disagree, and that asymmetry is exactly what makes it so useful for mixing a key into data reversibly.

Symmetric keys vary in size, but the most common lengths are 128 and 256 bits. A well-chosen 256-bit key makes an organization's communications effectively immune to brute-force attack, provided the key itself is generated soundly.

### Block ciphers and padding

The first family of symmetric ciphers is the block cipher, mentioned above. Block size is a balancing act: too small and the cipher can be broken; too large and it becomes inefficient. DES used a 64-bit block — perfectly reasonable for 1970 — with a 56-bit key, giving it more than seventy-two quadrillion possible keys. Impressive on paper, but 128 is simply better.

Because a block cipher works in fixed chunks, real messages rarely divide evenly. Take a 348-bit message and a 128-bit block: it spans three blocks, or 384 bits, which leaves 36 bits with nothing in them. Filling that empty tail is called *padding*. Looking slightly ahead, padding is handled elegantly in RSA-OAEP, a well-known construction built on two hash functions. Note also that encryption cannot hide a message's length. And if you use a block cipher in a naive mode — ECB, with no clever chaining — identical plaintext blocks produce identical ciphertext blocks. Since blocks are small, repeats are common, and each repeat leaks information. An attacker can study the traffic, isolate a pattern, and build on it. The standard countermeasures are to send decoy traffic and to add padding.

### Stream ciphers

Where a block cipher transforms a 128-bit chunk at a time, a stream cipher transforms the individual bit. It is the second of the two most popular symmetric primitives: it uses XOR, its key is typically 128 bits, and it is very fast. RC4 is one implementation, but it is weak by modern standards, so the convention is to use a fresh key for every packet. RC4 is no good for exchanging payment data — yet it is perfectly acceptable for a military field radio, and you will still find it in WEP.

### MAC: integrity, not just secrecy

Now to the MAC — not the address burned into a network card at the factory, but the cryptographic Message Authentication Code. It is short, cheap to compute, and the most popular symmetric technique of its kind. Picture an attacker who intercepts a message encrypted with a stream cipher and flips a single bit. If that field happened to be a date, the date changes and no one notices, because it remains perfectly valid — just different. `12/12/2022` quietly becomes `11/12/2022`. In other words, encryption on its own offers no guarantee that a message arrives unaltered. To check integrity, the receiver recomputes the MAC over what arrived and compares it against the MAC that was sent; a mismatch means tampering. A MAC is not a hash function — a hash cannot vouch for the origin of data — even though a MAC may be *built* on a hash function and still be called a MAC. Because a MAC lengthens the transmitted message, it costs a little speed; its length is therefore usually kept to 32–64 bits, a small time penalty in exchange for meaningful added security. The MAC has many standards; one is ISO/IEC 9797.

Encryption by itself, then, gives no hint about where data came from, and when we care about that — and we usually do — symmetric encryption is paired with MAC over CTR. My own discipline is to encrypt first and then MAC. If the MAC fails verification, the receiver simply refuses the message. This approach uses two distinct keys: two keys mean two operations, which is costlier but safer. The construction has a name — Galois/Counter Mode, GCM, which is exactly CTR plus a MAC — and its output is ciphertext together with a MAC. That is how active attacks are caught.

So a MAC provides data-origin authentication and a degree of protection against active attacks, all because the key is symmetric. MACs are sometimes built on hash functions, but they are not hash functions, and hash functions on their own provide no resilience against forgery. Hashes *can* form part of a digital signature, which can provide non-repudiation — but, again, a digital signature is not a hash function.

Integrity, properly understood, is not only about encryption: it also demands authenticity (the date example above shows why). Integrity means the data was not changed in transit. Authenticity means the message really came from the expected sender. A MAC takes a context as input and emits a 64- or 128-bit string. Two classic attacks bear watching here: the forgery attack and the key-recovery attack. A forgery attack lets a third party, without the secret key, produce a valid MAC.

### Keeping time

Key exchange often needs the two parties to agree on time, and that is harder than it sounds. A computer has a clock; simpler devices, such as smart cards, may not. Even when both sides have clocks, they will drift apart sooner or later — air temperature alone is enough to nudge them — so time has to be synchronized against some standard. A MAC can help with this. The cleaner option is a *nonce* mechanism (a Number Used Only Once); and if you want to dream, a strontium atomic clock. The trade-offs among the synchronization methods are summarized below.

|                          | Clock | Sequence | Nonce             |
| ------------------------ | ----- | -------- | ----------------- |
| Requires synchronization | Yes   | Yes      | No                |
| Communication delay      | Yes   | Yes      | Yes               |
| Requires integrity       | Yes   | Yes      | No                |
| Minimum number of passes | 1     | 1        | 2                 |
| Special requirement      | Clock | Database | Random-number gen |

### Hash functions

Protecting data integrity against *accidental* errors is the job of checks like CRC. Hash functions are something else, and one of their best uses is storing passwords, because a hash never needs to be decrypted. A hash has no key and is usually much smaller than the original message, which means different files can share a hash. Consider a bank-card PIN: four digits is only 10,000 combinations, so a bank with a million customers has, on average, a hundred people sharing each PIN. Yet even if an attacker buys a database of *salted* hashed passwords, it buys them nothing — recovering the original characters from a hash value is computationally infeasible. Good hash functions resist inversion, and it is hard to tell which *identical* inputs hide behind an *identical* output. Push customers to five-digit codes and you have 100,000 possible PINs; the square root of 100,000 is just over 316 — a number worth remembering when you start thinking about collisions.

This is the right place to be precise about what "decrypting a hash" really means: you don't. You attack it. An analyst first assembles candidate passwords — scraping a target site for likely words with a tool such as `cewl`, or simply reaching for a ready-made list like `rockyou.txt` — and then computes their hashes. Generating a hash is trivial: on Linux, `echo -n "JLjh7567jHytjkkj/ddh" | md5sum` yields an MD5 digest, and `sha1sum` or `sha256sum` do the same for their families. With a list of candidate hashes in hand, a tool like `hashcat` runs through them — `hashcat -m 0 -a 0 -o cracked.txt md5_hashes.txt rockyou.txt` — comparing against the captured hash until something matches. The hash was never reversed; a guess simply collided with it. That distinction is the whole reason salted hashes protect passwords in the first place.

A hash lets you confirm integrity, but it is not a *trustworthy* integrity check on its own. It assures you that a file was not corrupted by a technical fault — no more. Mechanically, you are just comparing two hashes, the one you computed against the one you expected. In practice that looks like this: compute a file's digest with `sha256deep -r capture.pcap > hashes.txt` (or `sha256sum`, or `md5sum` — different tools must agree to the byte), and once you receive the original you compare. But an attacker walks straight past this: they substitute an illegitimate file *and* regenerate the hash to match it. A hash cannot detect malicious modification by an active adversary who also rewrites the hash — which is exactly why integrity in a hostile setting needs a MAC or a signature, not a bare checksum.

What a hash function *does* guarantee rests on three properties: preimage, second-preimage, and collision resistance. It is quick and easy to compute, it is one-way, and — to say it once more — it provides no non-repudiation.

Choosing a hash function deserves a moment, because the catalogue is full of traps. RIPEMD, for instance, runs two parallel copies of MD4; each copy has three compression rounds and emits a 128-bit sum, mixing the input with the boolean functions

```
F(X, Y, Z) = (X AND Y) OR (NOT X AND Z)
G(X, Y, Z) = (X AND Y) OR (X AND Z) OR (Y AND Z)
H(X, Y, Z) = X XOR Y XOR Z
```

over 32-bit words. It is elegant, but it is fundamentally a teaching construction and too weak for anything serious. For real work, prefer SHA-3, which is cryptographically stronger than SHA-2 at the same length — SHA3-256 is stronger than SHA-256 — and is officially recommended in the United States; its core, Keccak, also underpins the Ethereum blockchain as Keccak-256. BLAKE2b and BLAKE2s are likewise solid. RIPEMD-160 still appears in Bitcoin and PGP, but reaching for RIPEMD-320 instead would be a poor idea, since its security has been questioned repeatedly. The short version: never use MD5, SHA-0, or SHA-1; steer clear of Panama, HAVAL, SipHash, and Tiger; and do not hesitate to use SHA-256, SHA3-256, Keccak-256, BLAKE2s, or RIPEMD-160.

### Modes of operation for AES

AES can run in several modes, and the choice — CBC, CFB, CTR — imposes its own character on the cipher. AES itself also offers three key sizes — 128, 192, and 256 bits — and the larger the key, the harder the brute force; a 256-bit key is what you choose when you want communications to be practically unbreakable. In practice you rarely "build" such a key by hand; you derive it through a KDF, which also explains the recommendation of 14 rounds for 256-bit keys.

**CTR** requires the counter to stay synchronized. It turns any block cipher into a stream cipher: formally, AES in CTR mode produces blocks of pseudo-random data that are mixed with the data being encrypted, so without the key there is nothing to recover. In practice each of the sixteen bytes is generated alike, which makes this a very conditional sort of pseudo-randomness — at bottom it is an XOR that can be broken by autocorrelation.

**ECB** suits only short messages: the same plaintext under the same key always yields the same ciphertext. It is the mode effectively used for public-key encryption (though only ever with a single "block" of plaintext). For anything longer than 128 bits, ECB exposes structure — a phrase that appears twice in the plaintext appears identically in the ciphertext — which lets an attacker read the shape of your data. That is why ECB is so fragile.

**CBC** is slightly slower than ECB but far better. Vendors sometimes price the modes very differently — say, \$100 for AES-128 in ECB versus \$1,000 for AES-256 in CBC — and even CBC is now used sparingly, since SSH CBC ciphers are regarded as weak. CBC can also carry authentication as CBC-MAC, all on the block cipher: you encrypt the message with AES and obtain a hash for validation from the CBC chain. A long message can in principle be encrypted under a public key in CBC mode, though in practice it is encrypted symmetrically and only the symmetric key is wrapped with the public key. Decryption then runs as a chain: a password generates a symmetric key, which decrypts the recipient's private key, which decrypts the wrapped symmetric key, which finally decrypts the file. As for the underlying guarantee of secrecy: if the shared key `k` is one bit long, an attacker has a 50% chance of guessing it; if it is `n` bits long, the chance falls to 1/2ⁿ.

| Property                              | ECB     | CBC            | CFB            | CTR   |
| ------------------------------------- | ------- | -------------- | -------------- | ----- |
| Easy to parallelize                   | Yes     | No             | No             | Yes   |
| Requires synchronization              | No      | No             | No             | Yes   |
| Bit-error propagation                 | 1 block | 1 block + 1 bit| 1 block + 1 bit| 1 bit |
| Needs both encrypt and decrypt routines | Yes   | Yes            | No             | No    |
| Requires padding                      | Yes     | Yes            | No             | No    |
| Position-dependent                    | No      | Yes            | Yes            | No    |

---

## DES

DES, as a symmetric algorithm, always attracted controversy — and yet it gave rise to a great deal of good. It was almost certainly good enough for many tasks through the 1970s and 1980s, and it triggered an avalanche of research into block-cipher design. The consensus today is that it was a sound algorithm undermined by a short key length introduced for political reasons. Modern cryptographic standards are built through collaboration among people from many countries to produce state-of-the-art methods, and for that reason I consider them trustworthy. Even so, we cannot afford to be complacent — as the controversy over the standardization of the Dual_EC_DRBG random-bit generator made plain.

---

## The Vernam Cipher

The Vernam cipher is an encryption scheme riddled with practical problems and yet conceptually perfect: it offers perfect secrecy, meaning that — in theory — no information whatsoever can be extracted before decryption. The perfection comes with three demands. The number of keys must equal the number of possible plaintexts; the key must be truly random; and the key may be used exactly once. Where block ciphers do not expand the data, the Vernam cipher works bit by bit on XOR.

---

## Asymmetric Encryption

Key generation differs sharply between symmetric and asymmetric cryptography. A symmetric key is just a random number, so it is best to use a hardware source for a master key and pseudo-random numbers for local keys; often you generate a single key and use it to deliver the rest. A useful rule of thumb: double the symmetric key length to get the equivalent hash-output length, which lets you withstand a preimage attack. Different algorithms generate keys differently, and vulnerabilities frequently lurk in the algorithm itself.

An asymmetric key is relatively harder to generate than a symmetric one. Asymmetric encryption is what secures email. The asymmetry lives in the keys: one for encryption, another for decryption. The public key can be known to the entire world, and public-key encryption is a slow operation compared with symmetric encryption — the public key is the one you use to reach out from your machine to a server. Asymmetric cryptography also uses random numbers, but with a wrinkle: choose RSA, for example, and you must fix a key `d`; at 4608 bits, `d` lives in the range up to 2⁴⁶⁰⁸, although certain candidate values are discarded by specific rules.

It bears repeating that public-key encryption is used only during the TLS handshake, while the connection is first being set up (with SHA-384). Once the tunnel exists, faster symmetric cryptography takes over and everything within the session is encrypted under the symmetric keys just established. This hand-off exists purely for speed, because public-key cryptography demands far more computational power. The real point of asymmetric schemes is not to reduce the number of keys but to solve key distribution: you no longer need a trusted channel. Even so, asymmetric encryption is slow — which is why the Diffie–Hellman protocol is used to derive a shared secret that a symmetric algorithm then uses for the actual encryption.

Generating an asymmetric keypair can be as simple as a single command. `ssh-keygen` writes a private key to your device and a matching public key alongside it; you `cat` the path it reports to read the public key, install that public key on the host you want to reach, and then connect with `ssh -i "/path/to/private_key" username@host`. From there ordinary commands — say, `ls -R` to walk a remote tree — flow over a channel secured by that keypair. It is the lowest-level, most hands-on illustration of the whole idea.

At the bottom end, the simplest asymmetric key needs nothing more than an email address — these are the certificates handed out "for free" with basic websites by hosting providers. A serious online store, by contrast, must verify its business registration. Keys can be generated on a range of devices, external or embedded. In the real world, the HSM is used mostly in services for internet payments and for delivering PINs over the internet, and HSMs are not cheap — look up the price of Luna Network Hardware Security Modules and you will see. And if you want to sign payments without revealing their amounts, you reach further still, for homomorphic encryption, which lets you operate on data without decrypting it; when you also want a hash to preserve similarity across pairs of objects, homomorphic encryption is again the tool.

When you buy something from a dubious online shop, public-key cryptography — asymmetric encryption — should always be in play. The protocol for buying anything online is SSL/TLS, combining the two flavours of cryptography. On the symmetric side, TLS uses AES or ChaCha20 (ChaCha20-Poly1305), or RC5/RC6 — RC5 with 2040 bits and RC6 with 4096. But our particular focus will be RSA. Almost always, the symmetric layer of any network exchange runs on ChaCha20-Poly1305, which is built into TLS.

There are no modes of operation for public-key encryption, and essentially there is no need for them. Modes of operation exist mainly to define how to securely encrypt multi-block plaintexts; since public-key encryption is used to encrypt short symmetric keys, there is nothing multi-block to worry about.

---

## RSA

Public-key cryptography is attractive for many applications, but it has its drawbacks. An attacker may have access to the ciphertext, and may even know the encryption key. The algorithm may be proprietary — its details known only to the vendor — or it may be public, in which case the attacker knows how the algorithm works without knowing the particular encryption and decryption process. And even a corporation that builds its own proprietary algorithm cannot assume its details are absent from the internet: an attacker can buy a device that implements the algorithm and reverse-engineer it. Hence a cardinal requirement for any algorithm — its security must never rest on secrecy. It makes sense to choose a popular algorithm whose strength has been confirmed by years of experience, by successful deployment between your network and your client's, and by the trust of experts. Put plainly, the convention is to choose RSA. It is a one-way function: easy to compute the encrypted result, hard to turn it back into the original values.

At the most primitive level, all RSA does is take two large prime numbers, multiply them, and form what is called the modulus. Every other number it operates on must lie between zero and the modulus. The procedure is simple: generate two large numbers — at least 512 bits each — multiply them, and pick a special property `e`. The toy walk-through below uses small stand-in numbers purely to show the shape of the calculation (they are not genuine primes):

```
p = 45 and q = 84
n = pq = 45 × 84 = 3780
e = 25      (44 × 83 = 3652 / 25 = 146.08)
```

The relationship between modulus length and equivalent symmetric strength looks like this:

| RSA modulus length | Symmetric-key length |
| ------------------ | -------------------- |
| 512                | 56                   |
| 1248               | 80                   |
| 2432               | 112                  |
| 3248               | 128                  |
| 15424              | 256                  |

To speed encryption, `e` is often chosen as 17, 257, or 65537 — all primes. The most popular is `0x10001` = 2¹⁶ + 1 = 65537, though 3, 5, 17, and 257 also appear, all of the form 2^(2ⁿ) + 1. Such an `e` is preferable because being a power of two makes `Mᵉ mod n` fast to compute; a larger `e` noticeably slows RSA down. Many users will share the same exponent `e` in their public keys, and that is no problem. What matters is choosing an `n` unlikely to be shared by many users, because the moduli differ and the private key cannot be recovered from `e` alone.

If either of RSA's two functions turns out not to be one-way, consider RSA broken. Conceptually, what is sent is not the key but an (un)encrypted message, while the little key stays on the sender's side, so the encryption and decryption keys differ. Take the message "3", which in bits is `00000011`; raise it to a power — `00000011⁴²` — and reduce modulo `7,830,987,678`. That is easy to compute and hard to invert.

The result is an enormous number, and it all looks secure. A little arithmetic shows why. If a computer performs a million operations per second, then a 30-bit key takes 2³⁰ / 10⁶ operations to exhaust; since 2¹⁰ ≈ 10³, we have 2³⁰ ≈ 10⁹, so the search runs in about 10⁹ / 10⁶ = 10³ = 1000 seconds, or roughly 17 minutes. The larger the exponentiation, the more secure — and the slower the encryption. There is an alternative algorithm, ElGamal. RSA is better than ElGamal in that it needs only a single exponentiation; ElGamal, however, wins on decryption speed.

If for some reason RSA does not appeal, ElGamal comes to the rescue. It is a public-key algorithm that works over curves and is built from three quantities: a special, huge number — around 3072 bits, call it `p`; another number acting as a primitive, between 1 and `p − 1`; and a private key, say 45. So with `p = 23, g = 11, pk = 3`, the result is `y = 11³`. The example looks simple, but encryption and decryption are more involved than RSA's. Breaking the algorithm without the private key is not impossible, but I would call it very hard: to break ElGamal without the encryption key, you would need to learn the ephemeral key. ElGamal is a reliably strong algorithm, while 3248-bit RSA is simply excellent — and the discrete-logarithm problem becomes harder still when it is defined over elliptic curves.

The key length of RSA and ElGamal modulo `n` works out to 3248 bits (and for ElGamal you double the value), whereas RSA and ElGamal over an elliptic curve yield a key of just 256 bits. When we brainstorm how to break a system, we always reason about attacks from the future. On that footing, the exponent in RSA encryption should be 1536 bits — considered robust against individual attackers — and 3072 bits against state-sponsored adversaries, with an eye to what is coming.

![RSA in a TLS exchange](https://your-scorpion.ru/wp-content/uploads/2023/01/c098981ed779c129e509053b0934aa69.png)

In the image above you can see that, at a certain point, RSA was applied. But RSA should never be used for key exchange; here it is being used to diagnose problems in a TLS connection. You can also make out ECDSA.

One of the finest examples of a hybrid scheme, in my view, is ECIES. It is an authenticated public-key encryption scheme that uses a KDF — a key-derivation function — to generate a separate media-access-control key and a symmetric encryption key from a shared ECDH secret. It is a more practical version of ElGamal precisely because it is hybrid, not fully asymmetric; ECDH itself runs over elliptic curves. And because ECIES includes a symmetric cipher, it can encrypt any volume of data. In practice, ECIES is used to keep keys secure on iOS. Its steps run as follows:

- The inputs are the plaintext message and the recipient's ECC public key.
- An initialization vector (IV) is generated — random bytes.
- An ephemeral ECC key is generated — random bytes — and its public key is derived from its private key.
- The ephemeral private key is combined with the recipient's public key; that combination is the ECDH shared secret.
- The shared secret is stretched with a KDF to produce two secret keys.
- One secret key encrypts the plaintext.
- The other secret key generates a MAC.
- The output is ciphertext + IV + ephemeral public key + MAC.

---

## TLS

The most popular use of modern cryptography is encrypting network traffic. You send a message to a server and an attacker intercepts it — but they cannot read its contents, and that, in essence, is what TLS is for. For network traffic, public certificates are built on SSL/TLS. If you want to create your own SSL certificate, there is [Let's Encrypt](https://letsencrypt.org/). In a complex case, certificates can be signed along a chain — RapidSSL → GeoTrust → Symantec → Broadcom → HP.

![TLS handshake](https://your-scorpion.ru/wp-content/uploads/2023/01/Group-33150.png)

Transport Layer Security lets your browser establish `httpS` for a secure connection between browser and server. You open the browser, and before any client/server dialogue begins, the two exchange TLS. TLS yields a secret key; the browser holds a list of root public keys, and the site presents a certificate signed by one of them. TLS is often called Secure Sockets Layer (SSL) because SSL is its foundation and one of the most widely used security protocols — a cryptographic protocol that secures communication between a user and a server over an insecure network. For our purposes, TLS = SSL.

Encrypting absolutely everything asymmetrically is hard, so to solve the performance problem TLS uses hybrid encryption: the shared key for symmetric data encryption is sent from client to server encrypted with the server's public key, after which the server decrypts it with its private key and uses it for the exchange.

There is a way to watch all of this on your own machine, which makes the abstraction concrete. Your browser can be told to log its session keys to a file, and Wireshark can then use that file to decrypt the very traffic you generate. On Windows you set the `SSLKEYLOGFILE` environment variable and launch the browser fresh:

```
@echo off
killall chrome
set SSLKEYLOGFILE=F:\keylogfile.txt
start chrome
```

On macOS the same idea, from the terminal:

```
export SSLKEYLOGFILE=/Users/you/Desktop/keylogfile.txt
open -a "Google Chrome.app"
```

Point Wireshark at that keylog file, and the captured TLS frames become readable — find the one you care about with a filter such as `frame contains "avito"` and inspect the exchange. It is the cleanest way to see session keys doing their job rather than merely reading about them.

The first obvious issue is that TLS v1.0 and v1.1 are obsolete and should not be used, though in practice they are sometimes necessary. TLS v1.2 or v1.3 should be your primary protocols, because these versions offer modern authenticated encryption — AEAD. Failing to support v1.2 or v1.3 today means your security is inadequate. You can use a stream cipher, but a block cipher is the usual choice; TLS 1.3 supports GCM and ChaCha20/Poly1305.

TLS 1.3 did not arrive for nothing — every earlier version carried vulnerabilities. Error messages could hint at the position of the first bits, which let an attacker work out the MAC. Another class is the protocol-downgrade attack, in which an outdated protocol is chosen for the exchange via old browsers; this works especially well against mobile phones, where you can be forced down to 2G and its weaknesses. Or a non-standard algorithm was used — MD5 was never standardized but was popular in its day, and you can experiment with it using a small utility like HashMyFiles. Among the alternatives are QUIC and SCTP + DTLS.

Although the encryption key is publicly available on the internet, only the recipient holding the decryption key can read messages encrypted with it. Private companies position the key in this asymmetric arrangement so that only authorized employees can read messages. In short, asymmetric encryption uses a public encryption key — which anyone may use to encrypt a message — and a private decryption key, with which the message can be decrypted; the owner of the private key publishes the public key online or sends it to others, who then use it to encrypt their mail. TLS uses this asymmetric cryptography to securely generate and exchange a session key:

- The client reaches the server over a secure URL (HTTPS…).
- The server sends the client its certificate and public key.
- The client validates it against a trusted root certificate authority to confirm the certificate is legitimate.
- Client and server agree on the strongest type of encryption both can support.
- The client encrypts the session (secret) key with the server's public key and sends it back.
- The server decrypts the client's message with its private key, and the session is established.
- The session key — symmetric encryption — is now used to encrypt and decrypt all data between client and server.

When there is no way to agree on a symmetric key in advance, you use hybrid encryption or the Diffie–Hellman protocol — which exists only to handle keys.

![TLS record and alert layer](https://your-scorpion.ru/wp-content/uploads/2023/01/Group-47388.png)

In the image above you can read off the Change Cipher Spec, a simple single-byte value `1`. The Alert is subtler: a value of `2` is a fatal error and TLS will immediately kill the connection, whereas `1` is a warning.

TLS, then, has what is called a cipher suite — an additional set of cryptographic algorithms used to protect the connection, negotiated during the handshake. The TLS record protocol uses symmetric cryptography to encrypt and protect the integrity of the exchanged data, using keys established as part of the handshake. There are many attacks on TLS, such as LogJam and FREAK, and many of them aim at downgrade, which is why keeping optimal TLS settings matters. For block encryption, padding is added to the MAC before encryption, up to a maximum of 255 bytes. If an AES block is 16 bytes and the whole text with MAC and padding comes to 79 bytes, the padding length can be 1, 17, 33, and so on up to 161 — because 79 + 161 = 240. With this much understood, you can already conclude that TLS is enough to talk to a cloud server over a couple of protocols. Once you are dealing with dozens of protocols, you need a VPN.

---

## Diffie–Hellman

For TLS, the most common choice is ephemeral Diffie–Hellman (DHE), which the SSL handshake needs — or CHAP, or EAP. The essence of Diffie–Hellman is that client and server each hold a secret `K`, generated at random, and these `K` values are never sent across the network; RSA is layered on top to handle the encryption. The reward is the coveted *perfect forward secrecy*: if the secret keys are later compromised, past correspondence is not. The protocol does not account for authentication, which opens the door to a man-in-the-middle attack — resolved with STS. Layer the STS protocol on top, whose full version provides mutual key confirmation, and you are nearly at an authenticated key exchange. Diffie–Hellman is preferred over RSA in TLS 1.3 precisely because it provides perfect forward secrecy.

Reality, as always, disappoints. PFS is often switched off so that something *else* will work — advice you will find in plenty of official manuals. I know of a case where one router had PFS enabled and the other did not, and yet the tunnel came up and somehow worked.

The mechanics run like this. We have `p = 14` and `g = 173`, the public components (small, schematic values here). The client picks a private key `a = 25`, and the server does likewise with `b = 78`. The client then applies `gᵃ mod p` = `173²⁵ mod p`, and the server applies `173⁷⁸ mod p`. The result is that server and client arrive at the same key.

So the Diffie–Hellman protocol lets two parties exchange a key. If you are a bank that must store a key per customer, that is costly; Diffie–Hellman was designed back when there was no way to set up a secure connection at all. It has several implementations — static, ECDH, and ephemeral — and the authenticated version of the protocol is best described as the "station-to-station" protocol.

---

## Authentication

Authenticating the party you are talking to means being confident about who that party is. It matters most in access control, which is part of broader cryptographic processes. If the task is to authenticate an employee for entry to a room, you can issue them a magnetic stripe that, together with a PIN, grants access; smart cards and tokens are authentication methods too. Do not confuse authentication — in the simplest case, checking a password — with authorization, which is access to particular data; you can authenticate as an ordinary user and still have no authorized access to the admin panel. And the mere fact of presenting data means nothing on its own; its freshness matters too.

In the common case, authentication requires a password, and obviously that password must be protected by cryptography. Many will recognize **Salt** in Unix: a 12-bit number generated from the system clock. The scheme is called DES+ — eight ASCII characters, each seven bits, converted into a 56-bit DES key — though many systems now modify how passwords are stored and protected and force the user to choose a strong one.

Authentication is needed whenever you want access to an account. The browser asks for your login and password, and the exchange happens after the TLS session is established, so the password cannot be intercepted. It is the same with a card payment through a terminal, where a MAC is generated. There are several kinds of authentication. *Local* authentication: you enter a password on your own machine. *Direct* authentication: you enter a password to a server whose password database lives directly on that server. *Indirect* authentication: a separate server is used, with protocols such as RADIUS or Kerberos. *Ticket-based* authentication, built on blocks of encrypted data, is how Microsoft's Kerberos works in Active Directory. *Offline* authentication is where a login attempt requires presenting an asymmetric-key certificate associated with the owner's name.

To prevent replay attacks against authentication, random-number generators are used — and, more broadly, a great deal of IT runs on random numbers. When we say "random," we mean genuinely random probability, but in the computer world nothing is truly random. Everything has structure, and structure is predictable, which means vulnerable. Does a `0` appear as often as a `1`? Do `1`s and `0`s alternate with equal frequency? Does the string `1111` appear as often as `0000`? If you found yourself thinking about a uniform distribution of numbers, note that this has nothing to do with randomness. There are only two kinds of generator: non-deterministic and deterministic. The first relies on the physical world — noise, the bouncing of electrons — and requires special hardware. The deterministic kind produces pseudo-randomness: know the initial inputs and you can predict the output; feed it the same input and you get the same result. That predictability makes a side-channel attack — breaking the system by indirect signs — possible.

Such an attack can also be physical. An RNG is the random-number-generation component in microcontrollers, used to create one-time passwords, to correct analog data, and for games. To obtain truly random numbers you can use sunlight, or a Geiger counter — background radiation sits within a predictable range, yet the generated number is quite random. On a standard STM32, random numbers are produced from the reverse current of a diode, which flows into a dedicated capacitor along with a small voltage; generation runs as long as the clock is on. A device usually has two generators, for safety's sake. Put yourself in the place of an attacker who has physically seized the device and is trying to influence its number generation with X-rays. Or a less sophisticated one who simply files off the microcontroller's lid and shines a laser inside to skew the output — and it might work, yielding several generations in a row with the same number. That is exactly why a hardened device places two independent generators inside the chip, positioned so that no single influence can affect both identically.

The standard requirement here is data-origin authentication — together with confidentiality — and that means a MAC plus the key used to encrypt the data. For deriving keys you can use NIST schemes or HKDF, which is based on HMAC; `pbkdf2` is a function and PBKDF2 is the algorithm, designed to increase resistance to brute force. A KDF can be based on a stream or block cipher, or on a hash function, and it expands one secret into many: SK → KDF → k1, k2, k3, …

---

## Key Hierarchy

A key hierarchy means that keys at a lower level are protected by keys above them. A three-level hierarchy has a master key at the top, which encrypts second-level keys; the second-level keys encrypt third-level keys. Second-level keys live shorter lives than first-level keys and are easier to rotate, and the third-level key is the per-session key. The crucial step is generating the master key, which is done in component form. Need to share a master key between two HSMs? Then either generate the same key on both sides, or exchange it via a special protocol.

This is where the electronic signature enters. The sender should receive confirmation of delivery, and the receiver should receive a guarantee that the sender is a particular person — so that everything was legitimate and the action irrevocable. This matters when signing documents to let an apartment, for instance. You could interpret "electronic signature" loosely — as simply typing your name into a web form — but that is plainly a very weak signature, and what we actually want is the ability to identify the sender. A proper electronic signature is generated directly from the data it confirms, plus an additional secret parameter. A signature may pass through an arbiter, on whose side an additional MAC is added — but an extra party in any data exchange is always a bad thing. The exchange can happen directly between the bank and the client, which is what asymmetry buys you. An HSM is a dedicated device in which one MAC can be used only by the client to create a MAC that the bank verifies; in that case a judge can determine whether data bearing a given MAC was generated by the sender. Everyone must use a specific hash function — and because RSA operates on bit blocks, it is inefficient for signing large blocks of information; each block is signed separately, and the blocks are in no way linked.

A real case that ties the material together: issuing certificates to your users. To issue a certificate you generate a private and a public key plus a certificate-signing request, and hand them to a certificate authority, where the CA's private key forms a digital signature that comes back to you. The delivery method scales with the size of the organization. In a small organization, issuing certificates through a corporate portal may suffice — though storing the private keys raises questions. In a large organization that needs heavy automation, you are more likely to take the MDM route with the SCEP protocol.

---

## Protocols

Encrypting data and keeping it on your own machine is interesting, but sending it is far more so. What is a protocol? When you meet someone in the hallway you say "how are you?" and smile — a protocol of behavior. In the computer world, the protocol is TCP/IP for exchanging information between devices; in the world of cryptography there are protocols of its own. They exist to solve the problem of transmitting data securely, but as always we sacrifice something for performance, and so the encryption is standardized and called a protocol.

The basic structure of a cryptographic protocol is fourfold:

1. what happens before the protocol;
2. who exchanges messages;
3. what information is present at each step;
4. what should happen at each step.

AKE protocols are very often adapted to the needs of a specific application. The two parties to an exchange must be certain that the other end of the wire is not an attacker, and must exchange a shared symmetric key — see ISO/IEC 9798-2:2019. Another transport protocol, RPC (Remote Procedure Call), is for invoking functions remotely on a server; it is very old. The RADIUS protocol handles authentication and authorization; in its place you can use the newer Diameter. Such protocols simplify the administration of 802.11. Other examples: PKCS for public keys, ISO/IEC 11770 for authentication, and SSL/TLS for a secure data channel — very often between a client and a web server. And it is not all that secure: SSL has no ability to control, or even react to, the *content* of a web page — only to its DNS address.

---

## PKI and Key Storage

Storing keys is, in itself, a bad idea. Ideally a key is generated on the fly, and such an implementation exists and works well — but generating a key needs a *seed*, and the seed has to be stored somewhere, namely in the user's head as a password or passphrase. And now we can recall that generating asymmetric keys is rather costly. The temptation is to save the generated key inside the application's code, but that is insecure; better an HSM or a smart token.

It is a bad idea to reuse a created key for the sake of convenience — for example, to store a PIN on a device or in memory encrypted under a symmetric key. That encrypted PIN should only ever be encrypted with the key, never decrypted with it. Using the same symmetric key for both MAC generation and encryption is always a bad idea; but if we decide to do it as an optimization — a very common situation — then derivation is required. On top you add SIV mode, fold the encrypted-key string into other information, and screw down ANSI TR-31, with DES carrying an explicit statement of the key's purpose.

So we come, step by step, to the question of storing keys — since generating them on the fly turned out to be hard. Keys have a life cycle. Breaking cryptography itself is clearly not easy, so it is far more promising for an attacker to reach the password through weaknesses in the key-storage system itself. That is why a dedicated infrastructure exists for keys: PKI, a key-storage system. PKI is a security technology built on the **X.509** standard that uses digital certificates. It can be realized as a separate device with a special cryptographic protocol that also provides key backup, and it usually works with asymmetric cryptography, because two keys are involved.

To be useful in practice, a public-key certificate must be created according to a predefined format. One very widely used format is the X.509 certificate, standardized in ISO/IEC 9594-8; X.509 certificates support internet security in many ways, in particular within the TLS protocol.

Encryption lets you encrypt the data on a disk, so that even if the data is stolen, no harm is done — but the keys that encrypted the disk must live somewhere, and if the key is lost, the data is lost. That is why we store the key in a dedicated TPM. Windows 11 requires a TPM chip in the computer; the key can automatically decrypt the data the moment the computer starts. You can also keep the key on a USB stick — some sticks offer protection, such as the diskAshur Pro 500GB or the Kingston IronKey D300S.

There are also certificate authorities (CAs), which play three roles: creating certificates, revoking them, and validating them. We need a strong binding between the owner of an asymmetric key and the key itself. But how do we know a CA is trustworthy? After all, a CA can do nothing except work with its own private keys. Lengthening keys does not compensate for an insecure system, because overall security is only as strong as the weakest component — and the same applies to the *verifying* computer, the one that uses the certificate. The private key, too, can be seized by an attacker. The conclusion is that we cannot blindly trust the authorities. Still, here are a few worth considering: DigiCert, Entrust, thawte, Verisign, GoDaddy, or your own server. A PKI consists of a CMS (Certificate Management System), a VA (Validation Authority), a CA (Certification Authority), an RA (Registration Authority), the DC (Digital Certificates), and the end user.

It is always better to be sure a key came from a trustworthy source, because a key is vulnerable both in transit and at rest. You need to know the owner's name, the asymmetric key itself, its validity period, and its digital signature. The most popular certificate format is X.509, as with PKI. The X.509 format is specified in a language called Abstract Syntax Notation One (ASN.1). Its basic encoding rules come in three abbreviations — BER, DER, CER — of which DER and CER are unambiguous. The main reason alternatives such as EMV certificates (Europay-MasterCard-Visa) were developed, rather than adopting X.509, was the need to minimize certificate length.

RFC 5280 describes how X.509 certificates should be used in internet protocols — in the document's own language, this is the X.509 *profile*, a specification of how certain optional elements should be used. It contains a description of the X.509 format and discusses a number of other topics, including the use of certificate revocation lists (CRLs). CRLs solve a long-known problem with public-key certificates: once issued, they remain valid indefinitely. Of course a certificate can include an expiry date, and the X.509 format explicitly allows this, but certificates are usually valid for several years. If the private key corresponding to the public key in a certificate is compromised, the certificate must be invalidated immediately.

That problem can be solved with certificate revocation lists. A CRL is a list of all certificates issued by a given CA that are no longer valid. The CA must publish a new CRL at fixed, frequent intervals, which lets a CRL user check that the CRL they received is current — and the principal role of all this is to verify the data used for authentication, since revocation is the hardest part of the whole business. A self-signed certificate is secure in that it proves whoever created it knows the private key corresponding to the public key in the certificate; this can be useful, for instance, when requesting that a CA create a "normal" certificate for a public key. The public key is contained within the certificate, if one can put it that way. Better than a CRL is only OCSP. Self-certification is simply certifying your own public key.

It is almost impossible to control who has access to a public key. Fortunately, replacing a public key is fairly easy. But since we do not know who had access to it, we cannot be sure we can deliver the new key to everyone who held the old one. There are three ways to revoke an old key: a CRL, which is in effect a blacklist; a whitelist; or a short certificate lifetime. A block cipher is used to encrypt the key.

For key exchange you can also use quantum technology, where both parties must be able to exchange qubits through special devices. An eavesdropper cannot intercept the signal without altering it. The protocol for transmitting qubits is called BB84 — and it has a distance limit, a few hundred kilometres at most. So exchanging keys over quantum links is already real today; the technology is called QKD.

Cryptographic material can be stored on the device — on the Secure *Enclave* in the case of iOS. On Android, as ever, everything is more complicated, but Bouncy Castle generally helps. Suppose your server holds a private key we'll call *Whiskers* and a public key *Paws*. During registration, *Paws* is sent to all devices. The device creates its own private key, *Pounce*, and associates it with the public key it received; an ECDH exchange takes place. Then, via SHA-256 over the server's public key *Paws* and the device's private key *Pounce*, two 128-bit values are formed — the signing key.

There is also identity-based encryption (IBE), in which a trusted third party (the TKC) takes part in generating the certificate. The proof of identity *is* the public key, so a public-key certificate is not required. The steps run like this:

- **Encryption.** Masha obtains Somchai's public key, PubB, encrypts a message with it, and sends the ciphertext to Somchai.
- **Identification.** Somchai logs into his account and requests PrvtB.
- **Private-key extraction.** The TKC derives PrvtB from PubB, providing a special TKC secret value.
- **Private-key distribution.** Somchai receives PrvtB from the TKC.
- **Decryption.** Somchai uses PrvtB and reads Masha's message.

There are conditions, though. Not every encryption algorithm is suitable for a TKC, and the TKC must always be online. This approach is used by large government institutions.

Everything above sounds dependable, yet in the end we still should not trust the signatures of the owners of the root servers. There is always a way to forge a certificate — by digging up the backbone fibre, splicing in a router, and reissuing a certificate. The private key sits on the router, and from there the attacker can decrypt everything that passes through it. It sounds difficult, but when the contest is between nation-states, it is more than realistic.

**Key activation.** If a key is stored on the end user's computer, activating it means using it; in the worst case the user must enter an activation code. In a more elaborate case the key is generated on the fly, or kept on an external device. But if you suddenly want to change a key unplanned, that is quite expensive — especially a master key on an HSM, because you will have to regenerate every dependent key.

**Key destruction.** Sooner or later a key loses its relevance. As general advice, encryption should be handled centrally; in the simplest case, on an HTTP gate running Nginx and Certbot, which renews and removes certificates in good time.

To consolidate: symmetric encryption uses the same shared key for both encryption and decryption. Working with a public key offers no authentication, so we cannot be sure that the colleague on the other end of the internet really is our colleague — it is that kind of one-way function. And to deliver a key, a device is often used: it is simpler to hand a user a device with the key already installed than to push the key onto the device remotely.

---

## Applied Cases

**IoT.** A pairing of SHA-256 with AES-128 — with the appropriate crypto specifics such as a salt, a packet counter, and so on — solves 99.9% of the security problem in IoT. Plain, familiar dTLS does the rest. You can also bring in specialized hardware such as the cheap but capable ATSHA204A, which makes life hard for an attacker. In LoRaWAN, user data is encrypted with AES-128 using a correspondingly sized 128-bit (16-byte) key. All of this can be realized on dedicated chips like the ATSHA204A.

The SHA-2 family is, at present, the recommended minimum security level for IoT — as is Whirlpool, with its 512 bits and AES-based core. For SHA-256 there is even a [visualization](https://sha256algorithm.com/). Hash functions, like block ciphers, work in rounds: a fixed-size block is fed into a function of a set output length — or via Merkle–Damgård, which underlies many hash functions. MD5, per RFC 1321, is a 128-bit hash function and is certainly not recommended. The rule of thumb: see MD5, choose MD6, SHA-2, or SHA-3. SHA-3 may also be used, though it is not a replacement for SHA-2; and Keccak — the hash function on which SHA-3 is based — transforms the input into something an attacker cannot read.

**Base stations** use AES — a reliable algorithm whose use, even on minimally modern microcontrollers that lack a hardware encryption block, carries no significant overhead: on a 48 MHz Cortex-M3, one 16-byte block encrypts in roughly 100 µs from scratch. Diffie–Hellman is not used here — too costly in performance for this class of device. Formally, the Russian "Kuznyechik" and "Magma" ciphers may be used, but in my own practice I have never once encountered them.

**Wi-Fi** requires a secure channel, so it uses WPA3 or WPA2, relies on a 256-bit encryption key, and authenticates with a Pre-Shared Key (PSK). Wi-Fi uses a block cipher, and the key lives in the router; in every such case the clients and the wireless access point share the same secret key. The updated WPA3 protocol adds built-in protection against brute-force attacks, an upgraded cryptography standard in the form of a 192-bit security suite, simpler device setup, and individualized encryption of information, which strengthens privacy on open Wi-Fi networks. You will occasionally meet 3DES: ordinary DES generating three keys, which may be different or the same. It is slow to encrypt and its effective security can fall to 112 bits — in other words, a leaky algorithm. AES is faster and better, even though it was originally made for hardware. If the one trying to break you is the schoolkid next door, a 32-bit key is enough; for protecting small organizations the key length was 80 bits in 2012 and 112 bits in 2022, and against quantum computers it is 256. Wi-Fi manages authenticated encryption through CCM mode in WPA2 and GCM mode in WPA3.

**Cryptocurrencies.** Bitcoin's digital signature is ECDSA, and anyone with the verification key can verify it. The public key is used to send cryptocurrency to a wallet, and Bitcoin is, in essence, one-way "offline" communication, much like email. The DSA signature scheme borrows much from ElGamal, here with the elliptic curve secp256k1. Cryptocurrencies use the private key to verify transactions and prove ownership of an address on the blockchain: if someone sends you one bitcoin, the private key is what "unlocks" the transaction and proves that you now own it.

**Mobile calls** rely on pre-shared keys and do not need data-origin authentication. They use symmetric encryption and require a secure channel over a stream cipher. GSM calls are not encrypted fully end-to-end, but they are encrypted along many stretches of their path, so random people cannot simply listen to phone conversations out of the air like a radio station. The exchange of encryption keys that establishes a secure connection between your phone and the nearest cell tower happens every time a call is newly initialized, and that exchange hands the keys to unlock the data to both your device and the tower. Because the mobile-calling industry is rather old by internet standards, you can still meet the GEA-1 and GEA-2 encryption algorithms used in the first three generations of GSM networks — and they are quite vulnerable. With the arrival of LTE they should have faded into history, but in fact many mobile operators keep using them today for compatibility with old equipment.

Thanks to that weak protection, attacks can recover a key from intercepted traffic and use it to decrypt earlier traffic. How does it work? When two people call each other, a key is generated to encrypt the conversation — but the resulting key can be used across several calls; it is not deleted the moment the conversation ends. So an attacker can call one of the two who just spoke, immediately after their conversation, and record the traffic, potentially decrypting the previous conversation with the recovered key. For this reason mobile calls use a two-stage process — authentication, then encryption of the conversation. During authentication a certificate is generated, encrypted with the RSA algorithm. But keep a backdoor in mind: it can be inserted either into the encryption algorithm or into the deterministic generator — Dual_EC_DRBG is the example — or there is the ZUC algorithm used by Chinese mobile operators.

**Identification cards.** They provide visual identification, digital data, authentication, and a signing function. Obviously you need to be able to confirm that an RFID card has not been modified since issuance. For magnetic and bank cards the standard is ISO/IEC 7810, under which you must fit a name, the card data, and additional information into 250 bytes. Smart cards use ISO/IEC 7816, and such cards are harder to copy than bank cards. They use public-key cryptography — RSA 1024, and newer ones up to 2048. Asymmetric keys used to be 1024 bits, but after several major cybersecurity incidents in the past, asymmetric keys are now 2048 bits. Most cards carry an authentication keypair and a non-repudiation keypair. Vehicle entry, in its simplest form, is primarily an access-control mechanism and needs no secure channel for transmitting data beyond what is required for authenticating the subject; in a more complex variant, a key hierarchy is built in. A citizen's identity document is more interesting than a parking card: for ID documents the root certificate is RSA 2048 bit, and the card holds five certificates — root, citizen, eID authentication, non-repudiation, and a regulatory-standards certificate. Such a card is certified under X.509 version 3; it looks fairly complex, and the process of issuing an eID involves several government organizations at once. A CRL is used to renew the certificate.

**Email.** Both asymmetric and symmetric keys are used to encrypt email, and both methods provide the same level of security while working differently; the shared goal is that no one else can read the messages. Take Gmail as an example. The public key is embedded in the TLS/SSL certificate and used to encrypt data from the sender; the private key sits in a separate file that must be securely stored on your server and can be used for both encryption and decryption. The public key is built into the SSL certificate, while the private key is kept secret on the server. To break an email message encrypted with asymmetric RSA — widely used to protect business correspondence — an attacker would need all the computing power currently on Earth, and even then it would take more than ten billion years.

Email protection is often associated with PGP (Pretty Good Privacy); desktop encryption, too, is frequently PGP, only there it is called GPG. If you need cryptographic protection, you add S/MIME. PGP can do more than encrypt and decrypt — it can also reduce a message's size; it is still used today, especially under the Symantec Encryption Desktop brand, and it relies on the Web of Trust. That compress-then-encrypt habit has a familiar command-line analogue when you simply want to bundle and protect a file at rest: the 7-Zip console binary handles it well. After dropping the executable into a folder and confirming it is there with `dir`, the command `.\7zr.exe a archive.zip ..\folder\ -p` creates a password-protected archive — the `-p` flag prompts for the password — and `.\7zr.exe e ..\archive.zip` extracts it again. PowerShell has built-in functions for this too, but the standalone tool is the one I reach for.

A note on headers: when you encrypt, all headers below the application layer can be kept in the clear, and even some application-layer headers can remain in cleartext — for instance, the email headers of encrypted email messages. Headers, in short, can be plain text.

---

## A Catalogue of Attacks

There are a great many attacks; the principal ones are listed below.

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

Defending against every single attack is futile; the important thing is to hold to the approaches set out in this article.

---

## A Short Glossary of Terms

**Confidentiality** — in everyday, applied terms, the property that a message you send is encrypted and only the recipient can read it.

**Integrity** — the message has not been and will not be altered.

**Authentication** — we know for certain whom we are talking to.

**Non-repudiation** — neither sender nor receiver can alter the message without clear signs of the change; this is usually achieved with timestamps and signatures, and it automatically gives us both authentication and integrity. (Authentication, in turn, provides integrity, while confidentiality stands on its own.)

**Cryptography** — the transformation of information.

**Cryptanalysis** — the reverse: turning encrypted information, without the original key, back into something readable.

**Encryption** — the process that turns a message into a value unreadable by a human.

**Ciphertext** — the jumble of characters transmitted across the internet.

**Decryption** — the transformation back into a human-readable form.
