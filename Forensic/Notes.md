### What is `IHDR`?

In a PNG file, `IHDR` is the **first chunk** after the PNG signature. It describes **essential metadata** about the image, like size, color format, compression, etc.


the IHDR contains

| Field             | Size (bytes) | Description                                       |
| ----------------- | ------------ | ------------------------------------------------- |
| **Width**         | 4            | Image width in pixels (must be > 0 and ≤ 2³¹–1)   |
| **Height**        | 4            | Image height in pixels (same constraints)         |
| **Bit Depth**     | 1            | Bits per sample (e.g., 8 for 256-color grayscale) |
| **Color Type**    | 1            | Describes the format (grayscale, RGB, etc.)       |
| **Compression**   | 1            | Always `0` (PNG uses DEFLATE compression)         |
| **Filter Method** | 1            | Always `0` (adaptive filtering)                   |
| **Interlace**     | 1            | 0 = no interlace, 1 = Adam7 interlace             |

A 2×2 pixel PNG might start like this (after the PNG signature):

00 00 00 0D   // Chunk length: 13 bytes
49 48 44 52   // 'IHDR' in ASCII
00 00 00 02   // Width: 2 pixels
00 00 00 02   // Height: 2 pixels
08            // Bit depth: 8
02            // Color type: Truecolor (RGB)
00            // Compression method: 0
00            // Filter method: 0
00            // Interlace method: 0


- If it's missing or malformed, the PNG **won't render**.
- Width/height are taken from here — and the **(2³¹ - 1)** limit applies here.

## What is the `PLTE` Chunk?

The `PLTE` (palette) chunk is a **color lookup table** — a list of colors (RGB values), each stored in **3 bytes**:

- 1 byte for Red
- 1 byte for Green
- 1 byte for Blue

It defines the **palette of available colors** for the image — especially used in **indexed color images** (color type 3).


Each **palette entry** = 3 bytes:

- Byte 1: Red (0–255)
- Byte 2: Green (0–255)
- Byte 3: Blue (0–255)

So:
- A chunk with length `3` has 1 color
- A chunk with length `6` has 2 colors
- A chunk with length `768` has 256 colors


 **The chunk length must be divisible by 3**. Otherwise, the PNG is **invalid**.

|Color Type|Meaning|PLTE Chunk|
|---|---|---|
|0|Grayscale|❌ Forbidden|
|2|Truecolor (RGB)|✅ Optional|
|3|Indexed-color|✅ **Required**|
|4|Grayscale + Alpha|❌ Forbidden|
|6|Truecolor + Alpha (RGBA)|✅ Optional|

- **Must appear before first `IDAT` chunk**  
because decoding of image data might rely on knowing the palette.
   **Unused or duplicate palette entries are allowed**
- Not all entries must be referenced by image data.

#### IEND Image trailer

The IEND chunk must appear LAST. It marks the end of the PNG datastream. The chunk's data field is empty.

#### IDAT Image data

The IDAT chunk contains the actual image data. To create this data:

