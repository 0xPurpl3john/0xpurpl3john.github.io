---
title: About
icon: fas fa-info-circle
order: 4
---


> # **Purpl3john**: `0xPurpl3john`

> ### **Where all began!**

#### **Try Harder!**

```Python
import random
import time
import base64

def display_message(message, border_char):
    message_len = len(message)
    border_len = message_len + 4
    border = border_char * border_len
    print(border)
    print(border_char + " " + message + " " + border_char)
    print(border)

def random_char():
    return chr(random.randint(33, 126))

def random_message(message, hide_message):
    message = list(message)
    hidden_message = [" " for char in message]
    for i, char in enumerate(message):
        if i % hide_message == 0:
            hidden_message[i] = char
        message[i] = random_char()
        time.sleep(0.01)
        print("\033c", end="") # Limpa a tela
        display_message("".join(hidden_message), "#")
    print("\033c", end="") # Limpa a tela
    display_message(message, "#")

def decode_message(encoded_message):
    decoded_message = base64.b64decode(encoded_message).decode("utf-8")
    return decoded_message

if __name__ == "__main__":
    encoded_message = "Tm8gc3lzdGVtIGlzIHNlY3VyZS4gVGhpbmsgYWJvdXQgaXQu"
    decoded_message = decode_message(encoded_message)
    hide_message = 3
    random_message(decoded_message, hide_message)

````