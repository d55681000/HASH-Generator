# HASH — Complete Capabilities and Operations Reference

## 1. Input and Preprocessing

- Accepts direct text input and pasted text.
- Processes text as UTF-8.
- Accepts files by:
  - Drag and drop
  - File picker
- Recalculates results automatically when the text input changes.
- Performs file-structure analysis after a file is loaded.
- Detects possible polyglot / embedded-container structures.
- Reports:
  - Grapheme count
  - Shannon entropy in `bit/byte`

### Text Normalization
- None
- NFC
- NFKC

### Sanitization
- Removes:
  - U+200B–U+200D
  - U+2060
  - U+FEFF
- Normalizes line endings:
  - CRLF → LF
  - CR → LF

### Hexadecimal Output Case
- Lowercase
- Uppercase

### Output Grouping
- None
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

### Separator
- Supports an arbitrary user-defined separator string.

---

## 2. Hash and Checksum Algorithms

- CRC32
- CRC64
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512

### CRC64 Variants
- `XZ (ECMA, Reflected)`
- `WE (ECMA, Non-Reflected)`
- `ISO (HDLC, Non-Reflected)`
- `GO-ISO (ISO, Reflected)`
- `Redis (Jones, Reflected)`

The CRC64 selector can cycle through all five variants.

---

## 3. Salt

- Salt can be enabled or disabled.
- Accepts a custom salt string.
- The salt is appended to the input before hashing.

---

## 4. HMAC

HMAC can be enabled or disabled and uses a user-supplied key.

### Supported HMAC Algorithms
- HMAC-MD5
- HMAC-SHA-1
- HMAC-SHA-224
- HMAC-SHA-256
- HMAC-SHA-384
- HMAC-SHA-512

### Not HMAC-Based
- CRC32
- CRC64

---

## 5. Iterative Hashing

### Iteration Count
- Minimum: `1`
- Maximum: `4294967295`
- Can be entered directly.
- Increment and decrement controls are available.

### Iteration Modes
- `Standard (Raw)`
- `Legacy Compatibility (Hex)`
- `XOR Overlay (XOR)`

### Mode Behavior
- `Standard (Raw)`: feeds the previous digest back as raw bytes.
- `Legacy Compatibility (Hex)`: feeds the UTF-8 bytes of the previous hexadecimal digest string into the next iteration.
- `XOR Overlay (XOR)`: accumulates successive iteration results using XOR.

---

## 6. Base64

### Operations
- Encode
- Decode

### Encoding
- Produces standard Base64 output.

### Decoding Compatibility
- Accepts standard Base64.
- Accepts URL-safe Base64 characters:
  - `-`
  - `_`
- Internally maps URL-safe characters to:
  - `+`
  - `/`
- Can restore missing `=` padding.

### Decoded Output
- UTF-8 text, when valid
- Binary data, otherwise

---

## 7. Base85

### Operations
- Encode
- Decode

### Supported Variants
- `Standard ASCII85`
- `Adobe ASCII85`
- `ZeroMQ (Z85)`
- `RFC 1924`

The Base85 selector can cycle through all supported variants.

### Adobe ASCII85
- Supports the `<~ ... ~>` wrapper.

### Standard / Adobe ASCII85 Shorthand
- `z` for zero-value blocks
- `y` for space blocks

### ZeroMQ Z85 Constraints
- Encoding input length must be a multiple of 4 bytes.
- Decoding input length must be a multiple of 5 characters.

### RFC 1924 Constraints
- Encoding input length must be a multiple of 4 bytes.
- Decoding input length must be a multiple of 5 characters.

### Variant Detection
- Can infer likely Base85 variant candidates during comparison.

---

## 8. Automatic File-Type Detection for Decoded Base Data

Automatic file detection can be enabled or disabled.

After Base64 or Base85 decoding:
- The tool first attempts UTF-8 interpretation.
- Valid UTF-8 is treated as text.
- Otherwise, the result is treated as binary.
- Binary data is classified using file signatures / magic bytes.
- Detected binary output can be downloaded directly.

---

## 9. Recognized Binary File Types

### Images
- `.png` — `image/png`
- `.jpg` — `image/jpeg`
- `.gif` — `image/gif`
- `.bmp` — `image/bmp`
- `.tif` — `image/tiff`
  - Little-endian TIFF
  - Big-endian TIFF
