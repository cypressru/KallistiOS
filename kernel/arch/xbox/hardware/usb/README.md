# Xbox USB support

The first Xbox USB implementation milestone supports the two standard
Microsoft gamepad layouts:

- Duke controllers (XID type `0x01`, subtype `0x01`)
- Controller S controllers (XID type `0x01`, subtype `0x02`)

The controller interface preserves the original hardware's complete input
reports: digital D-pad/Start/Back/stick clicks, 8-bit pressure-sensitive
A/B/X/Y/Black/White buttons, 8-bit triggers, signed 16-bit stick axes, and
the two rumble motors.

The USB core currently provides device enumeration, control and interrupt
transfers, root and external hub support, and disconnect/reconnect handling.
Unsupported devices remain visible as generic USB devices and must not be
claimed by the standard-gamepad driver.

## Remaining Xbox hardware

The following hardware needs dedicated support before the Xbox USB subsystem
can be considered complete:

- Duke hardware validation and simultaneous multi-controller validation
- Xbox Memory Units and compatible storage:
  - OHCI bulk transfers
  - USB mass-storage transport
  - KOS block-device integration
  - FATX filesystem support
- Xbox Live Communicator:
  - OHCI isochronous transfers
  - microphone and earpiece streaming
  - a KOS audio/voice-facing API
- Xbox DVD Movie Playback Kit IR receiver (XID type `0x03`)
- Steering wheels (XID type `0x01`, subtype `0x10`)
- Arcade sticks and compatible dance pads (commonly XID subtype `0x20`)
- Light guns (XID type `0x01`, subtype `0x50`), including calibration,
  feedback, and coordination with the Xbox controller-port VBlank signal
- Steel Battalion controller (XID type `0x80`), whose input and output
  reports require a dedicated API
- Karaoke microphones and other title-specific USB accessories

Optional generic USB support includes HID keyboards and mice, ordinary USB
mass-storage devices, and compatibility handling for third-party
controllers and adapters.

New XID drivers should select devices using the XID type, subtype,
capabilities, and advertised report sizes. Specialty devices should not be
forced through `xbox_controller_t` when their controls do not match a
standard Duke or Controller S.
