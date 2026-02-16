# VCU2 Quick Reference Guide

## Essential Information at a Glance

This quick reference provides the most commonly needed information about the VCU2 IP in an easy-to-find format. For detailed explanations of any topic, please refer to the main README document.

---

## Device Compatibility

The VCU2 IP is integrated into AMD Versal AI Edge Series Gen 2 and Versal Prime Series Gen 2 devices. It is implemented as hard IP, meaning it is physically built into the silicon rather than being configured in programmable logic.

---

## Supported Video Standards

**Encoding Standards**
- H.264/AVC (Advanced Video Coding)
- H.265/HEVC (High Efficiency Video Coding)

**Decoding Standards**
- H.264/AVC
- H.265/HEVC  
- JPEG (still images and Motion JPEG)

---

## Performance Capabilities

**Encoder Maximum Performance**
- Resolution: Up to 8192 × 8192 pixels (8K) at approximately 15 frames per second
- 4K UHD (3840 × 2160): 60 frames per second at full quality
- 4K DCI (4096 × 2160): 60 frames per second (requires 950 MHz core clock)

**Decoder Maximum Performance**
- Resolution: Up to 4096 × 4096 pixels at 60 frames per second
- 4K UHD (3840 × 2160): 60 frames per second at full quality
- 4K DCI (4096 × 2160): 60 frames per second (requires 918 MHz core clock)

**Concurrent Stream Capability**
- Maximum encoding streams: 32 simultaneous streams
- Maximum decoding streams: 32 simultaneous streams
- Aggregate bandwidth limitation: Equivalent to 3840 × 2160 at 60 frames per second across all streams combined

---

## Supported Input Formats (for Encoding)

**Color Formats**
- YUV420 (I420, NV12) - Semi-planar format, 12 bits per pixel
- YUV422 (I422, NV16) - Semi-planar format, 16 bits per pixel  
- YUV444 (I444, NV24) - Planar format, 24 bits per pixel
- Y400 - Monochrome only, 8 bits per pixel

**Bit Depths**
- 8-bit per color component (standard consumer video)
- 10-bit per color component (high-end consumer and professional)
- 12-bit per color component (cinema and high-end professional)

---

## Supported Output Formats

**Encoder Output**
- H.264 bitstream (.h264, .264) in Annex-B format with NAL units
- H.265 bitstream (.h265, .265, .hevc) in Annex-B format with NAL units

**Decoder Output**
- YUV420 (typically NV12 semi-planar)
- YUV422 (NV16 semi-planar)
- YUV444 (NV24 planar)
- Bit depth matches input (8-bit, 10-bit, or 12-bit)

---

## H.264 Profile Support

**Encoder and Decoder Both Support**
- Baseline Profile
- Constrained Baseline Profile
- Main Profile
- High Profile
- High 10 Profile (with 10-bit color depth)
- High 4:2:2 Profile (with enhanced color sampling)
- High 4:4:4 Profile (with full color information)
- Intra-only variants of all profiles

**Maximum Level**: 5.2 (with partial support for level 6.0 for 8K encoding)

---

## H.265 Profile Support

**Encoder and Decoder Both Support**
- Main Profile
- Main 10 Profile (10-bit color depth)
- Main 12 Profile (12-bit color depth)
- Main 4:2:2 10-bit Profile
- Main 4:2:2 12-bit Profile
- Main 4:4:4 10-bit Profile
- Main 4:4:4 12-bit Profile
- Monochrome 10-bit Profile
- Monochrome 12-bit Profile
- Intra-only variants of all profiles

**Maximum Level**: 5.1 High Tier (with partial support for level 6.0 for 8K encoding)

**Interlaced Support**: Decoder supports Sequence-Adaptive Field-Frame (SAFF) format only. Encoder does not support interlaced output.

---

## Rate Control Modes

**Constant Bitrate (CBR)**
Purpose: Maintains consistent bitrate throughout the video, ideal for streaming applications where network bandwidth is limited or predictable data rates are required.

**Variable Bitrate (VBR)**
Purpose: Allows bitrate to fluctuate based on scene complexity, providing better overall quality for a given average bitrate. Complex scenes use more bits, simple scenes use fewer bits.

