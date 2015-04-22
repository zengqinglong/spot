Sp◉t <a href="https://travis-ci.org/r-lyeh/spot"><img src="https://api.travis-ci.org/r-lyeh/spot.svg?branch=master" align="right" /></a>
=====

- Spot is a compact and embeddable pixel/image library (C++11).
- Spot supports both RGBA/HSLA pixel types.
- Spot provides both pixel and image algebra for pixel/image manipulation.
- Spot loads WEBP, JPG, progressive JPG, PNG, TGA, DDS DXT1/2/3/4/5, BMP, PSD, GIF, PVR2 (PVRTC), PVR3 (ETC1), KTX (ETC1), PKM (ETC1), HDR, PIC, PNM (PPM/PGM), CRN, PUG and vectorial SVG files.
- Spot saves WEBP, JPG, PNG, TGA, BMP, DDS, PVR3 (ETC1), KTX (ETC1), PKM (ETC1) and PUG files.
- Spot is self-contained. All libraries are included and amalgamated.
- Spot is tiny. A couple of source files.
- Spot is cross-platform.
- Spot is BOOST licensed.

## File format support

| File format  | Read | Write |
| :-------------|:-------------:| :-----:|
| BMP files | yes | yes |
| CRN files | yes | no |
| DDS DXT1/2/3/4/5 files | yes | yes |
| GIF files | yes | no |
| HDR files | yes | no |
| JPG files (progressive) | yes | no |
| JPG files | yes | yes |
| KTX (ETC1) files | yes* | yes* |
| PIC files | yes | no |
| PKM (ETC1) files | yes | yes |
| PNG files | yes | yes |
| PNM (PPM/PGM) files | yes | no |
| PSD files | yes | no |
| PUG files | yes | yes |
| PVR2 (PVRTC) files | yes* | no |
| PVR3 (ETC1) files | yes* | yes* |
| SVG files (rasterized) | yes | no |
| TGA files | yes | yes |
| WEBP files | yes | yes |

(*) partially supported, preliminary, warning, caution, atchung, beware of dog

## todo
- document api
- create more image filters
- clean up code

```c++
template<typename color>
class image : public std::vector<color>
{
    public:

    size_t w, h, d;
    float delay; // frame delay, when loading an animation

    image();
    image( size_t w, size_t h, size_t d = 0, const color &filler = color::hsla() );
    explicit image( const std::string &filename, bool mirror_w = false, bool mirror_h = false );
    explicit image( const void *ptr, size_t len, bool mirror_w = false, bool mirror_h = false );

    bool load( const void *ptr, size_t len, bool mirror_w = false, bool mirror_h = false );
    bool load( const std::string &filename, bool mirror_w = false, bool mirror_h = false );
    
    bool save( const std::string &filename ) const;

    bool save_as_bmp( const std::string &filename ) const;
    bool save_as_dds( const std::string &filename ) const;
    bool save_as_tga( const std::string &filename ) const;
    bool save_as_png( const std::string &filename, unsigned stride = 4 ) const;
    bool save_as_pug( const std::string &filename, unsigned quality = 90 ) const;
    bool save_as_jpg( const std::string &filename, unsigned quality = 90 ) const;
    bool save_as_webp(const std::string &filename, unsigned quality = 90 ) const;
    bool save_as_ktx( const std::string &filename, unsigned quality = 10 ) const;
    bool save_as_pvr( const std::string &filename, unsigned quality = 10 ) const;
    bool save_as_pkm( const std::string &filename, unsigned quality = 10 ) const;

    std::string encode_as_png( unsigned stride = 4 ) const;
    std::string encode_as_pug( unsigned quality = 90 ) const;
    std::string encode_as_jpg( unsigned quality = 90 ) const;
    std::string encode_as_webp(unsigned quality = 90 ) const;
    std::string encode_as_ktx( unsigned quality = 10 ) const;
    std::string encode_as_pvr( unsigned quality = 10 ) const;
    std::string encode_as_pkm( unsigned quality = 10 ) const;

    inline const size_t size() const;
    bool loaded() const;

    inline color &at( size_t offset );
    inline color &at( size_t x, size_t y );
    inline color &at( size_t x, size_t y, size_t z );
    inline color &atf( float x01 );
    inline color &atf( float x01, float y01 );
    inline color &atf( float x01, float y01, float z01 );

    inline const color &at( size_t offset ) const;
    inline const color &at( size_t x, size_t y ) const;
    inline const color &at( size_t x, size_t y, size_t z ) const;
    inline const color &atf( float x01 ) const;
    inline const color &atf( float x01, float y01 ) const;
    inline const color &atf( float x01, float y01, float z01 ) const;

    image copy( size_t ox, size_t oy, size_t w = ~0, size_t h = ~0 ) const;
    image paste( size_t at_x, size_t at_y, const image &other ) const;
    image crop( size_t left, size_t right, size_t top, size_t bottom ) const; // each param is number of row/cols to crop

    image flip_w() const;
    image flip_h() const;
    image rotate_left() const;
    image rotate_right() const;

    image operator *( const color &other ) const;
    image operator /( const color &other ) const;
    image operator +( const color &other ) const;
    image operator -( const color &other ) const;

    image &operator *=( const color &other );
    image &operator /=( const color &other );
    image &operator +=( const color &other );
    image &operator -=( const color &other );

    image to_rgba() const;
    image to_hsla() const;

    image to_premultiplied_alpha() const;
    image to_straight_alpha() const;

    std::vector<unsigned int>  rgba32() const;
    std::vector<unsigned char> rgbx( unsigned char ) const;
    std::vector<unsigned char> rgba() const;
    std::vector<unsigned char> rgb() const;
    std::vector<unsigned char> ya() const;
    std::vector<unsigned char> y() const;
};
```

