# Hashing and Encoding Utility

## 1. Input and Preprocessing

### Text Input
- Direct text entry.
- Text is encoded as UTF-8 before hashing or encoding.
- Grapheme-count reporting.
- Shannon entropy reporting in `bit/byte`.
- IME composition handling.
- Paste handling with optional sanitization.
- Editing the text field clears an active file-input state.

### File Input
- File picker.
- Drag-and-drop file loading.
- File data is processed as the original byte sequence.
- Text normalization and text sanitization do not apply to file data.
- File-mode hashing always uses one iteration.
- Base64 Decode and Base85 Decode are not supported directly on loaded file input.

### Unicode Normalization
Available modes:
- `None`
- `NFC`
- `NFKC`

### Invisible-Character and Line-Ending Sanitization
Optional preprocessing removes:
- `U+200B` ZERO WIDTH SPACE
- `U+200C` ZERO WIDTH NON-JOINER
- `U+200D` ZERO WIDTH JOINER
- `U+2060` WORD JOINER
- `U+FEFF` ZERO WIDTH NO-BREAK SPACE / BOM

Line endings are normalized as follows:
- `CRLF` → `LF`
- `CR` → `LF`

### Output Hexadecimal Case
- `lowercase`
- `UPPERCASE`

Hexadecimal case conversion applies to hexadecimal result data and does not alter Base64 or Base85 alphabets.

### Display Grouping
Available grouping sizes:
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

### Custom Separator
- Any text string may be used as the grouping separator.
- An empty separator is supported.
- Grouping is presentation-only and does not alter the stored raw result.

---

## 2. Hash and Checksum Algorithms

Supported algorithms:
- CRC32
- CRC64
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512

### CRC64 Variants
The CRC64 result can be switched between:
1. `XZ (ECMA, Reflected)`
2. `WE (ECMA, Non-Reflected)`
3. `ISO (HDLC, Non-Reflected)`
4. `GO-ISO (ISO, Reflected)`
5. `Redis (Jones, Reflected)`

All CRC64 variants are calculated and cached during applicable operations so the displayed variant can be changed without requiring a complete recalculation when cached data is available.

### Web Cryptography API Dependency
SHA-384 and SHA-512 depend on the availability of `window.crypto.subtle`.

If the required API is unavailable:
- A restricted-operation warning is displayed.
- SHA-384 is disabled in the interface.
- SHA-512 is disabled in the interface.
- Audit output records the unavailable state.

---

## 3. Salt

- Optional UTF-8 salt input.
- Salt is appended to the input data before hashing.
- Salt is available for text hashing and file hashing.
- Disabling the Salt option clears the configured salt.
- Salt state is included in the audit report.

For iterative XOR mode, the salt is applied to the first iteration rather than being appended again on every subsequent XOR iteration.

---

## 4. HMAC

Optional HMAC key input is encoded as UTF-8.

HMAC is supported for:
- HMAC-MD5
- HMAC-SHA1
- HMAC-SHA224
- HMAC-SHA256
- HMAC-SHA384
- HMAC-SHA512

HMAC is not applied to:
- CRC32
- CRC64
- Base64
- Base85

When HMAC is active, the corresponding hash labels change to HMAC labels.

Disabling the HMAC option clears the configured HMAC key.

---

## 5. Iterative Hashing

### Iteration Count
- Minimum: `1`
- Maximum UI value: `4294967295`
- Increment control
- Decrement control
- Direct numeric entry

Loaded files are processed with a fixed iteration count of `1`.

### Iteration Modes
1. `Standard (Raw)`
2. `Hex Handoff (Hex)`
3. `XOR Overlay (XOR)`

### Standard (Raw)
The binary digest from the previous iteration becomes the input to the next iteration.

### Hex Handoff (Hex)
The hexadecimal digest text from the previous iteration is UTF-8 encoded and becomes the input to the next iteration.

