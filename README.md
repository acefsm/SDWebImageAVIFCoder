# SDWebImageAVIFCoder

[![CI Status](https://img.shields.io/travis/SDWebImage/SDWebImageAVIFCoder.svg?style=flat)](https://travis-ci.org/SDWebImage/SDWebImageAVIFCoder)
[![Version](https://img.shields.io/cocoapods/v/SDWebImageAVIFCoder.svg?style=flat)](https://cocoapods.org/pods/SDWebImageAVIFCoder)
[![License](https://img.shields.io/cocoapods/l/SDWebImageAVIFCoder.svg?style=flat)](https://cocoapods.org/pods/SDWebImageAVIFCoder)
[![Platform](https://img.shields.io/cocoapods/p/SDWebImageAVIFCoder.svg?style=flat)](https://cocoapods.org/pods/SDWebImageAVIFCoder)
[![SwiftPM compatible](https://img.shields.io/badge/SwiftPM-compatible-brightgreen.svg?style=flat)](https://swift.org/package-manager/)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/SDWebImage/SDWebImageAVIFCoder)

## What's for

This is a [SDWebImage](https://github.com/rs/SDWebImage) coder plugin to add [AV1 Image File Format (AVIF)](https://aomediacodec.github.io/av1-avif/) support. Which is built based on the open-sourced [libavif](https://github.com/AOMediaCodec/libavif) codec.

This AVIF coder plugin currently support AVIF still image **decoding**. Including alpha channel, as well as 10bit/12bit/16bit HDR images.

The AVIF encoding is also supported now. Which always encode as 8-bit depth images.

## Note

AVIF image spec is still in evolve. And the current upstream AVIF codec is a simple implementation. The encoding time may be long for large images.

Since we rely on the external codec libavif. We may periodically update the dependency and bump version. Make sure you're using the latest version as possible as you can :)

## AV1 Codec

libavif is a still image codec. But AVIF is based on the AV1 Video standard. So it need a AV1 codec for support. This relationship is just like HEIF(image) and HEVC(video) codec.

### aom

By default, libavif is built with [aom](https://aomedia.googlesource.com/aom/) codec support. aom is the first AV1 codec during the standard draft implementation.

### dav1d (Decoding)

[dav1d](https://github.com/videolan/dav1d) is the new and next generation AV1 codec, focused on speed and correctness.

See more about [explanation for why starting a new project but not improving aom](https://github.com/videolan/dav1d#why-do-you-not-improve-libaom-rather-than-starting-a-new-project)

From v0.3.0, libavif can built with dav1d. For CocoaPods user, you can simply use the subspec for this. Carthage for optional dav1d codec is not supported currently.

### libgav1 (Decoding)

[libgav1](https://chromium.googlesource.com/codecs/libgav1/) libgav1 is a Main profile (0) & High profile (1) compliant AV1 decoder. More information on the AV1 video format can be found at aomedia.org.

From v0.8.3, libavif can built with libgav1. For For CocoaPods user, you can simply use the subspec for this.

### rav1e (Encoding)

[rav1e](https://github.com/xiph/rav1e) is the fastest and safest AV1 encoder. Which use [Rust programming](https://www.rust-lang.org/) to provide fast and safe codec compared to aom. Its current form it is most suitable for cases where libaom (the reference encoder) is too slow.

See more about [performance](https://github.com/xiph/rav1e/issues/1248)

From v0.4.3, libavif can built with rav1e. For CocoaPods user, you can simply use the subspec for this. Carthage for optional rav1c codec is not supported currently.

Note rav1e currently only support iOS && macOS. watchOS and tvOS supports need Rust community upstream support.

Note that for CocoaPods user, rav1e is prebuilt binary (to avoid developer to install rust toolchain) and hosted on GitHub Git LFS. Make sure you have git-lfs installed.

```bash
brew install git-lfs
git lfs install
```

### SVT-AV1 (Encoding)

[SVT-AV1](https://gitlab.com/AOMediaCodec/SVT-AV1) is the Scalable Video Technology for AV1 (SVT-AV1 Encoder and Decoder) is an AV1-compliant encoder/decoder library core.

From v0.8.3, libavif can built with STV-AV1. For For CocoaPods user, you can simply use the subspec for this.

### SVT-AV1


## Requirements

+ iOS 9
+ macOS 10.11
+ tvOS 9.0
+ watchOS 2.0

## Installation

#### CocoaPods
SDWebImageAVIFCoder is available through [CocoaPods](https://cocoapods.org). To install
it with default aom AV1 codec, simply add the following line to your Podfile:

```ruby
pod 'SDWebImageAVIFCoder'
```

Note: From version 0.4.0, if you want to use rav1e or dav1e instead aom for faster AV1 codec, control the subspec of libavif instead:

```ruby
pod 'SDWebImageAVIFCoder'
pod 'libavif', :subpsecs => [
  'libdav1d',
  'librav1e'
]
```

or, for libgav1 && SVT-AV1, use:

```ruby
pod 'SDWebImageAVIFCoder'
pod 'libavif', :subpsecs => [
  'libgva1',
  'SVT-AV1'
]
```

Note: From version 0.2.0, the dependency libavif and libaom use the portable C implementation to works on Apple platforms. If you need the pre-built library with SIMD/AVX and assembly optimization, try the 0.1.0 version. 

#### Carthage

SDWebImageAVIFCoder is available through [Carthage](https://github.com/Carthage/Carthage).

```
github "SDWebImage/SDWebImageAVIFCoder"
```

Carthage does not support like CocoaPods' subspec, it only supports libaom for AVIF decoding && encoding.

#### Swift Package Manager (Xcode 11+)

SDWebImageAVIFCoder is available through [Swift Package Manager](https://swift.org/package-manager).

The framework through SwiftPM only supports libaom for AVIF decoding && encoding.

```swift
let package = Package(
    dependencies: [
        .package(url: "https://github.com/SDWebImage/SDWebImageAVIF.git", from: "0.5")
    ]
)
```

## Usage

To use AVIF coder, you should firstly add the `SDImageAVIFCoder.sharedCoder` to the coders manager. Then you can call the View Category method to start load AVIF images.

+ Objective-C

```objective-c
SDImageAVIFCoder *AVIFCoder = SDImageAVIFCoder.sharedCoder;
[[SDImageCodersManager sharedManager] addCoder:AVIFCoder];
UIImageView *imageView;
[imageView sd_setImageWithURL:url];
```

+ Swift

```swift
let AVIFCoder = SDImageAVIFCoder.shared
SDImageCodersManager.shared.addCoder(AVIFCoder)
let imageView: UIImageView
imageView.sd_setImage(with: url)
```

## Screenshot

<img src="https://raw.githubusercontent.com/SDWebImage/SDWebImageAVIFCoder/master/Example/Screenshot/AVIFDemo-iOS.png" width="300" />
<img src="https://raw.githubusercontent.com/SDWebImage/SDWebImageAVIFCoder/master/Example/Screenshot/AVIFDemo-macOS.png" width="600" />

The images are from [AV1 Still Image File Format Specification Test Files](https://github.com/AOMediaCodec/av1-avif/tree/master/testFiles).

## AVIF Image Viewer

AVIF is a new image format, which lack of related toolchains like Browser or Desktop Viewer support.

You can try [AVIFQuickLook](https://github.com/dreampiggy/AVIFQuickLook) QuickLook plugin on macOS to view it in Finder.

You can also try using [avif.js](https://kagami.github.io/avif.js/) to view it online by using Chrome's AV1 codec.

## Testing

SDWebImageAVIFCoder use [GitHub Actions](https://github.com/SDWebImage/SDWebImageAVIFCoder/actions) for testing. Each merge request will run the test cases to ensure the AVIF decoding/encoding function works.

Current test cases use macOS command line tools, to compare the AVIF sample decoding result with [ImageMagick](https://www.imagemagick.org/).

We have also the XCTest unit test target, you can run it by using Xcode's Test action.

## Author

DreamPiggy, lizhuoli1126@126.com

## Contributor

ledyba-z, ryo.hirafuji@link-u.co.jp

## License

SDWebImageAVIFCoder is available under the MIT license. See the LICENSE file for more info.

## Thanks

+ [libavif](https://github.com/AOMediaCodec/libavif)
+ [aom](https://aomedia.googlesource.com/aom/)
+ [dav1d](https://github.com/videolan/dav1d)
+ [AVIFQuickLook](https://github.com/dreampiggy/AVIFQuickLook)
+ [avif.js](https://github.com/Kagami/avif.js)


