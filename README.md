# nacl-enigma

A fun cryptography demonstrator to give libsodium.js an Enigma / substitution-cipher-like UI


## Design

Adapting a modern symmetric cipher to an "Engima"-like typewriter experience. This means:

- 1 plaintext character <-> 1 ciphertext character (for the most part)
- typable ciphertext on a keyboard
- "reversible" typing process (decryption to plaintext is the same typable process as "encrypting")

while keeping modern symmetric cipher primitives, provided by `libsodium`:

- modern key stretching of the passphrase
- modern cipher with authentication via AEAD construction 


## Implementation

1. a restricted typable charset of 32 characters `[ !’,.a-z?]` is chosen and all plaintext is interpreted as such a `base32`-encoded byte array
2. keys are derived from passphrase using `Argon2id` via `libsodium.crypto_pwhash`
3. the nonce is randomly generated and encoded via above `base32` and described as a "preamble"
4. plaintext bytes are encrypted (currently with `XSalsa20-Poly1305`) via `libsodium.secretbox`
	  1. (Plaintext is padded to mod8 characters with trailing spaces (` `) to retain readability while not introducing another padding character)
5. ciphertext bytes are encoded back to `base32`. The stream cipher construction with typable `base32` ensures a character-by-character substitution cipher-like effect
6. the MAC is described as a "tamper-proof seal code" and can be used to authenticate