## sample
```c++
    spot::image img( filename );

    spot::image saturated = img * spot::hsla( 1, 2, 1, 1 );
    spot::image desaturated = img * spot::hsla( 1, 0.5f, 1, 1 );
    spot::image gray = img * spot::hsla( 1, 0, 1, 1 );
    spot::image light = img * spot::hsla( 1, 1, 1.75f, 1 );
    spot::image hue = img + spot::hsla( 0.5f, 0, 0, 0 );
    spot::image warmer = img + spot::hsla( -0.08f, 0, 0, 0 );
    spot::image colder = img + spot::hsla(  0.08f, 0, 0, 0 );

    img.save_as_bmp("collage.bmp");
    img.save_as_dds("collage.dds");
    img.save_as_jpg("collage.jpg", 80);
    img.save_as_ktx("collage.ktx", 10);
    img.save_as_pkm("collage.pkm", 10);
    img.save_as_png("collage.png");
    img.save_as_pug("collage.pug", 80);
    img.save_as_pvr("collage.pvr", 10);
    img.save_as_webp("collage.webp", 80);
```

## possible output
```bash
22/04/2015  16:32           607.990 collage.bmp
22/04/2015  16:32           203.264 collage.dds
22/04/2015  16:32            44.040 collage.jpg
22/04/2015  16:32           100.900 collage.ktx
22/04/2015  16:32           100.848 collage.pkm
22/04/2015  16:32           296.306 collage.png
22/04/2015  16:32            44.225 collage.pug
22/04/2015  16:32           100.884 collage.pvr
22/04/2015  16:32            32.384 collage.webp
```

![image](https://raw.github.com/r-lyeh/depot/master/spot_collage.jpg)

## color transfer sample
![image](https://raw.github.com/r-lyeh/depot/master/spot_color_transfer.jpg)

## licenses
- [spot](https://github.com/r-lyeh/spot) (BOOST licensed).
- [crn2dds](redist/deps/crn2dds) by r-lyeh, SpartanJ and Evan Parker (Public Domain).
- [crnlib](https://code.google.com/p/crunch/), by Rich Geldreich (ZLIB license).
- [DDS writer](http://www.lonesock.net/soil.html) by Jonathan Dummer (Public Domain).
- [etc1utils](redist/deps/soil2/etc1_utils.h) by Google Inc (Apache 2.0 license).
- [jpge](https://code.google.com/p/jpeg-compressor/) by Rich Geldreich (Public Domain).
- [libwebp](https://code.google.com/p/webp/) by Google Inc (BSD license).
- [lodepng](http://lodev.org/lodepng/) by Lode Vandevenne (ZLIB license).
- [nanosvg](https://github.com/memononen/nanosvg/) by Mikko Mononen (ZLIB license).
- [pngrim](https://github.com/fgenesis/pngrim) alpha bleeding algorithm by F-Genesis (Public Domain).
- [pug](https://github.com/r-lyeh/pug) (Public Domain).
- [rg_etc1](https://code.google.com/p/rg-etc1/) by Rich Geldreich (ZLIB license).
- [soil2](https://bitbucket.org/SpartanJ/soil2/) by Martin Lucas Golini and Jonathan Dummer (Public Domain).
- [stb_image](http://github.com/nothings/stb) by Sean Barrett (Public Domain).

## notes
- Samples are Public Domain licensed. Samples use CImg.h by David Tschumperle (CeCILL-C license) to display images. 
- gcc users may need strict aliasing disabled if using CRN textures: add `-fno-strict-aliasing` compilation flag.

## changelog
- 2015/04/23 v2.0.1: bugfixed b/w images;
- 2015/04/22 v2.0.0: etc1 encode/decode support; pvr3/ktx/pkm load/save support; a few optimizations
