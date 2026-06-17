# urikvm architecture

`kvm://` is a high-level computer-control contract. It should not directly know whether control is implemented by PyAutoGUI, VNC, RDP, a USB HID gadget, a browser automation bridge, or a physical KVM.

## Stack

```text
HTTP client
  → POST /uri/call :8794
  → urikvmedge (urisys-kvm)
  → urisysedge.Runtime + urisysedge.http.serve
  → standalone packs:
       urikvm   (kvm://)
       urihim   (him://)
       uriocr   (ocr://)
       urillm   (llm://)
```

Docker demo: [`urikvm-docker`](../) installs sibling packs and runs `urisys-kvm serve`.

## Layers

```text
kvm:// task/display/monitor
  uses ocr:// to read screen text
  uses llm:// to decide action from screen/goal
  uses him:// to perform mouse/keyboard input
```

| Scheme | Role |
|--------|------|
| `kvm://` | High-level task: screenshot, click text, type text, inspect monitor |
| `him://` | Mouse and keyboard commands |
| `ocr://` | Text extraction from screenshots |
| `llm://` | Reasoning/vision analysis over OCR/screenshot |

Real control is disabled by default. Use mock for Docker and tests. Enable real input only on a trusted local machine with explicit `--allow-real`.
