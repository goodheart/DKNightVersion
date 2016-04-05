![](./images/Banner.png)

<p align="center">
<a href="https://img.shields.io/badge/Language-%20Objective--C%20-orange.svg"><img src="https://img.shields.io/badge/Language-%20Objective--C%20-orange.svg"></a>
<a href="http://cocoadocs.org/docsets/DKNightVersion"><img src="http://img.shields.io/cocoapods/v/DKNightVersion.svg?style=flat"></a>
<a href="https://travis-ci.org/Draveness/DKNightVersion"><img src="https://travis-ci.org/Draveness/DKNightVersion.png"></a>
<img src="https://img.shields.io/badge/license-MIT-blue.svg">
<a href="https://img.shields.io/badge/platform-%20iOS%20-lightgrey.svg"><img src="https://img.shields.io/badge/platform-%20iOS%20-lightgrey.svg"></a>
</p>

- [x] Easily integrate and high performace
- [x] Providing UIKit and CoreAnimation categoty
- [x] Read color customization from file
- [x] Support different themes

# Demo

![](./images/DKNightVersion.gif)

----

+ [Installation with CocoaPods](#Installation-with-CocoaPods)
	+ [Podfile](#Podfile)
	+ [Import](#Import)
+ [Usage](#Usage)
+ [Advanced Usage](#Advanced-Usage)
	+ [DKNightVersionManger](#DKNightVersionManger)
		+ [Change Theme](#Change-Theme)
		+ [Post Notification](#Post-Notification)
	+ [DKColorPicker](#DKColorPicker)
	+ [DKColorTable](#DKColorTable)
	+ [Create temporary DKColorPicker](#Create-temporary-DKColorPicker)
	+ [DKImagePicker](#DKImagePicker)


# Get Started

## Installation with CocoaPods

[CocoaPods](https://cocoapods.org/) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like DKNightVersion in your projects. See the [Get Started section](https://cocoapods.org/#get_started) for more details.

### Podfile

Run following script in termianl

```shell
$ pod update
```

Add this line to your `podfile`

```shell
pod "DKNightVersion", "~> 2.1.0"
```

### Import

Import DKNightVersion header file

```objectivec
#import <DKNightVersion/DKNightVersion.h>
```

## Usage

Checkout `DKColorTable.txt` file in your project, which locates in `Pods/DKNightVersion/Resources/DKNightVersion.txt`

```
NORMAL   NIGHT
#ffffff  #343434 BG
#aaaaaa  #313131 SEP
```

And then, set color picker like this

```objectivec
self.view.dk_backgroundColorPicker = DKColorPickerWithKey(BG);
```

After the current theme version change to `DKThemeVersionNight`, the view background color will switch to `#343434`.

```objectivec
[DKNightVersionManager nightFalling];
``` 

or

```objectivec
DKNightVersionManager *manager = [DKNightVersionManager sharedInstance];
manager.themeVersion = DKThemeVersionNormal;
```

## Advanced Usage

There are two approaches you can use to integrate night mode to your iOS App.

### DKNightVersionManager

The latest version for DKNightVersion add a readonly `dk_manager` property for `NSObject` returns the `DKNightVersionManager` singleton.

#### Change Theme

You can call `nightFalling` or `dawnComing` to switch current theme version to `DKThemeVersionNight` or `DKThemeVersionNormal`.

```objectivec
[self.dk_manager dawnComing];
[self.dk_manager nightFalling];
```

Modify `themeVersion` property to directly switch theme version.

```objectivec
self.dk_manager.themeVersion = DKThemeVersionNormal;
self.dk_manager.themeVersion = DKThemeVersionNight;
// if there is a RED column in DKColorTable.txt (default) or in 
// other `file` if you customize `file` property for `DKColorTable`
self.dk_manager.themeVersion = @"RED"; 
```

#### Post Notification

Every time the current theme version changes, `DKNightVersionManager` will posts a `DKNightVersionThemeChangingNotificaiton`. If you wanna to do some customization, you can observe this notification and react with proper actions.

### DKColorPicker

`DKColorPicker` is the core of DKNightVersion. And this lib adds dk_colorPicker to every UIKit and Core Animation components. Ex:

```objectivec
@property (nonatomic, copy, setter = dk_setBackgroundColorPicker:) DKColorPicker dk_backgroundColorPicker;
@property (nonatomic, copy, setter = dk_setTintColorPicker:) DKColorPicker dk_tintColorPicker;
```

DKColorPicker is defined in `DKColor.h` file, receives a `DKThemeVersion` as parameter and return a `UIColor`.

```objectivec
typedef UIColor *(^DKColorPicker)(DKThemeVersion *themeVersion);
```

+ Use `DKColorPickerWithKey(key)` to obtain `DKColorPicker` from `DKColorTable`

	```objectivec
	view.dk_backgroundColorPicker = DKColorPickerWithKey(BG);
	```

+ Use `DKColorPickerWithRGB` to generate a `DKColorPicker`

	```objectivec
	view.dk_backgroundColorPicker =  DKColorPickerWithRGB(0xffffff, 0x343434);
	```

### DKColorTable

`DKColorTable` is a new feature in DKNightVersion which providing us an elegant way to manage color setting in a project. Use as follows:

There is a file called `DKColorTable.txt`

```
NORMAL   NIGHT
#ffffff  #343434 BG
#aaaaaa  #313131 SEP
```

The first line of this file indicated different themes. **NORMAL is required column**, and others are optional. So if you don't need to integrate different themes in your app, just leave the first column in this file, like this:

```
NORMAL
#ffffff BG
#aaaaaa SEP
```

`NORMAL` and `NIGHT` are two different themes, `NORMAL` is default and for normal mode. `NIGHT` is optional and for night mode.

You can add multiple columns in this `DKColorTable.txt` file as many as you want.

```
NORMAL   NIGHT    RED
#ffffff  #343434  #ff0000 BG
#aaaaaa  #313131  #ff0000 SEP
```

The last column is the key for a color entry, DKNightVersion use the current `themeVersion` (ex: `NORMAL` `NIGHT` and `RED`) and key (ex: `BG`, `SEP`) to find the corresponding color in DKColorTable.

`DKColorTable` has a property `file`, it will loads the color setting in this `file` when `+ [DKColorTable sharedColorTable` is called. Default value of `file` is `DKColorTable.txt`.

```objectivec
@property (nonatomic, strong) NSString *file;
```

You can also add another file into your project and fill your color setting in that file.

```
// color.txt
NORMAL   NIGHT
#ffffff  #343434 BG
```

And change `file` value

```objectivec
[DKColorTable sharedColorTable].file = @"color.txt"
```

This will reload color setting from `color.txt` file.

### Create temporary DKColorPicker

If you'd want to create some temporary DKColorPicker, you can use these methods.

```objectivec
view.dk_backgroundColorPicker =  DKColorPickerWithRGB(0xffffff, 0x343434);
```

`DKColorPickerWithRGB` will return a DKColorPicker which set background color to `#ffffff` when current theme version is `DKThemeVersionNormal` and `#343434` when it is `DKThemeVersionNight`.

There are also some similar functions like `DKColorPickerWithColors`

```objectivec
DKColorPicker DKColorPickerWithRGB(NSUInteger normal, NSUInteger night);
DKColorPicker DKColorPickerWithColors(UIColor *normalColor, UIColor *nightColor);
```

`DKColor` also provides a cluster of convinient `API` which returns `DKColorPicker` block, these blocks **return the same color in different themes**.

```objectivec
+ (DKColorPicker)colorPickerWithUIColor:(UIColor *)color;

+ (DKColorPicker)colorPickerWithWhite:(CGFloat)white alpha:(CGFloat)alpha;
+ (DKColorPicker)colorPickerWithHue:(CGFloat)hue saturation:(CGFloat)saturation brightness:(CGFloat)brightness alpha:(CGFloat)alpha;
+ (DKColorPicker)colorPickerWithRed:(CGFloat)red green:(CGFloat)green blue:(CGFloat)blue alpha:(CGFloat)alpha;
+ (DKColorPicker)colorPickerWithCGColor:(CGColorRef)cgColor;
+ (DKColorPicker)colorPickerWithPatternImage:(UIImage *)image;
#if __has_include(<CoreImage/CoreImage.h>)
+ (DKColorPicker)colorPickerWithCIColor:(CIColor *)ciColor NS_AVAILABLE_IOS(5_0);
#endif

+ (DKColorPicker)blackColor;
+ (DKColorPicker)darkGrayColor;
+ (DKColorPicker)lightGrayColor;
+ (DKColorPicker)whiteColor;
+ (DKColorPicker)grayColor;
+ (DKColorPicker)redColor;
+ (DKColorPicker)greenColor;
+ (DKColorPicker)blueColor;
+ (DKColorPicker)cyanColor;
+ (DKColorPicker)yellowColor;
+ (DKColorPicker)magentaColor;
+ (DKColorPicker)orangeColor;
+ (DKColorPicker)purpleColor;
+ (DKColorPicker)brownColor;
+ (DKColorPicker)clearColor;
```

### DKImagePicker

Use `DKImagePicker` to change images when `manager.themeVersion` changes.

```objectivec
imageView.dk_imagePicker = DKImagePickerWithNames(@"normal1", @"night1");
```

The first image is for `NORMAL` the second is for `NIGHT`.

```objectivec
DKImagePicker DKImagePickerWithNames(NSString *normal, NSString *night);
DKImagePicker DKImagePickerWithImages(UIImage *normal, UIImage *night);
```

> DKImagePicker doesn't supported in DKColorTable yet. 

You can set different images like this:

```objectivec
imageView.dk_imagePicker = ^(DKThemeVersion *themeVersion) {
  if ([themeVersion isEqualToString:DKThemeVersionNormal]) {
      return [UIImage imageNamed:@"normal"];
  } else if ([themeVersion isEqualToString:DKThemeVersionNight]) {
      return [UIImage imageNamed:@"night"];
  } else if ([themeVersion isEqualToString:@"RED"]) {
      return [UIImage imageNamed:@"red"];
  }
  return [UIImage new];
};
```

This is just a temporary solution.

# Contribute

Feel free to open an issue or pull request, if you need help or there is a bug.

# Contact

- Powered by [Draveness](http://github.com/draveness)
- Personal website [Draveness](http://draveness.me)

# Todo

- Documentation

# License

DKNightVersion is available under the MIT license. See the LICENSE file for more info.

The MIT License (MIT)

Copyright (c) 2015 Draveness

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

