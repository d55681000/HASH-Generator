# SONY HASH — Complete Feature and Operation Reference

## Input and Data Processing
- Direct text entry and paste input; text is processed as UTF-8.
- Drag-and-drop file input.
- File selection via the **Load File** control.
- Automatic recalculation whenever text input changes.
- Deep file-structure scanning and polyglot / embedded-format detection after a file is loaded.
- Grapheme-based character counting.
- Shannon entropy calculation, displayed in `bit/byte`.
- Text normalization modes:
  - None
  - NFC
  - NFKC
- Invisible-character and line-ending sanitization:
  - Removes U+200B–U+200D
  - Removes U+2060
  - Removes U+FEFF
  - Converts CRLF to LF
  - Converts CR to LF
- HEX display modes:
  - `lowercase`
  - `UPPERCASE`
- Output grouping lengths:
  - No grouping
  - 2 characters
  - 4 characters
  - 6 characters
  - 8 characters
  - 10 characters
  - 12 characters
  - 14 characters
  - 16 characters
  - 32 characters
  - 64 characters
- Custom separator string: accepts any user-defined string.

## Hash and Checksum Algorithms
- CRC32
- CRC64 variants:
  - `XZ (ECMA, Reflected)`
  - `WE (ECMA, Non-Reflected)`
  - `ISO (HDLC, Non-Reflected)`
  - `GO-ISO (ISO, Reflected)`
  - `Redis (Jones, Reflected)`
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512
- The CRC64 label can be clicked to cycle through all five CRC64 variants.

## Salt
- Enable / disable Salt.
- Custom Salt string.
- Salt is appended to the input data before hashing.

## HMAC
- Enable / disable HMAC.
- Custom HMAC key.
- Supported HMAC algorithms:
  - HMAC-MD5
  - HMAC-SHA-1
  - HMAC-SHA-224
  - HMAC-SHA-256
  - HMAC-SHA-384
  - HMAC-SHA-512
- CRC32 and CRC64 remain non-HMAC checksums.

## Iterative Hashing
- Iteration count range: `1` to `4294967295`.
- Iteration count can be entered directly.
- Increment / decrement controls are available.
- Iteration modes:
  - `Standard (Raw)`
  - `Legacy Compatibility (Hex)`
  - `XOR Overlay (XOR)`
- Raw mode: the next iteration uses the previous digest as raw bytes.
- Hex mode: the next iteration uses the UTF-8 bytes of the previous hexadecimal digest string.
- XOR mode: results from successive iterations are accumulated using XOR.

## Base64
- Base64 Encode: enable / disable.
- Base64 Decode: enable / disable.
- Encoding format: `Standard Base64`.
- Decoding supports standard Base64.
- URL-safe Base64 characters `-` and `_` are accepted and internally converted to `+` and `/`.
- Missing `=` padding can be restored automatically.
- Decoded output may be returned as UTF-8 text or binary data.

## Base85
- Base85 Encode: enable / disable.
- Base85 Decode: enable / disable.
- Supported variants:
  - `Standard ASCII85`
  - `Adobe ASCII85`
  - `ZeroMQ (Z85)`
  - `RFC 1924`
- Adobe ASCII85 supports the `<~ ... ~>` wrapper.
- Standard / Adobe ASCII85 supports:
  - `z` shorthand for zero-value blocks
  - `y` shorthand for space blocks
- ZeroMQ Z85:
  - Input byte length must be a multiple of 4 for encoding.
  - Encoded string length must be a multiple of 5 for decoding.
- RFC 1924:
  - Input byte length must be a multiple of 4 for encoding.
  - Encoded string length must be a multiple of 5 for decoding.
- The Base85 label can be clicked to cycle through all supported variants.
- Automatic Base85 variant detection is supported.

## Automatic Base File Detection
- Enable / disable automatic file detection for Base decoding.
- After Base64 / Base85 decoding, the tool attempts UTF-8 decoding.
- If the result cannot be interpreted as text, it is treated as binary.
- Binary output is classified by file signature / magic bytes.
- Binary results can be downloaded directly.

## Binary File Types Recognized for Decoded Output

### Images
- `.png` — `image/png`
- `.jpg` — `image/jpeg`
- `.gif` — `image/gif`
- `.bmp` — `image/bmp`
- `.tif` — `image/tiff` — Intel / Little-endian TIFF signature
- `.tif` — `image/tiff` — Motorola / Big-endian TIFF signature
- `.ico` — `image/x-icon`
- `.cur` — `image/x-icon`
- `.jp2` — `image/jp2`
- `.psd` — `image/vnd.adobe.photoshop`
- `.dds` — `image/vnd.ms-dds`
- `.exr` — `image/x-exr`
- `.webp` — `image/webp`
- `.heic` — `image/heic`
  - ISO BMFF brands: `heic`, `heix`, `mif1`