- `.ico` — `image/x-icon`
- `.cur` — `image/x-icon`
- `.jp2` — `image/jp2`
- `.psd` — `image/vnd.adobe.photoshop`
- `.dds` — `image/vnd.ms-dds`
- `.exr` — `image/x-exr`
- `.webp` — `image/webp`
- `.heic` — `image/heic`
  - ISO BMFF brands:
    - `heic`
    - `heix`
    - `mif1`
- `.avif` — `image/avif`
  - ISO BMFF brands:
    - `avif`
    - `avis`
- `.svg` — `image/svg+xml`

### Audio
- `.mp3` — `audio/mpeg`
  - ID3
  - FFFB
  - FFF3
  - FFF2
- `.aac` — `audio/aac`
  - FFF1
  - FFF9
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
  - AIFF
  - AIFC
- `.m4a` — `audio/mp4`

### Video
- `.mkv` — `video/x-matroska`
- `.webm` — `video/webm`
- `.mpg` — `video/mpeg`
- `.mp4` — `video/mp4`
  - ISO BMFF brands:
    - `mp41`
    - `mp42`
    - `isom`
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

### Documents, Disk Images, Executables, and Other Binary Types
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
- `.bin` — `application/octet-stream`
  - Fallback for unrecognized binary data

---

## 10. File-Structure Analysis

The file analyzer recognizes or reports structures including:

- Windows PE (`EXE/DLL`)
- MS-DOS executable
- Executable / unverified PE
- MP3 ID3v2
  - Approximate ID3 tag size
  - Approximate embedded cover size
- SQLite 3 database
- ZIP archive
- Verified ZIP archive
- JPEG image
- PNG image
- ZIP EOCD tail structures
- ZIP64 locator structures
- Possible polyglot files when ZIP structures are found at the tail of a file whose primary format is not ZIP

---

## 11. Match / Verification Mode

A user-supplied value can be compared against generated results.

### Comparison Normalization
For hash comparison, the tool:
- Removes whitespace
- Removes colons
- Removes hyphens
- Converts input to lowercase

### Comparison Targets
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

### CRC64 Variant Identification
- A 16-character hexadecimal CRC64 value can be checked against all CRC64 variants.

### Base85 Variant Identification
- Can identify a matching variant.
- Can return multiple candidate variants when the input is ambiguous.

### Additional Comparison Metrics
- Grapheme count
- Shannon entropy

---

## 12. Built-In Test Vectors

### Available Test Inputs
- Empty string `""`
- `abc`
- `123456789`

### Apply
Applying a test vector:
- Loads the selected input.
- Disables / clears Salt.
- Disables / clears HMAC.
- Resets iterations to `1`.
- Resets iteration mode to `Standard (Raw)`.

### Verify
Verification covers:
- CRC32
- Selected CRC64 variant
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512

---

## 13. Result Actions

- Click a normal result to copy its raw value.
- Keyboard activation is supported with:
  - Enter
  - Space
- Clipboard behavior:
  - Uses the Clipboard API in secure contexts.
  - Falls back to a hidden textarea and `execCommand('copy')` otherwise.
- Individual hash calculations can be stopped.
- Multi-iteration processing can be stopped globally.
- Binary decoded results can be downloaded directly.
- Very large text results can be downloaded as `.txt`.

---

## 14. Export and Download Formats

### 14.1 Markdown Audit Report
- Display name: `Markdown Report`
- Extension: `.md`
- MIME type: `text/markdown`
- Filename pattern:
  - `sony-hash-audit-<Japan-time-timestamp>.md`

### Report Contents
- System / environment information
- Active configuration
- Input target
- `FORMATTED` results
- `RAW` results

### Result Fields Included in the Report
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

### HMAC Report Labels
When HMAC is enabled:
- `HMAC-MD5`
- `HMAC-SHA-1`
- `HMAC-SHA-224`
- `HMAC-SHA-256`
- `HMAC-SHA-384`
- `HMAC-SHA-512`

### 14.2 Large Base64 Direct-to-Disk Export
- Filename:
  - `base64_out.txt`
- Extension:
  - `.txt`

### 14.3 Large Base85 Direct-to-Disk Export
- Filename:
  - `base85_out.txt`
- Extension:
  - `.txt`

### 14.4 Large Text Result Export
- Filename pattern:
  - `SONY_HASH_<Result-ID>.txt`