**Constant QP (Quantization Parameter)**
Purpose: Maintains consistent quality level rather than consistent bitrate. Useful when quality consistency is more important than file size predictability.

---

## Memory Interface Specifications

**AXI Interface Configuration**
- Interface Width: 128 bits per channel
- Number of Independent Channels: Four total (two for encoder subsystem, two for decoder subsystem)
- Connection: All channels connect through Network-on-Chip (NoC) to system memory
- Supported Memory Types: DDR4, DDR5

**Specific Interface Assignments**
- C0_ENC_M_AXI_NOC: Encoder data path (128-bit master interface)
- C0_ENC_MCU_M_AXI_NOC: Encoder microcontroller control path (128-bit master interface)
- C0_DEC_M_AXI_NOC: Decoder data path (128-bit master interface)
- C0_DEC_MCU_M_AXI_NOC: Decoder microcontroller control path (128-bit master interface)

---

## Control Architecture

**Microcontroller Details**
- Architecture: RISC-V 64-bit processors
- Quantity: Two independent microcontrollers (one dedicated to encoder, one to decoder)
- Firmware: Pre-loaded and non-modifiable by users
- Function: Handles all detailed control of encoding and decoding processes based on task lists provided by application software

**Software Control Options**
- VCU2 Control Software Library: Provides C/C++ API for direct control
- GStreamer Framework: Provides multimedia pipeline integration through omxh264enc, omxh265enc, omxh264dec, and omxh265dec plugins

---

## Common GStreamer Pipeline Examples

**Basic Encoding Example (YUV to H.264)**
```bash
gst-launch-1.0 filesrc location=input.yuv ! rawvideoparse width=1920 height=1080 format=i420 framerate=30/1 ! omxh264enc target-bitrate=5000000 ! filesink location=output.h264
```

**Basic Decoding Example (H.264 to YUV)**
```bash
gst-launch-1.0 filesrc location=input.h264 ! h264parse ! omxh264dec ! filesink location=output.yuv
```

**H.265 Encoding Example**
```bash
gst-launch-1.0 filesrc location=input.yuv ! rawvideoparse width=3840 height=2160 format=i420 framerate=60/1 ! omxh265enc target-bitrate=15000000 ! filesink location=output.h265
```

**H.265 Decoding Example**
```bash
gst-launch-1.0 filesrc location=input.h265 ! h265parse ! omxh265dec ! filesink location=output.yuv
```

---

## Typical Bitrate Recommendations

These are general guidelines. Actual optimal bitrates depend on content complexity, desired quality, and application requirements.

**H.264 Encoding Bitrates**
- 720p (1280×720) @ 30 fps: 2-4 Mbps
- 1080p (1920×1080) @ 30 fps: 4-8 Mbps
- 1080p (1920×1080) @ 60 fps: 8-12 Mbps
- 4K UHD (3840×2160) @ 30 fps: 15-25 Mbps
- 4K UHD (3840×2160) @ 60 fps: 25-40 Mbps

**H.265 Encoding Bitrates** (approximately half of H.264 for equivalent quality)
- 720p @ 30 fps: 1-2 Mbps
- 1080p @ 30 fps: 2-4 Mbps
- 1080p @ 60 fps: 4-6 Mbps
- 4K UHD @ 30 fps: 8-15 Mbps
- 4K UHD @ 60 fps: 12-20 Mbps

---

## Common Applications

**Video Surveillance Systems**
- Multiple camera stream encoding
- Long-term storage with H.265 efficiency
- Real-time monitoring with low latency
- Motion detection integration

**Broadcasting and Professional Video**
- Live encoding for broadcast transmission
- Multi-bitrate encoding for adaptive streaming
- Professional color space support (4:2:2, 4:4:4, 10-bit)
- Archive and distribution encoding

**Video Conferencing**
- Simultaneous encode and decode
- Low-latency configuration
- Adaptive bitrate based on network conditions
- Multiple participant stream handling

**Embedded Vision and Automotive**
- Sensor data compression for autonomous vehicles
- Multi-camera systems
- Real-time processing requirements
- Power-efficient operation

