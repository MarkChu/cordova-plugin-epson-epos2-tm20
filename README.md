epsonepos2tmp20
====================
Cordova plugin for Epson ePOS SDK(v2.6.0) for iOS and Android.

Integrates the Epson ePOS2 SDK for iOS and Android with a
limited set of functions to discover and connect ePOS printers

Check supported devices and requirements from official SDK by Epson.
* [iOS](https://download.epson-biz.com/modules/pos/index.php?page=single_soft&cid=5670&scat=58&pcat=52)
* [Android](https://download.epson-biz.com/modules/pos/index.php?page=single_soft&cid=5669&scat=61&pcat=52)

Install
-------

```
cordova plugin add epsonepos2tmp20
```

API
---

The plugin exposes an interface object to `cordova.epos2` for direct interaction
with the SDK functions. See `www/plugin.js` for details about the available
functions and their arguments. All API functions are asynchronous and return a
[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises).

### Printer Discovery

#### .startDiscover(discoverCallback)
This will search for supported printers connected to your mobiel device
via Bluetooth or available in local area network (LAN)

```
cordova.epos2.startDiscover(function(deviceInfo) {
    // success callback with deviceInfo
}).catch(function(error) => {
    // error callback
});
```

#### .stopDiscover()
```
cordova.epos2.stopDiscover()
  .then(function() {
    // success callback
  })
  .catch(function(error) {
    // error callback
  });
```

#### .getSupportedModels()
Resolves with an array of strings denoting the supported printer models.
```
cordova.epos2.getSupportedModels()
  .then(function(models) {
    // success callback
  })
  .catch(function(error) {
    // error callback
  });
```

### Printer Connection

#### .connectPrinter(device, printerModel)
Establish a connection to the given printer device.
For `device` either provide a device information objects as retrieved from discovery
or string with device address ('BT:xx:xx:xx:xx:xx' or 'TCP:xx.xx.xx.xx').

```
cordova.epos2.connectPrinter(device, printerModel)
  .then(function() {
    // success callback
  })
  .catch(function(error) {
    // error callback
  });
```

#### .disconnectPrinter()
```
cordova.epos2.disconnectPrinter()
  .then(function() {
    // success callback
  })
  .catch(function(error) {
    // error callback
  });
```

#### .getPrinterStatus()
```
cordova.epos2.getPrinterStatus()
  .then(function(status) {
    // success callback with status object
  })
  .catch(function(error) {
    // error callback
  });
```

### Printing

#### .print(stringData, successCallback, errorCallback)
One-shot function printing the given text. Use '\n' in string data in order to move to next line.
Cut feed is added automatically.

```
cordova.epos2.print(stringData, function() => {
    // success callback
}, function(error) => {
    // error callback
});
```

#### .printText(stringData, textFont, textSize, textAlign, terminate)
Send text to the connected printer. Also accepts parameters for font type, text size and alignment.
Can be called multiple times for additional text lines. Set `terminate` to True in order to complete
the print job and add cut feed.

```
cordova.epos2.printText(stringData, 0, 1, 2, false)
  .then(function() {}
    // success callback
  })
  .catch(function(error) => {
    // error callback
  });
```

#### .printBarCode(data, type, hriPosition,hriFont,Bwidth,Bheight,terminate)
Print different types of barcodes. **Please refer to the Epson SDK documentation for correctly encoding the data**.

Types:

* EPOS2_BARCODE_UPC_A
* EPOS2_BARCODE_UPC_E
* EPOS2_BARCODE_EAN133
* EPOS2_BARCODE_JAN133
* EPOS2_BARCODE_EAN8
* EPOS2_BARCODE_JAN8,
* EPOS2_BARCODE_CODE39
* EPOS2_BARCODE_ITF
* EPOS2_BARCODE_CODABAR
* EPOS2_BARCODE_CODE93
* EPOS2_BARCODE_CODE128
* EPOS2_BARCODE_GS1_128
* EPOS2_BARCODE_GS1_DATABAR_OMNIDIRECTIONAL
* EPOS2_BARCODE_GS1_DATABAR_TRUNCATED
* EPOS2_BARCODE_GS1_DATABAR_LIMITED
* EPOS2_BARCODE_GS1_DATABAR_EXPANDED


Function parameters:
* @param {String} Barcode Data String, Encode Data or Specify start character 
* @param {String} Type Type of barcode
* @param {Number} Specifies the HRI position.(0 - 3) 0:No print 1:above 2:below 3:both
* @param {Number} Specifies the HRI font. (0 - 4) 
* @param {Number} Specifies the width of a single module in dots. (2-6)
* @param {Number} Specifies the height of the barcode in dots. (1 - 255)
* @param {Boolean} [terminate=false] Send additional line feeds an a "cut" command to complete the print
* @return {Promise} resolving on success, rejecting on error

Example

```
cordova.epos2.printBarCode("123456789011", "EPOS2_BARCODE_EAN13", 0,0,2,70, true)
.then(res => console.debug(res))
.catch(e => console.error(e))
```

#### .printBarCode128(data, type, hriPosition,hriFont,Bwidth,Bheight,terminate)

Helper function to print barcode 128 CODEB

```
cordova.epos2.printBarCode128("11111", 0,0,2,70, true).then(res => console.debug(res)).catch(e => console.error(e))
```

#### .printImage(data, printMode, halfTone, terminate)
Send image data as data-url to the connected printer.

```
cordova.epos2.printImage(imageSource, 0, 0, false)
  .then(function() {}
    // success callback
  })
  .catch(function(error) => {
    // error callback
  });
```

Example
-------

After successful discovery or entering the printer address, one can send a print job like this:

```js
cordova.epos2.connectPrinter(device, model)
  .then(function() {
    return cordova.epos2.printText([
      'This is a printing demo from Cordova\n',
      'Composed using the cordova-plugin-epos2 plugin\n',
      '\n'
    ]);
  })
  .then(function() {
    // send image with `terminate` flag to complete the print job
    return cordova.epos2.printImage('data:image/png;base64,xxxxx', 0, 0, true);
  })
  .then(function() {
    return cordova.epos2.disconnectPrinter()
      .catch(function() { /* ignore disconnect errors */ });
  })
  .then(function() {
    console.log('Printing complete.');
  })
  .catch(function(err) {
    console.error('Printing failed', err);
  })
```

Platforms
---------

* iOS 9+
* Android

License
-------

[MIT License](http://ilee.mit-license.org)
