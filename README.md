# JPG Only

A module for ProcessWire CMS/CMF. Converts several different image formats to JPG format on upload. The original uploaded file is NOT kept.

If you have ImageMagick installed in your hosting environment then the module can covert many different formats to JPG including PNG, GIF, WEBP, BMP, TIFF, PSD, SVG, AI, EPS, PDF, maybe more. Although your mileage may vary in terms of which formats are convertable and the quality of the converted image.

If you only have GD installed then the supported source formats are PNG, GIF and possibly BMP and and WEBP.

## Installation

[Install](http://modules.processwire.com/install-uninstall/) the JPG Only module.

Set the quality for the JPG conversion in the module config if you like.

## Usage

In your image field settings, set the "Valid File Extensions" to include any formats you want to convert from.

The default behaviour is to convert all images to JPG on upload, for all image fields in the website. If you want to opt-out of the JPG conversions on certain fields, certain templates, etc, then you can hook after `JpgOnly::allowJpgConversion`. For example, in `/site/ready.php`:

```php
$wire->addHookAfter('JpgOnly::allowJpgConversion', function(HookEvent $event) {

    $filename = $event->arguments(0);
    $inputfield = $event->arguments(1);
    $page = $event->arguments(2);

    // Not for PNG images
    if(strtolower(substr($filename, -4)) === '.png') $event->return = false;

    // Not for the special_images field
    if($inputfield->hasField == 'special_images') $event->return = false;

    // Not for image fields in the Home template
    if($page->template == 'home') $event->return = false;

});
```

### Using the `imgToJpg()` method from the API

If you are adding an image to a field using the API you might want to use `JpgOnly::imgToJpg()` to convert the image before you add it. The method must be supplied with two arguments:

1. The full path or URL of an image
2. The Pageimages object that you intend to later add the converted image to

The method will return the full local path to the converted image.

Example:

```php
$file = 'https://upload.wikimedia.org/wikipedia/commons/f/fa/718smiley.png';
$pageimages = $page->gallery_images;
$path_to_converted_image = $modules->JpgOnly->imgToJpg($file, $pageimages);
// Now you can add $path_to_converted_image to $page->gallery_images if you like
```