- `.avif` — `image/avif`
  - ISO BMFF brands: `avif`, `avis`
- `.svg` — `image/svg+xml`

### Audio
- `.mp3` — `audio/mpeg`
  - Recognized signatures: ID3, FFFB, FFF3, FFF2
- `.aac` — `audio/aac`
  - Recognized signatures: FFF1, FFF9
- `.ogg` — `audio/ogg`
- `.flac` — `audio/flac`
- `.mid` — `audio/midi`
- `.dsf` — `audio/x-dsf`
- `.dff` — `audio/x-dff`
- `.tta` — `audio/x-tta`
- `.ape` — `audio/ape`
- `.wv` — `audio/wavpack`
- `.wma` — `audio/x-ms-wma`
- `.wav` — `audio/wav`
- `.aiff` — `audio/x-aiff`
  - Recognizes AIFF and AIFC
- `.m4a` — `audio/mp4`

### Video
- `.mkv` — `video/x-matroska`
- `.webm` — `video/webm`
- `.mpg` — `video/mpeg`
  - Recognizes MPEG Pack and Sequence signatures
- `.mp4` — `video/mp4`
  - ISO BMFF brands: `mp41`, `mp42`, `isom`
  - Unknown `ftyp` brands fall back to MP4
- `.m4v` — `video/mp4`
- `.mov` — `video/quicktime`
- `.avi` — `video/x-msvideo`

### Archives and Containers
- `.zip` — `application/zip`
- `.rar` — `application/x-rar-compressed`
- `.7z` — `application/x-7z-compressed`
- `.gz` — `application/gzip`
- `.bz2` — `application/x-bzip2`
- `.tar` — `application/x-tar`

### Documents, Disk Images, Executables, and Other Formats
- `.pdf` — `application/pdf`
- `.iso` — `application/x-iso9660-image`
- `.exe` — `application/x-msdownload`
- `.cab` — `application/vnd.ms-cab-compressed`
- `.msi` — `application/x-msi`
- `.lnk` — `application/x-ms-shortcut`
- `.chm` — `application/vnd.ms-htmlhelp`
- `.vhdx` — `application/x-vhd`
- `.dwg` — `image/vnd.dwg`
- `.blend` — `application/x-blender`
- `.txt` — `text/plain; charset=utf-16le`
- `.txt` — `text/plain; charset=utf-16be`
- `.bin` — `application/octet-stream` — fallback for unknown binary formats

## File Structure Analysis
- Windows PE (`EXE/DLL`)
- MS-DOS Executable
- Executable / Unverified PE
- MP3 ID3v2 with approximate ID3 tag / cover size estimation
- SQLite 3 Database
- ZIP Archive
- ZIP Archive (Verified)
- JPEG Image
- PNG Image
- ZIP EOCD / ZIP64 locator tail inspection
- Polyglot detection when ZIP structures are found at the file tail even though the primary format is not ZIP

## Match Verification
- User-supplied comparison value.
- Hash comparison normalization:
  - Removes whitespace
  - Removes colons
  - Removes hyphens
  - Converts to lowercase
- Supported comparison targets:
  - CRC32
  - CRC64
  - MD5
  - SHA-1
  - SHA-224
  - SHA-256
  - SHA-384
  - SHA-512
  - Base64 Enc
  - Base64 Dec
  - Base85 Enc
  - Base85 Dec
- A 16-character hexadecimal CRC64 value can be matched against all CRC64 variants.
- Base85 comparison can identify a single variant or report multiple candidate variants.
- Match results also report character count and entropy.

## Built-in Test Vectors
- Empty string `""`
- `abc`
- `123456789`
- **Apply**:
  - Loads the selected test vector.
  - Clears Salt.
  - Clears HMAC.
  - Resets iteration count to 1.
  - Resets iteration mode to Standard (Raw).
- **Verify**:
  - CRC32
  - Current CRC64 variant
  - MD5
  - SHA-1
  - SHA-224
  - SHA-256
  - SHA-384
  - SHA-512

## Result Operations
- Click a standard result to copy its raw value to the clipboard.
- In a secure context, the Clipboard API is used.
- In a non-secure context, the tool falls back to a hidden textarea plus `execCommand('copy')`.
- Each hash algorithm can be stopped individually.
- Multi-iteration processing can be stopped globally.
- Result fields support keyboard activation with Enter or Space.

## Export and Save Formats

### Comprehensive Audit Report
- Format name: `Markdown Report`
- Extension: `.md`
- MIME type: `text/markdown`
- Filename pattern:
  - `sony-hash-audit-<Japan-time-timestamp>.md`
- Includes:
  - System / environment information
  - Configuration parameters
  - Input target
  - FORMATTED results
  - RAW results