**Medical Imaging**
- High bit-depth support (10-bit, 12-bit)
- Quality preservation for diagnostic purposes
- Compliance with medical imaging standards

---

## Power and Thermal Considerations

The VCU2 hard IP implementation provides significant power advantages compared to software-based video encoding and decoding. Typical power consumption depends on utilization level, operating frequency, and resolution being processed. The dedicated hardware design achieves better performance per watt than general-purpose CPU processing.

For thermal management, ensure adequate cooling for your Versal device when running VCU2 at full capacity, especially when processing 4K or 8K streams. The VEK385 evaluation board includes appropriate thermal solutions for development and testing.

---

## Key Features Not Supported

Understanding limitations helps avoid design issues:

**H.264 Limitations**
- Encoder: No lossless mode (transform bypass)
- Decoder: No support for Flexible Macroblock Ordering (FMO), Arbitrary Slice Ordering (ASO), or Redundant Slices (RS)
- Both: No interlaced video support

**H.265 Limitations**
- Encoder: No Asymmetric Motion Partition (AMP), no lossless mode, no transform skip mode, no Wavefront Parallel Processing (WPP)
- Decoder: Only SAFF format supported for interlaced video
- Both: No Multiview (MV-HEVC), no Scalable Video Coding

**JPEG Limitations**
- Encode not supported (decode only)
- Supported bit depths: 8-bit and 12-bit only (no 10-bit JPEG)

---

## Development Board Information

**VEK385 Evaluation Board**
- Device: Versal AI Edge Gen 2
- Includes: VCU2 hard IP, memory interfaces, connectivity options
- Primary Use: Development, prototyping, and validation
- Available From: AMD/Xilinx and authorized distributors

---

## Software Development Environment

**Required Tools**
- AMD Vivado Design Suite (for hardware design and bitstream generation)
- Yocto Project or AMD PetaLinux (for Linux system build)
- VCU2 drivers and Control Software library (included in Yocto/PetaLinux)
- GStreamer (for multimedia application development)

**Optional Tools**
- Video analysis tools (for quality measurement: PSNR, SSIM)
- Bitstream analyzers (for debugging encoded streams)
- Performance monitoring tools (for throughput and latency analysis)

---

## Validation and Testing Recommendations

**Functional Testing**
Test all codec types (H.264, H.265, JPEG), multiple resolutions, various bitrates and GOP structures, different profile and level combinations, and both encoding and decoding paths.

**Performance Testing**
Measure actual frame rates achieved, encoding/decoding latency, CPU utilization to verify hardware acceleration benefits, power consumption, and thermal behavior under sustained operation.

**Quality Testing**
Compare encoded output quality against reference encoders using objective metrics (PSNR, SSIM, VMAF), perform subjective quality assessment, verify compliance with codec standards, and test with diverse content types.

**Stress Testing**
Run multiple concurrent streams, execute long-duration continuous operation (hours to days), test dynamic parameter changes during operation, and verify error recovery mechanisms.

---

## Troubleshooting Quick Tips

**If encoding fails to start:**
Check that input format specifications match actual file format, verify sufficient memory is available, confirm correct codec and profile selection, and ensure proper VCU2 driver initialization.

**If performance is lower than expected:**
Verify system clock frequencies meet requirements for target resolution, check for memory bandwidth bottlenecks, review rate control settings and GOP structure, and ensure no thermal throttling is occurring.

**If output quality is poor:**
Increase target bitrate, adjust rate control parameters, modify GOP structure for more I-frames, and verify input video quality is adequate.

**If system is unstable:**
Check for sufficient cooling and proper power supply, verify correct driver and firmware versions, review memory allocation and buffer sizes, and examine system logs for error messages.

---

## Where to Find More Information

For comprehensive explanations of concepts, detailed architecture information, in-depth workflow descriptions, application examples, and troubleshooting guidance, please refer to the main VCU2_README.md document.

For official AMD documentation, consult PG447 Product Guide, Versal Data Sheets (DS1020, DS1021), Vivado Design Suite documentation, and Yocto Project resources.

---

*This quick reference is based on VCU2 IP v3.0 specifications as of November 2025*