### XOR Overlay (XOR)
- Each iteration hashes the previous binary digest.
- Successive digest values are XOR-accumulated.
- The accumulated XOR value is displayed as hexadecimal output.
- Salt is applied on the first iteration.
- CRC64 applies the same XOR accumulation independently to each CRC64 variant.

### Cancellation
For iterative calculations:
- Each hash algorithm can be stopped individually.
- All active hash calculations can be stopped simultaneously.
- Worker-side cancellation is propagated to active algorithm tasks.

---

## 6. Base64

### Encoding
- Standard RFC 4648 Base64 alphabet:
  `A-Z a-z 0-9 + /`
- Standard `=` padding is generated.
- Text input is UTF-8 encoded before Base64 encoding.
- File input can be Base64 encoded.
- Large-file Base64 output supports streaming.

### Decoding
Base64 Decode supports two validation modes.

#### Lenient Validation
- Removes whitespace.
- Accepts standard Base64 characters.
- Accepts URL-safe `-` and `_`.
- Converts:
  - `-` → `+`
  - `_` → `/`
- Automatically adds missing `=` padding until the encoded length is a multiple of 4.
- Rejects invalid characters.
- Rejects invalid padding placement.

#### Strict Validation
Strict mode accepts standard RFC 4648 Base64 only.

Validation includes:
- Whitespace is prohibited.
- URL-safe `-` and `_` are prohibited.
- Only `A-Z`, `a-z`, `0-9`, `+`, `/`, and `=` are accepted.
- Encoded length must be an exact multiple of 4.
- Padding is limited to zero, one, or two trailing `=` characters.
- Padding placement is validated.
- Non-zero pad bits are rejected.

### Decoded Output
Decoded data can be returned as:
- UTF-8 text
- Binary data

When automatic file detection is disabled, decoded bytes are converted to text with the browser UTF-8 decoder.

When automatic file detection is enabled:
- Valid UTF-8 text is returned as text.
- Data identified as binary is retained as bytes and can be downloaded with a detected extension and MIME type.

### Text-Encoding Limit
For text input:
- Base64 Encode input limit: `50,000,000` bytes.

---

## 7. Base85

### Supported Variants
1. `Standard ASCII85`
2. `Adobe ASCII85`
3. `ZeroMQ (Z85)`
4. `RFC 1924`

The active variant can be cycled from the Base85 result label.

### Standard ASCII85
- Character range corresponds to ASCII characters `!` through `u`.
- Supports `z` shorthand for a four-byte all-zero block.
- Supports `y` shorthand for four ASCII space bytes.
- Supports partial final input blocks.

### Adobe ASCII85
- Uses the same base alphabet as Standard ASCII85.
- Encoded output is wrapped with:
  - Prefix: `<~`
  - Suffix: `~>`
- Decoder accepts wrapped Adobe input.
- Supports `z`.
- Supports `y`.
- Supports partial final input blocks.

### ZeroMQ (Z85)
- Uses the Z85 alphabet.
- Encoding input length must be a multiple of 4 bytes.
- Decoding input length must be a multiple of 5 characters.

### RFC 1924
- Uses the RFC 1924 Base85 alphabet.
- Encoding input length must be a multiple of 4 bytes.
- Decoding input length must be a multiple of 5 characters.

### Base85 Decode Validation
- Whitespace is removed before decoding.
- Characters are validated against the selected Base85 alphabet.
- Standard ASCII85 and Adobe ASCII85 accept partial final encoded blocks.
- A final encoded block containing only one character is invalid.
- ZeroMQ Z85 and RFC 1924 require exact 5-character encoded blocks.
- Values exceeding the unsigned 32-bit range are rejected.

### Base85 Variant Detection
Comparison operations can test candidate encodings against:
- Standard ASCII85
- Adobe ASCII85
- ZeroMQ Z85
- RFC 1924

Adobe `<~ ... ~>` framing directly identifies the Adobe variant.

### Text-Encoding Limit
For text input:
- Base85 Encode input limit: `50,000,000` bytes.