- Supported result entries:
  - CRC32
  - CRC64
  - MD5
  - SHA-1
  - SHA-224
  - SHA-256
  - SHA-384
  - SHA-512
  - Base64 Enc
  - Base64 Dec
  - Base85 Enc
  - Base85 Dec
- When HMAC is enabled, report labels may include:
  - `HMAC-MD5`
  - `HMAC-SHA-1`
  - `HMAC-SHA-224`
  - `HMAC-SHA-256`
  - `HMAC-SHA-384`
  - `HMAC-SHA-512`

### Large Base64 Direct-to-Disk Export
- Filename: `base64_out.txt`
- Extension: `.txt`

### Large Base85 Direct-to-Disk Export
- Filename: `base85_out.txt`
- Extension: `.txt`

### Large Text Result Export
- Filename pattern:
  - `SONY_HASH_<Result-ID>.txt`
- Possible Base result filenames include:
  - `SONY_HASH_Base64-Enc.txt`
  - `SONY_HASH_Base64-Dec.txt`
  - `SONY_HASH_Base85-Enc.txt`
  - `SONY_HASH_Base85-Dec.txt`

### Decoded Binary Export
- Filename pattern:
  - `decoded_YYYYMMDD_HHMMSS.<detected-extension>`
- The detected extension may be any recognized binary format listed above.
- Unknown binary formats use `.bin`.

## Large File Processing
- In environments supporting File Streams and the Save File Picker:
  - Standard file loading supports files up to 300 MB.
  - Larger files switch to the **Start Large File Output** workflow.
- In environments without streaming save support:
  - File size limit is 50 MB.
- Files `≤ 10 MB`:
  - Full hash processing is available.
  - SHA-384 and SHA-512 are supported.
  - Base64 and Base85 encoding are supported.
- Files `> 10 MB`:
  - Incremental streaming calculation is used for:
    - CRC32
    - All CRC64 variants
    - MD5
    - SHA-1
    - SHA-224
    - SHA-256
  - SHA-384 and SHA-512 are marked as exceeding the supported file-size limit.
- Large Base64 / Base85 output can be written directly to disk without first storing the entire result in the DOM.

## Themes and Display
- Available themes:
  - `DEFAULT`
  - `Command Prompt`
  - `PowerShell`
- Fullscreen / normal view toggle.

## System and Environment Information
- OS / platform
- OS version
- CPU architecture
- Bitness
- Language
- Time zone
- Desktop / mobile classification
- Multi-touch point count
- CPU core count
- Device memory
- GPU renderer
- Screen resolution
- Device pixel ratio
- Color depth
- HDR / SDR capability
- Viewport dimensions
- Browser brand / version
- System dark / light theme
- Battery percentage
- Charging state
- Network effective type
- Downlink Mbps
- RTT ms
- Public IP
- IPv4 / IPv6 classification
- Japan Standard Time synchronization

## Location Information
- Enable / disable location lookup.
- IP-based geolocation.
- For Japan:
  - Prefecture
  - Municipality / ward / city
  - Postal code
  - ISP
- Locations outside Japan are reported as out of scope.
- Geolocation results are cached for 12 hours.

## ZESS Isolation Mode
- Full name: `ZESS (ZERO-EGRESS STATELESS SANDBOX)`
- Activation:
  - `?U-571=1`
  - `#U-571`
- Blocks `fetch`.
- Blocks `XMLHttpRequest`.
- Applies CSP with `connect-src 'none'`.
- Replaces `localStorage` and `sessionStorage` with in-memory temporary storage.
- Unregisters Service Workers.
- Clears Cache Storage.
- Deletes enumerable IndexedDB databases.
- Disables location lookup.

## DOM Protection
- Automatically removes dynamically inserted `IFRAME` elements.
- Automatically removes `OBJECT` elements.
- Automatically removes `EMBED` elements.
- Automatically removes suspected advertisement nodes and nodes with abnormally high z-index values.

## Emergency Exit
- Windows / non-Apple:
  - `Ctrl + Shift + I`
  - `Ctrl + Shift + J`
  - `Ctrl + Shift + C`
- Apple:
  - `⌘ + Option + I`
  - `⌘ + Option + J`
  - `⌘ + Option + C`
- `F12`
- Non-Apple `Meta / Windows` key
- Press `Esc` 4 times within 500 ms
- Four-finger-or-more touch gesture
- On activation:
  - Stops page execution
  - Stops hash workers
  - Stops Base64 worker
  - Stops Base85 worker
  - Clears the page
  - Redirects to Google





https://d55681000.github.io/HASH-Generator/

https://hash-generatorg.d55681000.workers.dev/





"This is a browser-based hash calculator made purely for fun. Honestly, I'm not quite sure how to describe it, so please treat it as something fun rather than a practical tool. Any brand names mentioned are just for laughs—none of it is real, and this project was created solely for my own personal satisfaction."

These tasks were completed by Gemini V3.1 Pro
