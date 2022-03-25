QR Code generator library
=========================
Work with Platform.io
------------
this project is forked from [here](https://github.com/nayuki/QR-Code-generator), a minimal qrcode generator for almost language, include c.
I just add `library.json`, which makes it work for platform.io.  
You can add this library to you embedded project by edit `platformio.ini`
```ini
lib_deps =
    liux-pro/qrcode generator@^1.7.0
```
A simple example for esp8266
```c
#include <Arduino.h>
#include "qrcodegen.h"
/*---- Utilities ----*/

// Prints the given QR Code to the console.
static void printQr(const uint8_t qrcode[]) {
    int size = qrcodegen_getSize(qrcode);
    int border = 4;
    for (int y = -border; y < size + border; y++) {
        for (int x = -border; x < size + border; x++) {
            Serial.print((qrcodegen_getModule(qrcode, x, y) ? "@@" : "  "));
        }
        Serial.print("\n");
    }
    Serial.print("\n");
}
/*---- Demo suite ----*/

// Creates a single QR Code, then prints it to the console.
static void doBasicDemo() {
    const char *text = "Hello, world!";                // User-supplied text
    enum qrcodegen_Ecc errCorLvl = qrcodegen_Ecc_LOW;  // Error correction level

    // Make and print the QR Code symbol
    uint8_t qrcode[qrcodegen_BUFFER_LEN_MAX];
    uint8_t tempBuffer[qrcodegen_BUFFER_LEN_MAX];
    bool ok = qrcodegen_encodeText(text, tempBuffer, qrcode, errCorLvl,
                                   qrcodegen_VERSION_MIN, qrcodegen_VERSION_MAX, qrcodegen_Mask_AUTO, true);
    if (ok)
        printQr(qrcode);
}

void setup() {
    Serial.begin(115200);
    doBasicDemo();
// write your initialization code here
}

void loop() {
// write your code here
}
```

Introduction
------------

This project aims to be the best, clearest QR Code generator library in multiple languages. The primary goals are flexible options and absolute correctness. Secondary goals are compact implementation size and good documentation comments.

Home page with live JavaScript demo, extensive descriptions, and competitor comparisons: [https://www.nayuki.io/page/qr-code-generator-library](https://www.nayuki.io/page/qr-code-generator-library)


Features
--------

Core features:

* Available in 6 programming languages, all with nearly equal functionality: Java, TypeScript/JavaScript, Python, Rust, C++, C
* Significantly shorter code but more documentation comments compared to competing libraries
* Supports encoding all 40 versions (sizes) and all 4 error correction levels, as per the QR Code Model 2 standard
* Output format: Raw modules/pixels of the QR symbol
* Detects finder-like penalty patterns more accurately than other implementations
* Encodes numeric and special-alphanumeric text in less space than general text
* Open-source code under the permissive MIT License

Manual parameters:

* User can specify minimum and maximum version numbers allowed, then library will automatically choose smallest version in the range that fits the data
* User can specify mask pattern manually, otherwise library will automatically evaluate all 8 masks and select the optimal one
* User can specify absolute error correction level, or allow the library to boost it if it doesn't increase the version number
* User can create a list of data segments manually and add ECI segments

Optional advanced features (Java only):

* Encodes Japanese Unicode text in kanji mode to save a lot of space compared to UTF-8 bytes
* Computes optimal segment mode switching for text with mixed numeric/alphanumeric/general/kanji parts

More information about QR Code technology and this library's design can be found on the project home page.


Examples
--------

The code below is in Java, but the other language ports are designed with essentially the same API naming and behavior.

```java
import java.awt.image.BufferedImage;
import java.io.File;
import java.util.List;
import javax.imageio.ImageIO;
import io.nayuki.qrcodegen.*;

// Simple operation
QrCode qr0 = QrCode.encodeText("Hello, world!", QrCode.Ecc.MEDIUM);
BufferedImage img = toImage(qr0, 4, 10);  // See QrCodeGeneratorDemo
ImageIO.write(img, "png", new File("qr-code.png"));

// Manual operation
List<QrSegment> segs = QrSegment.makeSegments("3141592653589793238462643383");
QrCode qr1 = QrCode.encodeSegments(segs, QrCode.Ecc.HIGH, 5, 5, 2, false);
for (int y = 0; y < qr1.size; y++) {
    for (int x = 0; x < qr1.size; x++) {
        (... paint qr1.getModule(x, y) ...)
    }
}
```


License
-------

Copyright © 2022 Project Nayuki. (MIT License)  
[https://www.nayuki.io/page/qr-code-generator-library](https://www.nayuki.io/page/qr-code-generator-library)

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

* The above copyright notice and this permission notice shall be included in
  all copies or substantial portions of the Software.

* The Software is provided "as is", without warranty of any kind, express or
  implied, including but not limited to the warranties of merchantability,
  fitness for a particular purpose and noninfringement. In no event shall the
  authors or copyright holders be liable for any claim, damages or other
  liability, whether in an action of contract, tort or otherwise, arising from,
  out of or in connection with the Software or the use or other dealings in the
  Software.
