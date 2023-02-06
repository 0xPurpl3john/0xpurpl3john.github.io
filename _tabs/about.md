---
title: About
icon: fas fa-info-circle
order: 4
---


> **Purpl3john**: `0xPurpl3john`

> #### **Where all began!**

#### **Try Harder!**

```Python
import base64
import random
import string

def xor_cipher(message, key):
    encrypted = []
    for i, char in enumerate(message):
        encrypted.append(chr(ord(char) ^ ord(key[i % len(key)])))
    return "".join(encrypted)

def decrypt(encrypted_message, key):
    decrypted = []
    for i, char in enumerate(encrypted_message):
        decrypted.append(chr(ord(char) ^ ord(key[i % len(key)])))
    return "".join(decrypted)

def random_string(length):
    letters = string.ascii_lowercase
    return "".join(random.choice(letters) for i in range(length))

encoded_message = "Tm8gc3lzdGVtIGlzIHNlY3VyZS4gVGhpbmsgYWJvdXQgaXQu"
key = random_string(len(base64.b64decode(encoded_message)))
message = base64.b64decode(encoded_message).decode("utf-8")
encrypted_message = xor_cipher(message, key)

print(decrypt(encrypted_message, key))
````