Possible Base result filenames include:
- `SONY_HASH_Base64-Enc.txt`
- `SONY_HASH_Base64-Dec.txt`
- `SONY_HASH_Base85-Enc.txt`
- `SONY_HASH_Base85-Dec.txt`

### 14.5 Decoded Binary Export
- Filename pattern:
  - `decoded_YYYYMMDD_HHMMSS.<detected-extension>`

The extension is derived from detected file type.

If no known type is identified:
- Extension: `.bin`
- MIME type: `application/octet-stream`

---

## 15. Large-File Processing

### File-Size Limits
When File System Access / streaming-save support is available:
- Standard file-loading limit: 300 MB
- Files above that threshold enter the large-file-output workflow.

Without streaming-save support:
- File-size limit: 50 MB

### Files up to 10 MB
Full processing includes:
- CRC32
- All CRC64 variants
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512
- Base64 encoding
- Base85 encoding

### Files above 10 MB
Incremental streaming calculation is used for:
- CRC32
- All CRC64 variants
- MD5
- SHA-1
- SHA-224
- SHA-256

For this path:
- SHA-384 is marked as exceeding the supported text-processing size.
- SHA-512 is marked as exceeding the supported text-processing size.

### Streaming Output
Large Base64 / Base85 output can be written directly to disk rather than retained entirely in the DOM.

---

## 16. Themes and Display

### Themes
- `DEFAULT`
- `Command Prompt`
- `PowerShell`

### Display Controls
- Fullscreen
- Normal view

---

## 17. System and Environment Information

The environment panel can report:

- Operating system / platform
- OS version
- CPU architecture
- Bitness
- Language
- Time zone
- Desktop / mobile classification
- Maximum touch points
- CPU core count
- Device memory
- GPU renderer
- Screen resolution
- Device pixel ratio
- Color depth
- HDR / SDR capability
- Viewport dimensions
- Browser brand
- Browser version
- System dark / light preference
- Battery percentage
- Charging state
- Network effective type
- Downlink rate
- RTT
- Public IP address
- IPv4 / IPv6 classification
- Japan Standard Time synchronization / offset

---

## 18. Location Information

- Location lookup can be enabled or disabled.
- Uses IP-based geolocation.

### Japan-Specific Fields
- Prefecture
- Municipality / ward / city
- Postal code
- ISP

Locations outside Japan are reported as out of scope.

### Cache
- Geolocation results are cached for 12 hours.

---

## 19. ZESS Isolation Mode

### Name
`ZESS (ZERO-EGRESS STATELESS SANDBOX)`

### Activation
- `?U-571=1`
- `#U-571`

### Network Isolation
- Blocks `fetch`.
- Blocks `XMLHttpRequest`.
- Applies a CSP with:
  - `connect-src 'none'`

### Storage Isolation
- Replaces `localStorage` with in-memory temporary storage.
- Replaces `sessionStorage` with in-memory temporary storage.
- Unregisters Service Workers.
- Clears Cache Storage.
- Deletes enumerable IndexedDB databases.

### Other Behavior
- Disables location lookup.

---

## 20. DOM Protection

The runtime protection layer can remove dynamically inserted:

- `IFRAME`
- `OBJECT`
- `EMBED`

It can also remove nodes identified as:
- Suspected advertisements
- Abnormally high-z-index overlays

---

## 21. Emergency Exit

### Keyboard Triggers

#### Windows / Non-Apple
- `Ctrl + Shift + I`
- `Ctrl + Shift + J`
- `Ctrl + Shift + C`

#### Apple
- `⌘ + Option + I`
- `⌘ + Option + J`
- `⌘ + Option + C`

#### Additional Triggers
- `F12`
- `Meta / Windows` key on non-Apple platforms
- Press `Esc` four times within 500 ms
- Touch gesture using four or more fingers

### Activation Behavior
- Stops page execution.
- Stops hash workers.
- Stops the Base64 worker.
- Stops the Base85 worker.
- Clears the page.
- Redirects to Google.





https://d55681000.github.io/HASH-Generator/

https://hash-generatorg.d55681000.workers.dev/





"This is a browser-based hash calculator made purely for fun. Honestly, I'm not quite sure how to describe it, so please treat it as something fun rather than a practical tool. Any brand names mentioned are just for laughs—none of it is real, and this project was created solely for my own personal satisfaction."

These tasks were completed by Gemini V3.1 Pro
