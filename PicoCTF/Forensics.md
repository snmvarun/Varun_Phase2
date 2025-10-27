# Trivial Flag Transfer Protocol
Figure out how they moved the flag.

## Solution:
![alt text](image.png)
First I download the given file to check the type to be a wireshark capture file, I open it in wireshark and then upon analysing, there was a lot of data having the tftp protocol, so I applied a filter to only display tftp protocol data and then exported the objects from tftp

![alt text](image-1.png)
These are the files that were exported from wireshark. Opening the instructions and plan text file, I applied trial and error to check what type of way the information is changed in to figure out that it is ciphered in ROT13, upon decoding, instructions gave: "TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFERFIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN"; for plan it gave: "IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS". Then I downloaded steghide onto linux to inspect hidden data in the pictures by extracting.

```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/tftp_$ steghide extract -sf picture1.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/tftp_$ steghide extract -sf picture2.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/tftp_$ steghide extract -sf picture3.bmp
Enter passphrase:
wrote extracted data to "flag.txt".
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF/tftp_$ cat flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```
When I used steghide extract with -sf for the sourcefile and ran the pictures, it asked for a passphrase so for that I went through the stuff I already had to see that the decoded phrase of "plan" had a bit of focus on "DUEDILIGENCE" so I tried that as password and it worked then I catted the flag to obtain it.

## Flag:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts learnt:
- How to use wireshark and how to filter it and its functions.
- How to use the steghide command to extract hidden details of files.
- How to decode ciphers.
 
## Notes:
- None

## Resources:

- [Wireshark Tutorial](https://youtu.be/lb1Dw0elw0Q?si=dQ7QmG2uPkdGDwmZ)
- [Steghide Usage](https://www.kali.org/tools/steghide/)
 

# 2. tunn3l v1s10n

We found this file. Recover the flag.

## Solution:
![alt text](image-2.png)
First opening the file, I realised it was corrupt and a type of .bmp file so I opened it on HxD and analysed it but got nothing much, so I opened multiple functioning .bmp files in the HxD editor and I noticed a common factor which work but doesnt lie in the file. 

![alt text](image-3.png) 
So I corrected this in HxD and saved it. I also changed the extention and renamed the file to end it with .bmp and then opened it to find the next picture 

![alt text](tunn3l_v1s10n-1.bmp)
To check more hidden details I used exiftool and checked its properties. 

```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF$ exiftool tunn3l_v1s10n.bmp
ExifTool Version Number         : 12.76
File Name                       : tunn3l_v1s10n.bmp
Directory                       : .
File Size                       : 2.9 MB
File Modification Date/Time     : 2025:10:26 15:55:04+00:00
File Access Date/Time           : 2025:10:26 16:02:38+00:00
File Inode Change Date/Time     : 2025:10:26 15:55:18+00:00
File Permissions                : -rwxrwxrwx
File Type                       : BMP
File Type Extension             : bmp
MIME Type                       : image/bmp
BMP Version                     : Windows V3
Image Width                     : 1134
Image Height                    : 306
Planes                          : 1
Bit Depth                       : 24
Compression                     : None
Image Length                    : 2893400
Pixels Per Meter X              : 5669
Pixels Per Meter Y              : 5669
Num Colors                      : Use BitDepth
Num Important Colors            : All
Image Size                      : 1134x306
Megapixels                      : 0.347
```
analysing, the height of the file felt unusual and less, and as the challenge name hints saying "tunnel vision" I thought of thinking of a way to increase the height pixels, so I opened the file on HxD again to change its resolution by editing a few hex values targetting the image height, to about 800 pixels and 800 in hex is 0x320

![alt text](tunn3l_v1s10n-2.bmp)
and as I change the appropriate hex value in HxD and increase the resolution, I get the flag 

## Flag:

```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts learnt:

- How to use the HxD editor
- How to spot file types
- How to fix a corrupt file
- How to use exiftool to look through metadata

## Notes:

- None

## Resources:

- [HxD Tutorial](https://youtu.be/3xFQM6d7vnA?si=XlclRrhmEbIndma6)
- [Exiftool](https://youtu.be/rMdLcYXG1u4?si=Ifb24mxbPW7KC4Ac)

# 3. m00nwalk 
Decode this message from the moon.

## Solution:
![alt text](image-4.png)
First upon researching, the moon landing pictures were sent back by SSTV system, keeping that in mind I thought of opening the file in a spectrogram in Audacity to find pattern at the beginning of the spectrogram, almost like a digital waveform of binary, which similarly is a hint towards SSTV broadcasting system.
![alt text](image-5.png)
So I used an online SSTV decoder and applied it to the message.wav file to find this image with the flag written on it.

## Flag:

```
picoCTF{beep_boop_im_in_space}
```

## Concepts learnt:

- How to analyse a spectrogram and a wav file.
- How to decode SSTV file 

## Notes:
- None

## Resources:
- [Wikipage on how the pictures were sent back to earth by SSTV](https://en.wikipedia.org/wiki/Apollo_11_missing_tapes#:~:text=Apollo%2011%20was%20the%20spaceflight,lines%20of%20resolution%2C%20progressively%20scanned)
- [SSTV Decoder](https://sstv-decoder.mathieurenaud.fr/)

