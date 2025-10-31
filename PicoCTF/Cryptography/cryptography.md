# 1. miniRSA

> Put in the challenge's description here

## Solution:
![alt text](image-5.png)
What I noticed first was that the value of 'e' and value of 'N' was given in the file aswell as the encrypted text. So I ran the encrypted text onto a RSA decoder online and got the flag.
![alt text](image-6.png)

## Flag:

```
picoCTF{n33d_a_lArg3r_e_606ce004}
```

## Concepts learnt:

- How to use an RSA decoder and how they work.

## Notes:
- none

## Resources:

- [RSA Decoder](https://www.dcode.fr/rsa-cipher)

# 2. Custom Encryption

Can you get sense of this code file and write the function that will decode the given encrypted file content. Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solution:
```python
def generator(g, x, p):
    return pow(g, x) % p        #function made to generate a key(modular exponentiation, a key component in DH key exchange algorithm)

def dynamic_xor_encrypt(plaintext, text_key): 
    cipher_text = ""                                 
    key_length = len(text_key)
    k = ""

    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        k += key_char
        encrypted_char = chr(ord(char) ^ ord(key_char)) #XOR operation
        cipher_text += encrypted_char #result
        print(k)
    return cipher_text      #function based on XOR cipher to encrypt/decrypt

p = 97
g = 31
a = 88
b = 26 

cipher = [97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]


v = generator(g, b, p)
key = generator(v, a, p)

print(key)

decipher = ""

for c in cipher:
    decipher += chr(c // 311 // key) #Reversing the first layer of encryption

print(dynamic_xor_encrypt(decipher, "picoCTF{")) #Reversing the second layer(XOR encryption)
```

![alt text](image.png)
First I ran the decryption program putting "picoCTF{" as the string to second decryption, which would give me the correct key "aedurtu" as the first 7 characters(due to the key length) of the string, so then I copy it and place it in place of "picoCTF{" and then run it to get the correct flag. 

![alt text](image-1.png)

## Flag:

```
picoCTF{custom_d2cr0pt6d_019c831c}
```

## Concepts learnt:

- Comprehending a decryption algorithm

## Notes:
- The inverse operation of XOR cipher is XOR itself
- Only 7 characters of the first output because decipher string was originally encrypted using a 7 character key  

## Resources:

- [geeks for geeks - decryption algorithm basics](https://www.geeksforgeeks.org/computer-networks/basics-of-cryptographic-algorithms/)
- [XOR Cipher Information](https://www.geeksforgeeks.org/dsa/xor-cipher/)
- [Enumerate function](https://www.geeksforgeeks.org/python/enumerate-in-python/)

# 3. RSA Oracle

> Put in the challenge's description here

## Solution:

```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ cat password.enc
2575135950983117315234568522857995277662113128076071837763492069763989760018604733813265929772245292223046288098298720343542517375538185662305577375746934snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ cat secret.enc
Salted__g���0' ^�       l�h��i���p2Έ�?�Lc}5ڧ��H�|UV;��h@�=��snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ nc titan.picoctf.net 61129
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 2575135950983117315234568522857995277662113128076071837763492069763989760018604733813265929772245292223046288098298720343542517375538185662305577375746934
Lol, good try, can't decrypt that for you. Be creative and good luck

what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: Salted__g���0' ^�       l�h��i���p2Έ�?�Lc}5ڧ��H�|UV;��h@�=��
```
First I catted password.enc and secret.enc, tried to run it through the website but didnt get much other than how it works.

```python
#RSA.py
from pwn import *

connection = remote('titan.picoctf.net', 61129)

#Get the first messages and send E to the program to choose Encryption
response = connection.recvuntil('decrypt.')
print(response.decode())
payload = b'E' + b'\n'

connection.send(payload)

response = connection.recvuntil('keysize):')
print(response.decode())

# We want to encrypt the number 2 so we send the ASCII value of 2.
payload = b'\x02' + b'\n'
connection.send(payload)
response = connection.recvuntil('ciphertext (m ^ e mod n)')
response = connection.recvline()

# We now have 2^e, we want to multiply by m^e which is the password
num=int(response.decode())*2575135950983117315234568522857995277662113128076071837763492069763989760018604733813265929772245292223046288098298720343542517375538185662305577375746934

# Decrypting process
response = connection.recvuntil('decrypt.')
print(response.decode())
payload = b'D' + b'\n'
connection.send(payload)

#We decrypt 2^e*m^e or (2m)^e, which gives us 2m
response = connection.recvuntil('decrypt:')
print(response.decode())
connection.send(str(num)+'\n')

response = connection.recvuntil('hex (c ^ d mod n):')
print(response.decode())
response = connection.recvline()
print(response.decode())

# Now that we have the response, convert it from hexadecimal and then divide it by  
num=int(response,16)//2
print(hex(num))

#Now we convert this to ASCII
hex_string=hex(num)[2:] # get rid of 0x
byte_array=bytes.fromhex(hex_string)
print(byte_array.decode('ascii'))

connection.close()
```

```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ python3 RSA.py
[+] Opening connection to titan.picoctf.net on port 61129: Done
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:6: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:12: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('keysize):')

enter text to encrypt (encoded length must be less than keysize):
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:18: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('ciphertext (m ^ e mod n)')
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:25: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')

what should we do for you?
E --> encrypt D --> decrypt.
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:31: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt:')

Enter text to decrypt:
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:33: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  connection.send(str(num)+'\n')
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:35: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('hex (c ^ d mod n):')
 decrypted ciphertext as hex (c ^ d mod n):
 c8c2607272

0x6461303939
da099
[*] Closed connection to titan.picoctf.net port 61129
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ cat password.enc
2575135950983117315234568522857995277662113128076071837763492069763989760018604733813265929772245292223046288098298720343542517375538185662305577375746934snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$  ^C
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ python3 RSA.py
[+] Opening connection to titan.picoctf.net on port 61129: Done
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:6: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:12: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('keysize):')

enter text to encrypt (encoded length must be less than keysize):
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:18: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('ciphertext (m ^ e mod n)')
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:25: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt.')

what should we do for you?
E --> encrypt D --> decrypt.
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:31: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('decrypt:')

Enter text to decrypt:
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:33: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  connection.send(str(num)+'\n')
/mnt/d/Cryptonite/PicoCTF/Cryptography/RSA.py:35: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  response = connection.recvuntil('hex (c ^ d mod n):')
 decrypted ciphertext as hex (c ^ d mod n):
 6468c4c6c4

0x3234626362
24bcb
[*] Closed connection to titan.picoctf.net port 61129
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Cryptography$ openssl enc -aes-256-cbc -d -in secret.enc -k 24bcb
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_24bcbc66}
```
Then I ran he RSA.py program that I created and then it prints the key, then as given in the hint I run "openssl enc -aes-256-cbc -d -in secret.enc -k 24bcb" and got the flag.


## Flag:

```
picoCTF{su((3ss_(r@ck1ng_r3@_24bcbc66}
```

## Concepts learnt:

- How the RSA Encryption Algorithm works
- How to "bypass" certain restriction while decrypting in RSA 

## Notes:

- RSA Encryption works by - m^e(mod n)
- openssl enc -aes-256-cbc -d -in secret.enc -k 24bcb
    i) openssl: is command line program
    ii) enc: to mention that we're encrypting 
    iii) -aes-256-cbc: tells which cipher to use where: aes-256 is the Advanced Encryption Standard with a 256-bit key and cbc is Cipher Block Chaining mode
    iv) -d: means that we want to decrypt
    v) -in secret.enc: specifies the input file
    vi) -k 24bcb: to provide the decryption process a key.

## Resources:

- [RSA Encryption Algorithm Basics](https://youtu.be/hm8s6FAc4pg?si=EqiamOScxJK7jUEA)
- [More basics on RSA](https://youtu.be/wcbH4t5SJpg?si=51G7J_WJNo7-09sV)
- [Modular Exponentiation](https://youtu.be/xcr-eyOgnTA?si=jhUJdUOIN5pWkqrB)