1. Begin with image scanlines represented as described in [Image layout](https://www.w3.org/TR/PNG-DataRep.html#DR.Image-layout); the layout and total size of this raw data are determined by the fields of IHDR.
2. Filter the image data according to the filtering method specified by the IHDR chunk. (Note that with filter method 0, the only one currently defined, this implies prepending a filter type byte to each scanline.)
3. Compress the filtered data using the compression method specified by the IHDR chunk.

The IDAT chunk contains the output datastream of the compression algorithm.

To read the image data, reverse this process.

There can be multiple IDAT chunks; if so, they must appear consecutively with no other intervening chunks. The compressed datastream is then the concatenation of the contents of all the IDAT chunks. The encoder can divide the compressed datastream into IDAT chunks however it wishes. (Multiple IDAT chunks are allowed so that encoders can work in a fixed amount of memory; typically the chunk size will correspond to the encoder's buffer size.) It is important to emphasize that IDAT chunk boundaries have no semantic significance and can occur at any point in the compressed datastream. A PNG file in which each IDAT chunk contains only one data byte is legal, though remarkably wasteful of space. (For that matter, zero-length IDAT chunks are legal, though even more wasteful.)

See [Filter Algorithms](https://www.w3.org/TR/PNG-Filters.html) and [Deflate/Inflate Compression](https://www.w3.org/TR/PNG-Compression.html) for details.

#### bKGD Background color

The bKGD chunk specifies a default background color to present the image against. Note that viewers are not bound to honor this chunk; a viewer can choose to use a different background.

For color type 3 (indexed color), the bKGD chunk contains:

   Palette index:  1 byte

The value is the palette index of the color to be used as background.

For color types 0 and 4 (grayscale, with or without alpha), bKGD contains:

   Gray:  2 bytes, range 0 .. (2^bitdepth)-1

(For consistency, 2 bytes are used regardless of the image bit depth.) The value is the gray level to be used as background.

For color types 2 and 6 (truecolor, with or without alpha), bKGD contains:

   Red:   2 bytes, range 0 .. (2^bitdepth)-1
   Green: 2 bytes, range 0 .. (2^bitdepth)-1
   Blue:  2 bytes, range 0 .. (2^bitdepth)-1

(For consistency, 2 bytes per sample are used regardless of the image bit depth.) This is the RGB color to be used as background.

When present, the bKGD chunk must precede the first IDAT chunk, and must follow the PLTE chunk, if any.

See Recommendations for Decoders: [Background color](https://www.w3.org/TR/PNG-Decoders.html#D.Background-color).


## `cHRM`: Primary Chromaticities and White Point

 What is it?

`cHRM` specifies the **chromaticities (color characteristics)** of the **red, green, blue primaries**, and the **white point** of the image.

### Why does it matter?

It helps color management systems accurately map the image colors to the screen or printer colors.

### 🧩 Structure:

Contains **8 values**, each a 4-byte unsigned integer representing the x and y coordinates (multiplied by 100,000):

|Field|Meaning|
|---|---|
|WhitePointX|x coordinate of white point|
|WhitePointY|y coordinate of white point|
|RedX|x coordinate of red primary|
|RedY|y coordinate of red primary|
|GreenX|x coordinate of green primary|
|GreenY|y coordinate of green primary|
|BlueX|x coordinate of blue primary|
|BlueY|y coordinate of blue primary|

## Image Gamma

###  What is it?

Specifies the **gamma** of the image, which affects how the image is displayed on different monitors.

###  What is gamma?

Gamma controls the **brightness/contrast curve** of an image. A gamma of 1.0 means **linear light**, while 2.2 is common for displays (nonlinear).

###  Structure:

A single 4-byte unsigned integer:

`gamma value × 100000`

Example:
- `45455` → gamma = `0.45455` (common for PNGs meant for Windows)
- `100000` → gamma = `1.0` (linear light)
 Helps correct for system differences in display gamma.

## Physical Pixel Dimensions

### What is it?

Specifies the **intended pixel size** in real-world units (like DPI or pixels/meter).

###  Structure (9 bytes total):

| Field          | Size | Meaning                        |
| -------------- | ---- | ------------------------------ |
| PixelsPerUnitX | 4    | Pixels per unit in X direction |
| PixelsPerUnitY | 4    | Pixels per unit in Y direction |
| UnitSpecifier  | 1    | 0 = unit is unknown, 1 = meter |

- If PixelsPerUnitX = 3780 and unit = meter → 96 DPI (`3780/100 = 96`)
- Helps **print layout software** understand real-world image size.


## What is Data Carving?

In **digital forensics**, **data carving** (also called **file carving** or **data extraction**) is the process of **recovering files or data fragments** from **unallocated space, memory dumps, or raw disk images** **without relying on file system metadata**.

- When files are deleted, their references (like directory entries) are often removed, but the actual data may still exist on the disk.
- Data carving involves **scanning these raw data areas** and **reconstructing files based on known file signatures**, even when there's **no file name or folder structure** available.

It typically relies on:

- **File headers and footers (magic numbers)**: Known byte patterns that mark the start and end of files (e.g., JPEG starts with `FFD8` and ends with `FFD9`).