---

## 8. Automatic File-Type Detection for Decoded Base Data

Automatic file detection can be enabled for:
- Base64 Decode
- Base85 Decode

If decoded data is not valid text, the decoder can preserve the binary data and associate it with a detected file extension and MIME type.

Unknown binary data uses:
- Extension: `.bin`
- MIME type: `application/octet-stream`

---

## 9. Recognized Binary Output Types

### Images

| Extension | MIME Type | Detection |
|---|---|---|
| `.png` | `image/png` | PNG signature |
| `.jpg` | `image/jpeg` | JPEG signature |
| `.gif` | `image/gif` | GIF signature |
| `.bmp` | `image/bmp` | BMP signature |
| `.tif` | `image/tiff` | Little-endian TIFF signature |
| `.tif` | `image/tiff` | Big-endian TIFF signature |
| `.ico` | `image/x-icon` | ICO signature |
| `.cur` | `image/x-icon` | CUR signature |
| `.jp2` | `image/jp2` | JPEG 2000 signature |
| `.psd` | `image/vnd.adobe.photoshop` | Photoshop signature |
| `.dds` | `image/vnd.ms-dds` | DDS signature |
| `.exr` | `image/x-exr` | OpenEXR signature |
| `.webp` | `image/webp` | RIFF/WEBP container |
| `.heic` | `image/heic` | BMFF brands `heic`, `heix`, `mif1` |
| `.avif` | `image/avif` | BMFF brands `avif`, `avis` |
| `.svg` | `image/svg+xml` | XML/SVG text signature |

### Audio

| Extension | MIME Type | Detection |
|---|---|---|
| `.mp3` | `audio/mpeg` | ID3 / MPEG audio frame signatures |
| `.aac` | `audio/aac` | AAC frame signatures |
| `.ogg` | `audio/ogg` | Ogg signature |
| `.flac` | `audio/flac` | FLAC signature |
| `.mid` | `audio/midi` | MIDI signature |
| `.dsf` | `audio/x-dsf` | DSF signature |
| `.dff` | `audio/x-dff` | DSDIFF container |
| `.tta` | `audio/x-tta` | TTA signature |
| `.ape` | `audio/ape` | Monkey's Audio signature |
| `.wv` | `audio/wavpack` | WavPack signature |
| `.wma` | `audio/x-ms-wma` | ASF/WMA signature |
| `.wav` | `audio/wav` | RIFF/WAVE container |
| `.aiff` | `audio/x-aiff` | AIFF / AIFC container |
| `.m4a` | `audio/mp4` | BMFF brand `M4A` |

### Video

| Extension | MIME Type | Detection |
|---|---|---|
| `.mkv` | `video/x-matroska` | EBML / Matroska signature |
| `.webm` | `video/webm` | EBML signature with WebM identifier |
| `.mpg` | `video/mpeg` | MPEG program/video signatures |
| `.mp4` | `video/mp4` | BMFF brands `mp41`, `mp42`, `isom`; unknown `ftyp` defaults to MP4 |
| `.m4v` | `video/mp4` | BMFF brand `M4V` |
| `.mov` | `video/quicktime` | BMFF brand `qt` |
| `.avi` | `video/x-msvideo` | RIFF/AVI container |

### Archives

| Extension | MIME Type |
|---|---|
| `.zip` | `application/zip` |
| `.rar` | `application/x-rar-compressed` |
| `.7z` | `application/x-7z-compressed` |
| `.gz` | `application/gzip` |
| `.bz2` | `application/x-bzip2` |
| `.tar` | `application/x-tar` |

### Documents, Executables, Disk Images, and Other Types

