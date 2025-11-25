# Message processing error

Specially crafted USB packet could trigger a buffer overflow which could lead to code execution on older firmware.

Reported by: Christian Reitter

---

Fixed in version v2.1.1, commit: https://github.com/trezor/trezor-firmware/commit/c9113fd3f5fcd78e9e560dbac75ed5aae359eb2d

In the review version (v2.0.5) we don't have the vulnerable function `msg_read_common`, but function with similar logic `_usb_read` exists in `embed/bootloader/messages.c`.

The `_usb_read` function receives `pb_istream_t`, `uint8_t *buf` which has size of `USB_PACKET_SIZE` (64), and `size_t count` which is value parsed from the packet header.

Compared to the vulnerable variable of the function, this one correctly checks the size of the buffers (USB_PACKET_SIZE) with this condition: `(state->packet_pos + remaining <= USB_PACKET_SIZE)`. If the condition is not met, only part that fits is copied.