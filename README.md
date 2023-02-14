# drm-quick-start-react-native

This sample application demonstrates how to use Axinom DRM for playback using React Native Video Player. This project uses react-native-video npm package.

## Prerequisities

In order to use this sample the following prerequisities should be met:

1. Set up Axinom DRM demo account. Registration can be performed here: https://www.axinom.com/products/drm.

2. Ability to generate Axinom DRM License Tokens. Documentation on this can be found here: https://github.com/Axinom/drm-quick-start.

## Setup

### Using the code samples in the repo

This repo consists of a working react native video player. You can clone the sample code and build the code using Xcode or Android Studio.
Then you can transfer the build file to your device and try out the playback.

### From the scratch

You can install react-native-video npm package using `npm i react-native-video`. For further information please refer to  https://www.npmjs.com/package/react-native-video.

To intgrate DRM, you need to specify the DRM details in the video element in the App.js file.

#### Widevine & Playready
```
drm= {{
    type: '<DRM_Type>',
    licenseServer:'<License_server_url>',
    headers: {
        'X-AxDRM-Message':'<token>',
        'Content-Type': 'application/octet-stream'
    }
}}
```
#### Fairplay
```
drm= {{
    type: 'fairplay',
    licenseServer:'https://drm-fairplay-licensing.axprod.net/AcquireLicense',
    certificateUrl: 'https://vtb.axinom.com/FPScert/fairplay.cer',
    headers: {
        'X-AxDRM-Message':'<token>',
        'Content-Type': 'application/octet-stream'
    }
}}
```

Note:
RCTVideo.m class had to be slightly modified for Axinom DRM to work. It is part of the
react-native-video module responsible for video playback on iOS. The change in it was necessary
because originally the received spcData was handled differently according to whether "getLicense"
drm property was used or not. The change was made to leave the received spcData unmodified even
when "getLicense" property was not used (example showing the required change along with commented
out old implementation):
```
  if(self.onGetLicense) {
    [request setHTTPBody: spcData];
  } else {
    // Original implementation
    /* NSString *spcEncoded = [spcData base64EncodedStringWithOptions:0];
    NSString *spcUrlEncoded = (NSString *) CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes(kCFAllocatorDefault, (CFStringRef)spcEncoded, NULL, CFSTR("?=&+"), kCFStringEncodingUTF8));
    NSString *post = [NSString stringWithFormat:@"spc=%@&%@", spcUrlEncoded, contentId];
    NSData *postData = [post dataUsingEncoding:NSUTF8StringEncoding allowLossyConversion:YES];
    [request setHTTPBody: postData]; */
    // Required change for Axinom DRM
    [request setHTTPBody: spcData];
  }
```

The obtained license had to be also left unmodified:
```
if(self.onGetLicense) {
  [dataRequest respondWithData:data];
} else {
  // Original implementation
  /* NSData *decodedData = [[NSData alloc] initWithBase64EncodedData:data options:0];
  [dataRequest respondWithData:decodedData]; */
  // Required change for Axinom DRM
  [dataRequest respondWithData:data];
}
```

The reason why just using the "getLicense" drm property would not work is that it would add more
complexity to the drm parameters in App.js file and a change in the RCTVideo.m class would still
be required because otherwise contentId would not be correct:
```
NSString *contentId;
NSString *contentIdOverride = (NSString *)[self->_drm objectForKey:@"contentId"];
if (contentIdOverride != nil) {
  contentId = contentIdOverride;
} else if (self.onGetLicense) {
  contentId = url.host;
} else {
  contentId = [url.absoluteString stringByReplacingOccurrencesOfString:@"skd://" withString:@""];
}
```

If we take "skd://9f39d572-1a68-4c8a-ae1b-7123b285ba2f:90E95794A62BD1FA12BE63B6D9B8AD43" as a
"url" example then "url.host" would give us "9f39d572-1a68-4c8a-ae1b-7123b285ba2f" but we would
need "9f39d572-1a68-4c8a-ae1b-7123b285ba2f:90E95794A62BD1FA12BE63B6D9B8AD43" which is given to us
by the second option by removing just the "skd://" beginning and keeping everything else.

##  Using Simulator

Important: Protected streams cannot be played back on Simulator.
