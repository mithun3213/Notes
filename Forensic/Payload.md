exiftool
xxd -b ./fileanme --> hexdump of the raw binary content
hexeditor to inspect the bytes of the file
binwalk firmware.bin if the files are there
we use the binwalk -e firmware.bin
wireshark
(Files carving tools
- **Foremost**
- **Scalpel**
- **PhotoRec**
- **Autopsy (via Sleuth Kit)**
 )
bgrep "89 50 4E 47 0D 0A 1A 0A" cat.png  ---> to find the deleted images or png in the files

(
`bulk_extractor` is a **digital forensics tool** used to extract **useful artifacts** (like emails, URLs, credit card numbers, browser history, etc.) from:

- Disk images (`.dd`, `.E01`, etc.)
- Memory dumps
- Raw data files
📌 **Key Feature:**  
It **ignores the file system** entirely and scans the raw bytes of the input data to extract strings and patterns. That means it can recover **hidden**, **deleted**, or **fragmented** data missed by file-system-based tools.

)
(
### What is `csvcut`?
`csvcut` is a command-line tool (part of the `csvkit`) used to **extract specific columns** from a CSV (Comma-Separated Values) file—**like `cut` but CSV-aware**

)

bitlocker2john -i bitlocker-1.dd > bitlocker.txt --->> to extract the image from the .dd image
--> to decrypt the .dd image we use the dislocker --->sudo dislocker  -V bitlocker-1.dd -upassword -- /mnt/1

in the ls -la /mnt/1 visit there will  be the dockerfile 
sudo mount -o loop,ro /mnt/1/dockerfile -- /mnt/2 

ls -la /mnt/2 we have the flag.txt ...
### What is `zsteg`?

**`zsteg`** is a **steganography tool** specifically designed to detect and extract **hidden data from PNG and BMP images**, especially when that data is hidden using **Least Significant Bit (LSB) techniques**.

---> zsteg 

### What is `fsstat`?

**`fsstat`** is a **forensic tool** from **The Sleuth Kit (TSK)** suite that displays detailed information about a **file system** inside a disk image (like `disk.img`).


---->fsstat

### What is `fls`?

**`fls`** is a command-line tool from **The Sleuth Kit (TSK)** that **lists files and directories** from a disk image or

fls -o 206848 disk.img

### What is `icat`?

**`icat`** is a Sleuth Kit tool used to **extract the content of a file** from a disk image

icat -o 206848 disk.img 2345