| Extension | MIME Type |
|---|---|
| `.pdf` | `application/pdf` |
| `.iso` | `application/x-iso9660-image` |
| `.exe` | `application/x-msdownload` |
| `.cab` | `application/vnd.ms-cab-compressed` |
| `.msi` | `application/x-msi` |
| `.lnk` | `application/x-ms-shortcut` |
| `.chm` | `application/vnd.ms-htmlhelp` |
| `.vhdx` | `application/x-vhd` |
| `.dwg` | `image/vnd.dwg` |
| `.blend` | `application/x-blender` |
| `.txt` | `text/plain; charset=utf-16le` |
| `.txt` | `text/plain; charset=utf-16be` |
| `.bin` | `application/octet-stream` |

---

## 10. File-Structure Analysis

Loaded files are subjected to structural inspection before processing.

Recognized structural classifications include:
- Windows PE executable (`EXE` / `DLL`)
- MS-DOS executable
- Unverified PE-style executable
- MP3 with ID3v2 metadata
- SQLite 3 database
- ZIP archive
- Verified ZIP archive
- JPEG image
- PNG image
- Generic binary data

### ZIP Tail Verification
The end of the file is inspected for:
- ZIP End of Central Directory
- ZIP64 locator-related signature

### Polyglot Detection
If a ZIP end structure is found in a file whose leading structure is not ZIP, the file is flagged as containing an embedded multi-format / polyglot structure.

### Scan Windows
- Initial structural scan: up to `8192` bytes from the file start.
- Tail structural scan: up to `65536` bytes from the file end.

---

## 11. Match and Verification Mode

A comparison value can be entered and tested against current results.

### Hash Comparison
Supported:
- CRC32
- CRC64
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512

Hash comparison normalization removes:
- Whitespace
- Colon characters
- Hyphen characters

Hash comparison is case-insensitive.

### CRC64 Variant Identification
For a 16-hex-character input, the utility can identify:
- One matching CRC64 variant
- Multiple matching CRC64 variant candidates

### Base Comparison
Supported:
- Base64 Encoded output
- Base64 Decoded output
- Base85 Encoded output
- Base85 Decoded output

Base output comparison is exact after trimming the outer comparison input.

### Base85 Variant Inference
If no direct match is found and the candidate contains non-hexadecimal characters, Base85 variant inference may still be attempted.

### Comparison Metrics
Comparison notifications can include:
- Grapheme count
- Shannon entropy for hexadecimal byte data
- Shannon entropy for the entered Base-form text

---

## 12. Built-In Test Vectors

Available test inputs:
- Empty string
- `abc`
- `123456789`

### Apply
Applying a test vector resets applicable configuration before loading the selected test input, including:
- Salt
- HMAC
- Iteration count
- Iteration mode

### Verify
Built-in verification covers:
- CRC32
- Selected CRC64 variant
- MD5
- SHA-1
- SHA-224
- SHA-256
- SHA-384
- SHA-512

Verification can report individual self-test failures.

---

## 13. Startup Input Preset

If the main input field is empty during application initialization:
- A fixed preset text matrix is inserted automatically.
- An input event is dispatched so normal processing begins against that preset.

---

## 14. Result Presentation and Interaction

### Result Rendering
Each result can maintain:
- Raw result data
- Formatted display data
- Progress status
- Binary-result metadata
- Large-text result metadata

### Copy
For normal text results:
- Clicking the result copies the raw result.
- `Enter` activates the focused result.
- `Space` activates the focused result.

Clipboard behavior:
- Secure context with Clipboard API: `navigator.clipboard.writeText`.
- Fallback: temporary read-only textarea and legacy copy command.

### Binary Result
Clicking a binary decoded result downloads the binary content instead of copying text.

### Large Text Result
Clicking a large text result downloads it as a text file instead of copying the complete value.

---

## 15. Export and Download Formats

### Markdown Audit Report

Format:
- Description: `Markdown Report`
- Extension: `.md`
- MIME type: `text/markdown`

Filename pattern:
- `sony-hash-audit-YYYYMMDD-HHMMSS.md`

Timestamp basis:
- Japan Standard Time
- Network-time offset is used when available

