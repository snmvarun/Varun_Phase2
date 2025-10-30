# 1. IQ Test
let your input x = 30478191278.

wrap your answer with nite{ } for the flag.

As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

## Solution:
```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/Hardware$ file iqtest.png
iqtest.png: PNG image data, 1225 x 2176, 8-bit colormap, non-interlaced
```
My first thought was to check whats up with the file and its metadeta but found nothing interesting and scratched that idea, since the flag contents were similar to binary, I thought to check its input in binary which gave a 36 bit binary number where would each bit cooresponds to the x0,..x35, that would be put into the logic circuit of the image to get the output binary. Doing it similar to the example, when I take x=30478191278, its binary equivalent is 011100011000101001000100101010101110. Cooresponding each of the output and concatting them, the 12 bit binary number is 100010011000, which are the flag contents.
![alt text](image-2.png)

## Flag:
```
nite{100010011000}
```

## Concepts learnt:

- Navigating through circuit diagrams
- Data Representation 

## Notes:

- Steganography was not an approach to take.

## Resources:

- none

# 2. i_like_logic

i like logic and i like files, apparently, they have something in common, what should my next step be.


## Solution:
First I researched on what a .sal file is to understand the basics of sal file and downloaded saleae logic 2, now then I opened the sal file in software and realised that D3 channel was the only channel that was having data or was responsive, all others were flat, this eliminated the possibilities of SPI and I2C to confirm that we should use Async Serial Analyser. 
![alt text](image-3.png)

I set the channel to D3 for the Async Serial analyser and ran it, it showed some form of data in hexadecimal, so I changed it to ASCII format and got a big string in the terminal, in the middle of the terminal was the flag present
![alt text](image-4.png)

## Flag:

```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Concepts learnt:

- What are .sal files and how to use saleae logic 2 to analyse the files.
- What are the different types of analysers.

## Notes:

- none

## Resources:

- [Saleae Logic 2 Tutorial](https://youtu.be/Ak9R4yxQPhs?si=L8o_v62FXL66I5Oh)
- [What is SPI, I2C, UART](https://www.totalphase.com/blog/2021/12/i2c-vs-spi-vs-uart-introduction-and-comparison-similarities-differences/?srsltid=AfmBOorDPu4PACMYCJ-qgBSezVzkEISjbAtdJWbqEexsgKWSsFEGIMw8)
