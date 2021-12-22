# drm-quick-start-react-native

This sample application demonstrates how to use Axinom DRM for playback using React Native Video Player. This project uses react-native-video npm package.

## Prerequisities

In order to use this sample the following prerequisities should be met:

1. Set up Axinom DRM demo account. Registration can be performed here: https://www.axinom.com/products/drm.

2. Ability to generate Axinom DRM License Tokens. Documentation on this can be found here: https://github.com/Axinom/drm-quick-start.

## Setup

You can install react-native-video npm package using `npm i react-native-video`. For further information please refer to  https://www.npmjs.com/package/react-native-video.

To intgrate DRM, you need to specify the DRM details in the video element in the App.js file.

### Widevine & Playready
```
drm= {{
    type: '<DRM_Type>',
    licenseServer:'<License_server_url>',
    headers: {
        'X-AxDRM-Message':'<token>',
        'Transfer-Encoding': 'Chunked',
        'Content-Type': 'application/octet-stream'
    }
}}
```
### Fairplay
```
drm= {{
    type: 'fairplay',
    licenseServer:'https://drm-fairplay-licensing.axtest.net/AcquireLicense',
    certificateUrl: 'https://vtb.axinom.com/FPScert/fairplay.cer',
    contentId:'f8c80c25-690f-4736-8132-430e5c6994ce:51BB4F1A7E2E835B2993884BD09ADB19',
    headers: {
        'X-AxDRM-Message':'<token>',
        'Transfer-Encoding': 'Chunked',
        'Content-Type': 'application/octet-stream'
    }
}}
```
##  Using Simulator

Important: Protected streams cannot be played back on Simulator.