The report can include:
- Generation timestamp
- Isolation-mode state
- Storage-isolation warning
- Web Cryptography API warning
- System information
- Network information
- Location information
- Overall processing status
- UI metadata
- Character encoding
- Normalization state
- Sanitization state
- Hexadecimal case
- Grouping size
- Custom separator
- Target iteration count
- Iteration mode
- Salt value
- HMAC key value
- CRC64 variant
- Base64 validation mode
- Base85 variant
- Automatic Base file-detection state
- Input type
- Input filename
- Input size
- Input MIME type
- Text grapheme count
- Input text content
- Formatted result table
- Raw result table
- Per-result progress status

For text input:
- Audit-report input-content inclusion is limited to the first `100000` source characters.
- Longer input is truncated in the report with a truncation notice.

Binary results are represented by a binary-data size marker rather than embedded binary content.

Very large text results are excluded from inline report content and represented by an exclusion marker.

### Audit Report Save Path
If File System Access is available and isolation mode is not active:
- `showSaveFilePicker()` is used.

Otherwise:
- Browser download via object URL is used.

### Direct Large-File Base64 Output
Suggested filename:
- `base64_out.txt`

### Direct Large-File Base85 Output
Suggested filename:
- `base85_out.txt`

### Large Text Result Download
Filename pattern:
- `SONY_HASH_<result-element-id>.txt`

Examples:
- `SONY_HASH_Base64-Enc.txt`
- `SONY_HASH_Base85-Enc.txt`

MIME type:
- `text/plain`

### Binary Decoded Output
Filename pattern:
- `decoded_YYYYMMDD_HHMMSS.<detected-extension>`

MIME type:
- Determined from the detected binary file type.
- Unknown data uses `application/octet-stream`.

---

## 16. File Size and Streaming Behavior

### Small-File Threshold
A file is treated as a small file when:
- File size is `10 MiB` or less.

Small-file processing:
- Reads the entire file into memory.
- Supports CRC32.
- Supports all CRC64 variants.
- Supports MD5.
- Supports SHA-1.
- Supports SHA-224.
- Supports SHA-256.
- Supports SHA-384.
- Supports SHA-512.
- Supports Base64 Encode when enabled.
- Supports Base85 Encode when enabled.

### Large-File Path
For files larger than `10 MiB`:
- Uses `file.stream()` when available.
- Uses worker-backed incremental hashing.
- Supports incremental CRC32.
- Supports incremental CRC64.
- Supports incremental MD5.
- Supports incremental SHA-1.
- Supports incremental SHA-224.
- Supports incremental SHA-256.
- SHA-384 is reported as limited to files of `10 MiB` or less.
- SHA-512 is reported as limited to files of `10 MiB` or less.
- Base64 encoding can be streamed.
- Base85 encoding can be streamed.
- Data is aligned in streaming blocks compatible with both Base64 and Base85 processing.
- Processing periodically yields after approximately `50 MiB` of streamed input to avoid uninterrupted main-task occupancy.

### Z85 / RFC 1924 Large-File Constraint
For ZeroMQ Z85 or RFC 1924 Base85:
- Entire file size must be divisible by 4.
- Otherwise Base85 encoding is rejected before streaming output begins.

### Adobe ASCII85 Streaming
When Adobe ASCII85 is streamed:
- `<~` is written before encoded data.
- `~>` is written after encoded data.

### Browser File-Size Limits
When File System Access streaming output is available and isolation mode is not active:
- Normal file-loading threshold: up to `300 MiB`.
- Files above `300 MiB` are retained as the current file and require explicit direct-output initiation.

When direct stream-save support is unavailable:
- File input is limited to `50 MiB`.

### Direct-to-Disk Output
For oversized files in a supported environment:
- Base64 output can be written directly to a selected file.
- Base85 output can be written directly to a selected file.
- Output streams are closed when processing finishes.

### In-Memory Large Encoded Output
When no direct write handle is active:
- Large Base64 encoded data is assembled into a text blob.
- Large Base85 encoded data is assembled into a text blob.
- The result can then be downloaded as a text file.

