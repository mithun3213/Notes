


### What is a **Hash**?

A **hash** is the result of applying a **hash function** to some input data. It turns any input (text, file, password, etc.) into a **fixed-length string of characters**, usually in hexadecimal.


| Hash Type      | Length (Hex) | Bit Size | Example                                                            |
| -------------- | ------------ | -------- | ------------------------------------------------------------------ |
| MD5            | 32           | 128-bit  | `5f4dcc3b5aa765d61d8327deb882cf99`                                 |
| SHA-1          | 40           | 160-bit  | `b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3`                         |
| SHA-256        | 64           | 256-bit  | `916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745` |
| SHA-512        | 128          | 512-bit  | `cf83e1357eefb8bdf1542850d66d8007d620...`                          |
| NTLM           | 32           | 128-bit  | Looks like MD5 but used by Windows                                 |
| bcrypt         | 60           | variable | `$2y$12$eImiTXuWVxfM37uY4JANjQ==`                                  |
| Argon2         | ~95          | variable | Starts with `$argon2`                                              |
| LM Hash        | 32           | 128-bit  | Uppercase only, rarely used now                                    |
| Base64 Encoded | varies       | -        | Often ends with `=` or `==`, has `/`, `+`                          |

