# vision-camera-code-scanner

VisionCamera Frame Processor Plugin to read barcodes using MLKit Vision Barcode Scanning

## Installation

```sh
yarn add anderpaz/vision-camera-code-scanner-fix
```

Import the react-native-reanimated on the first line of your [`index`](https://github.com/anderpaz/vision-camera-code-scanner-fix/blob/1409a8afd02328a26e336036493b2d6ef8441359/example/index.tsx#L1) file.

```sh
import 'react-native-reanimated'
```

Add this to your `babel.config.js`
```
[
  'react-native-reanimated/plugin',
  {
    globals: ['__scanCodes'],
  },
]

```
To use it alongside [`ocr`](https://github.com/aarongrider/vision-camera-ocr), configure your babel.config.js like this.
```
[
  'react-native-reanimated/plugin',
  {
    globals: ['__scanOCR', '__scanCodes'],
  },
]

```
## Usage

Simply call the `useScanBarcodes()` hook or call `scanBarcodes()` inside of the `useFrameProcessor()` hook. In both cases you will need to pass an array of `BarcodeFormat` to specify the kind of barcode you want to detect.

> Note: The underlying MLKit barcode reader is only created once meaning that changes to the array will not be reflected in the app.

```js
import * as React from 'react';

import { StyleSheet, Text } from 'react-native';
import { useCameraDevices } from 'react-native-vision-camera';
import { Camera } from 'react-native-vision-camera';
import { useScanBarcodes, BarcodeFormat } from 'vision-camera-code-scanner';

export cosnt App = () => {
  const [hasPermission, setHasPermission] = React.useState(false);
  const devices = useCameraDevices();
  const device = devices.back;

  const [frameProcessor, barcodes] = useScanBarcodes([BarcodeFormat.QR_CODE], {
    checkInverted: true,
  });

  // Alternatively you can use the underlying function:
  // const frameProcessor = useFrameProcessor((frame) => {
  //   'worklet';
  //   const codes = scanBarcodes(frame, [BarcodeFormat.QR_CODE], { checkInverted: true });
  //   console.log(codes)
  //   // runOnJS(onBarcodes)(codes);

  //   //   Ocr reading
  //   const ocr = scanOCR(frame) as unknown as typ.oCRFrame;
  //   console.log(ocr)
  //   //  runOnJS(onOcr)(ocr);
  // }, []);

  React.useEffect(() => {
    (async () => {
      const status = await Camera.requestCameraPermission();
      setHasPermission(status === 'authorized');
    })();
  }, []);

  return (
    device != null &&
    hasPermission && (
      <>
        <Camera
          style={StyleSheet.absoluteFill}
          device={device}
          isActive={true}
          frameProcessor={frameProcessor}
          frameProcessorFps={5}
        />
        {barcodes.map((barcode, idx) => (
          <Text key={idx} style={styles.barcodeTextURL}>
            {barcode.displayValue}
          </Text>
        ))}
      </>
    )
  );
}

const styles = StyleSheet.create({
  barcodeTextURL: {
    fontSize: 20,
    color: 'white',
    fontWeight: 'bold',
  },
});
```

## License

MIT