---

## 17. Worker Processing

Dedicated Web Workers are used for:
- Hash calculations
- Base64 operations
- Base85 operations

Hash worker threshold constant:
- `200000` bytes

Worker functionality includes:
- Iterative hashing
- Incremental large-file hashing
- Hash self-test verification
- Per-algorithm abort signaling
- Worker termination and recreation after failures
- Transferable ArrayBuffer use for selected data paths

Workers are terminated during page unload.

---

## 18. Themes and Display

Available themes:
1. `DEFAULT`
2. `Command Prompt`
3. `PowerShell`

Theme selection:
- Cycles through the available themes.
- Is persisted in `localStorage` under the application theme setting.
- Uses the View Transitions API when available.
- Falls back to immediate theme switching when View Transitions are unavailable.

### Fullscreen
- Enter fullscreen.
- Exit fullscreen.
- Supports standard and WebKit-prefixed fullscreen APIs.
- The control label reflects the current fullscreen state.

### Responsive Presentation
- Single-column result layout on smaller viewports.
- Two-column result layout on larger viewports.
- System-panel spacing is recalculated when the viewport changes.

---

## 19. System and Environment Diagnostics

The system panel can report, when browser APIs permit:
- Platform
- Operating-system version
- CPU architecture
- Platform bitness
- Browser language
- Time zone
- Desktop / mobile classification
- Maximum touch-point count
- Logical processor count
- Estimated device memory
- GPU renderer
- Screen width and height
- Device pixel ratio
- Screen color depth
- HDR / SDR capability
- Viewport dimensions
- Browser brand and version
- Browser vendor fallback
- Operating-system dark / light preference
- Battery percentage
- Charging state

### GPU Detection
GPU information is attempted through:
1. WebGPU adapter information
2. WebGL / WebGL2 debug renderer information
3. Browser capability/status fallback

### User-Agent Client Hints
When high-entropy client hints are available, the utility requests:
- OS version
- Architecture
- Bitness
- Device model

---

## 20. Network Diagnostics

### Connection Information
When supported by the browser Network Information API:
- Effective connection type
- Estimated downstream bandwidth in Mbps
- Estimated RTT in milliseconds

### Public IP
- Public IP lookup uses an external IP service.
- IPv4 and IPv6 are distinguished.
- Public-IP results are cached for approximately `120000` ms.
- Public-IP requests use a `3000` ms timeout.
- Offline state is reported.
- Public-IP lookup is blocked in zero-egress isolation mode.

### Network Time
- Network time is synchronized against the Asia/Tokyo time endpoint when network access is available.
- Timeout: `3000` ms.
- The calculated offset is used for displayed time and audit-report timestamps.
- The offset returns to zero on failure or offline state.

### Online / Offline Events
When network connectivity changes:
- Network time is resynchronized after reconnection.
- Public-IP cache is reset after reconnection.
- Geolocation cache is invalidated after reconnection.
- Offline state is reflected in the system panel.

---

## 21. Location Information

Location lookup is optional and disabled by default unless a previously persisted setting enables it.

### Lookup Method
The implementation uses IP-derived location services rather than browser GPS/geolocation permission.

Provider fallback sequence:
1. `ipapi.co`
2. `ipwho.is`
3. `ipinfo.io`
4. Cloudflare trace
5. `ip-api.com`

### Japan-Specific Formatting
For Japanese IP results, location output can include:
- Country
- Prefecture
- Municipality / ward
- Postal code
- ISP / organization

The implementation contains:
- Japanese prefecture name mapping
- Prefecture-code mapping
- Tokyo ward mapping
- Major-city name mapping
- Japanese postal-code formatting

### Non-Japan Results
A successfully detected non-Japanese country is reported as outside the configured Japan-specific scope.

### Cache
Location result cache duration:
- `12 hours`

### Isolation Mode
Location lookup is disabled while zero-egress isolation mode is active.

---

## 22. Zero-Egress Stateless Isolation Mode

