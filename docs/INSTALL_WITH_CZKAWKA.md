## (Mac OS X) Install Instructions with Czkawka

### Building 

### Building libdng (Mac Specific)

Download and install the Adobe DNG SDK 1.7.1, unzip, then open `dng_sdk_1_7_1/dng_sdk/projects/mac/dng_validate.xcodeproj`

Make a new target called "dng", and in the type menu select static library.

Target Dependencies will need to include:
```text
XMPCoreStaticRelease (XMPCore64)
XMPFilesStatic_Release (XMPFiles64)
jxl_release (jxl)
dng_validate release (dng_validate)
```

And you'll want to set the `Link Binary With Libraries` section to:

```text
CoreServices.Framework
CoreFoundation.Framework
libjxl_release.a
libXMPCoreStatic_Release.a
libXMPFilesStatic_Release.a
```

Then add all dng_sdk + libjpeg `.c` / `.cpp` files to compile sources, and all `.h` to headers.

In the scheme section, set `mach-o type` to dynamic library, set optimization level to `-O3`. I believe I'm
having more success with DNG files with `DNG_VALIDATE_FLAGS` set to `qDNGValidate=0 qDNGValidateTarget=0 qDNGUseLibJPEG=1`,
though some `smartpreview` DNGs still won't hash.

Then run build. With any luck, your new library will live in `dng_sdk_1_7_1/dng_sdk/targets/mac/libraries`

Move libdng.a to /usr/local/lib, then:

```shell
mkdir -p /usr/local/include/libdng
cp dng_sdk_1_7_1/dng_sdk/source/*.h /usr/local/include/libndg/
```

### Brew, libheif, libjpeg

Install [brew](https://brew.sh/), install libheif, install libjpeg. If you already have libjpeg installed, you might want a reminder of what commands you need to get it available in your environment for LibRaw:

```sh
# Ensure libjpeg is installed and
# in our build environment:
┌───────────────────>
│
└─> brew reinstall libjpeg

==> Caveats
jpeg is keg-only, which means it was not symlinked into /opt/homebrew,
because it conflicts with `jpeg-turbo`.

If you need to have jpeg first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/jpeg/bin:$PATH"' >> ~/.zshrc

For compilers to find jpeg you may need to set:
  export LDFLAGS="-L/opt/homebrew/opt/jpeg/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/jpeg/include"

For pkg-config to find jpeg you may need to set:
  export PKG_CONFIG_PATH="/opt/homebrew/opt/jpeg/lib/pkgconfig"

# The ones we care about for the configure script:
# export LDFLAGS="-L/opt/homebrew/opt/jpeg/lib" && \
# export CPPFLAGS="-I/opt/homebrew/opt/jpeg/include" 
# We'll need them alongside LDFLAGS and CPPFLAGS for libdng.
```

### Building LibRaw

```sh
┌───────────────────>
│
└─> git clone https://github.com/LibRaw/LibRaw.git

┌───────────────────>
│
└─> cd LibRaw

# Heads up that the LibRaw link to their 202403 snapshot 
# just goes straight to their GitHub, where there's no
# tag or release for this snap shot. We're just going
# to use this commit, it's master branch @ 
# Friday August 30, 2024.

┌───────────────────>
│
└─> git checkout 70f511871e002942d3e6b60c99fe04ce5c0c605b

# LibRaw repo checkouts are not releases, so configure isn't ready yet, they have a script to generate that.

┌───────────────────>
│
└─> ./mkdist.sh

┌───────────────────>
│
└─> export CPPFLAGS="-I/usr/local/include/libdng -I/opt/homebrew/opt/jpeg/include -DUSE_DNGSDK -Wno-deprecated-register" & \
export LDFLAGS="-ldl /usr/local/lib/libdng.a -L/opt/homebrew/opt/jpeg/lib -ljpeg" & \
export MACOSX_DEPLOYMENT_TARGET=14.0 

┌───────────────────>
│
└─> ./configure --enable-jpeg --enable-zlib --enable-lcms

# Double check the output for:
#   checking for jpeg_mem_src in -ljpeg... yes
#   checking for jpeglib.h... yes
# to confirm your libjpeg was available to configure.

┌───────────────────>
│
└─> make

┌───────────────────>
│
└─> make install
```

### Building Czkawka

```sh
┌───────────────────>
│
└─> git clone https://github.com/qarmin/czkawka.git

┌───────────────────>
│
└─> cd czkawka
```

Make this change to the `czkawka_core/Cargo.toml`

```diff
--- a/czkawka_core/Cargo.toml
+++ b/czkawka_core/Cargo.toml
@@ -62,7 +62,7 @@ once_cell = "1.19"
 # Raw image files
 rawloader = "0.37"
 imagepipe = "0.5"
-libraw-rs = { version = "0.0.4", optional = true }
+libraw-rs = { git = "https://github.com/tommyyork/libraw-rs.git", version = "0.0.5", optional = true, features = ["uselocal"]}
```

There was a bug on the Czkawka master branch commit I used (`2a32a52`) from the time module, so that needed to be updated too. Change libheif location as necessary.

```sh
cargo update -p time
cargo run --release --bin czkawka_gui --features "heif,libraw"
```

Now go into the Czkawka gui, go to the settings, optimize your connection to whatever storage medium you're using, crank the threads all the way up, do the `cargo run` command, then start deduplicating.

At the moment, LibRaw fails to parse SmartPreview dng's, which I suspect would be parseable with the Adobe DNG SKD 1.7.1 integrated.