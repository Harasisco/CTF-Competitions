![image](https://github.com/Harasisco/CTF/assets/87074807/ba7c4ecb-5d04-457a-86a0-e0267c3a20ad "Description")
 
# Solution

#### we get a text file contain a python byte code assembly:

```python
  2           0 LOAD_CONST               1 (0)
              2 LOAD_CONST               0 (None)
              4 IMPORT_NAME              0 (base64)
              6 STORE_FAST               0 (base64)

  3           8 LOAD_CONST               1 (0)
             10 LOAD_CONST               2 (('Fernet',))
             12 IMPORT_NAME              1 (cryptography.fernet)
             14 IMPORT_FROM              2 (Fernet)
             16 STORE_FAST               1 (Fernet)
             18 POP_TOP

  4          20 LOAD_CONST               3 (b'gAAAAABj7Xd90ySo11DSFyX8t-9QIQvAPmU40mWQfpq856jFl1rpwvm1kyE1w23fyyAAd9riXt-JJA9v6BEcsq6LNroZTnjExjFur_tEp0OLJv0c_8BD3bg=')
             22 STORE_FAST               2 (encMessage)

  5          24 LOAD_FAST                0 (base64)
             26 LOAD_METHOD              3 (b64decode)
             28 LOAD_CONST               4 (b'7PXy9PSZmf/r5pXB79LW1cj/7JT6ltPEmfjk8sHljfr6x/LyyfjymNXR5Z0=')
             30 CALL_METHOD              1
             32 STORE_FAST               3 (key_bytes)

  6          34 BUILD_LIST               0
             36 STORE_FAST               4 (key)

  7          38 LOAD_FAST                3 (key_bytes)
             40 GET_ITER
        >>   42 FOR_ITER                 9 (to 62)
             44 STORE_FAST               5 (k_b)

  8          46 LOAD_FAST                4 (key)
             48 LOAD_METHOD              4 (append)
             50 LOAD_FAST                5 (k_b)
             52 LOAD_CONST               5 (160)
             54 BINARY_XOR
             56 CALL_METHOD              1
             58 POP_TOP
             60 JUMP_ABSOLUTE           21 (to 42)

 10     >>   62 LOAD_GLOBAL              5 (bytes)
             64 LOAD_FAST                4 (key)
             66 CALL_FUNCTION            1
             68 STORE_FAST               4 (key)

 11          70 LOAD_FAST                1 (Fernet)
             72 LOAD_FAST                4 (key)
             74 CALL_FUNCTION            1
             76 STORE_FAST               6 (fernet)

 12          78 LOAD_FAST                6 (fernet)
             80 LOAD_METHOD              6 (decrypt)
             82 LOAD_FAST                2 (encMessage)
             84 CALL_METHOD              1
             86 LOAD_METHOD              7 (decode)
             88 CALL_METHOD              0
             90 STORE_FAST               7 (decMessage)

 13          92 LOAD_GLOBAL              8 (print)
             94 LOAD_FAST                7 (decMessage)
             96 CALL_FUNCTION            1
             98 POP_TOP
            100 LOAD_CONST               0 (None)
            102 RETURN_VALUE
None
```
#### By looking to the code we find it use the fornet module for encrypting the FLAG u can read more about it [here](https://cryptography.io/en/3.4.4/fernet.html "fornet documentation").

```
 3           8 LOAD_CONST               1 (0)
            10 LOAD_CONST               2 (('Fernet',))
            12 IMPORT_NAME              1 (cryptography.fernet)
            14 IMPORT_FROM              2 (Fernet)
            16 STORE_FAST               1 (Fernet)
```

#### just the key is not randomly generated it’s base64 encoded value XORed with key “160”.

```
5          24 LOAD_FAST                0 (base64)
           26 LOAD_METHOD              3 (b64decode)
           28 LOAD_CONST               4 (b'7PXy9PSZmf/r5pXB79LW1cj/7JT6ltPEmfjk8sHljfr6x/LyyfjymNXR5Z0=')
           30 CALL_METHOD              1
           32 STORE_FAST               3 (key_bytes)    8          46 LOAD_FAST                4 (key)
           48 LOAD_METHOD              4 (append)
           50 LOAD_FAST                5 (k_b)
           52 LOAD_CONST               5 (160)
           54 BINARY_XOR
```
#### so we can solve it in two different ways :
## first solution:

#### using [cyberchef](https://gchq.github.io/CyberChef/) to generate the key:

![image](https://github.com/Harasisco/CTF/assets/87074807/cd34987e-bbad-4503-8746-17527e6b9baa)

#### then we need to decodes the token I will use [Fernet (Decode)](https://asecuritysite.com/tokens/ferdecode):

![image](https://github.com/Harasisco/CTF/assets/87074807/13891480-4e14-4292-871e-c8c74e5faebf)

#### And we got the flag.

## second solution:

#### writing a python script:

```python
import base64
from cryptography.fernet import Fernet

encMessage = b'gAAAAABj7Xd90ySo11DSFyX8t-9QIQvAPmU40mWQfpq856jFl1rpwvm1kyE1w23fyyAAd9riXt-JJA9v6BEcsq6LNroZTnjExjFur_tEp0OLJv0c_8BD3bg='

key_bytes = base64.b64decode(b'7PXy9PSZmf/r5pXB79LW1cj/7JT6ltPEmfjk8sHljfr6x/LyyfjymNXR5Z0=')

key = []

for k_b in key_bytes:
    key.append(k_b ^ 160)

key = bytes(key)

fernet = Fernet(key)

decMessage = fernet.decrypt(encMessage).decode()

print(decMessage)
```

#### Thanks for reading.