### Activation
Isolation mode is enabled when the page is loaded with:
- Query parameter: `?U-571=1`
- Fragment: `#U-571`

The interface can also toggle the isolation state by reloading the page with or without the activation parameter.

### Content Security Policy
In isolation mode:
- `connect-src 'none'`
- `object-src 'none'`
- `base-uri 'none'`
- `frame-ancestors 'none'`
- Scripts, styles, and workers remain restricted to the configured local/inline/blob policy.

### Network Isolation
While active:
- `fetch` is replaced with a blocked implementation.
- `XMLHttpRequest` is replaced with a blocked implementation.
- Public-IP lookup is disabled.
- Location lookup is disabled.
- Network-time synchronization is disabled.

### Storage Isolation
The implementation attempts to replace:
- `localStorage`
- `sessionStorage`

with an in-memory storage proxy.

The isolation state is checked separately for both storage mechanisms.

The audit report can indicate:
- Complete storage isolation
- `localStorage` isolation failure
- `sessionStorage` isolation failure
- Combined incomplete storage isolation

### Persistent-State Cleanup
Isolation initialization attempts to:
- Unregister service workers.
- Delete Cache Storage entries.
- Enumerate and delete IndexedDB databases when enumeration is supported.

### File System Access
Direct File System Access save operations are not used while isolation mode is active.

---

## 23. Dynamic DOM Protection

A `MutationObserver` monitors newly inserted DOM elements.

Automatically removed element types:
- `IFRAME`
- `OBJECT`
- `EMBED`

Additional dynamically inserted elements may be removed when:
- Class name contains `ad-`
- Element ID contains `ads`
- Computed or inline `z-index` exceeds `1945`

Explicit exceptions include:
- Download anchors
- Fixed read-only textareas used by the copy fallback
- Script, style, and meta elements

---

## 24. Emergency Termination Mechanism

### Keyboard Triggers
On non-Apple platforms:
- `Ctrl + Shift + I`
- `Ctrl + Shift + J`
- `Ctrl + Shift + C`
- `F12`
- Meta / Windows key

On Apple platforms:
- `Command + Option + I`
- `Command + Option + J`
- `Command + Option + C`
- `F12`

### Escape Sequence
- Four qualifying `Escape` key presses trigger termination.
- The sequence resets after `500` ms.
- Extremely rapid repeated events within `50` ms are not counted as separate qualifying presses.

### Touch Trigger
- Four or more simultaneous touch points trigger termination.

### Termination Behavior
When triggered:
- Current page loading is stopped.
- Active hash processing is reset.
- Hash worker is terminated.
- Base64 worker is terminated.
- Base85 worker is terminated.
- Document content is replaced.
- In normal mode, navigation proceeds to Google.
- In zero-egress isolation mode, navigation proceeds to `about:blank`.

---

## 25. Audit and Runtime State Reporting

The application tracks and can expose operational state including:
- Current processing status
- Current CRC64 variant
- Current Base85 variant
- Current Base64 validation mode
- Salt state
- HMAC state
- Iteration mode
- Iteration target
- Input type
- File size
- Text grapheme count
- Shannon entropy
- Character encoding
- Sanitization state
- Normalization state
- Hexadecimal case
- Grouping state
- Automatic file detection state
- Network state
- Location state
- Battery state
- Isolation state
- Storage-isolation completeness
- Web Cryptography API availability

The system panel is refreshed once per second.





https://d55681000.github.io/HASH-Generator/

https://hash-generatorg.d55681000.workers.dev/





"This is a browser-based hash calculator made purely for fun. Honestly, I'm not quite sure how to describe it, so please treat it as something fun rather than a practical tool. Any brand names mentioned are just for laughs—none of it is real, and this project was created solely for my own personal satisfaction."


These tasks were completed by Gemini V3.1 Pro and ChatGPT 5.6 Sol together. I was just responsible for directing the work, copying and pasting things, and putting all the results together.
