# G98 verified boot artifacts

These two files are intentionally kept separate in the G98 image:

- `bdy-g98-istoreos-2024.10-idbloader.img` is the loader read back from a
  working G98 and is written at sector 64 (32 KiB).
- `BDY-G98-u-boot-2024.10-full-v21.itb` is the hardware-verified U-Boot FIT
  and is written at sector 16384 (8 MiB).

The generated `u-boot-rockchip.bin` remains a diagnostic build artifact. It
is not embedded in the G98 image because replacing the verified SPL/DDR
loader would invalidate the tested boot chain.
