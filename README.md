# Alyx I100 – Simple Processing Unit (SPU)

The **Alyx I100** is a compact, ESP32-based **Simple Processing Unit (SPU)** designed to perform basic computational operations, execute simple mathematical calculations, and support lightweight neural network training directly on embedded hardware.

Built around the powerful and versatile ESP32 microcontroller, the Alyx I100 provides an accessible platform for experimenting with embedded artificial intelligence, machine learning, and hardware-level computation without requiring a traditional GPU or high-performance processor.

The device is controlled through a dedicated **Python library**, which acts as its central control interface. This library allows users to communicate with the SPU, configure its processing parameters, send computational instructions, manage neural network operations, and retrieve results through a simple Python-based environment.

## Installment :

1. Flash the firmware from this [file](https://github.com/alyxofficial/Alyx-I100/blob/main/alyx-i100/alyx-i100.ino).
2. Install *alyxspu* with pip :
```
pip install alyxspu
```
## Usage :
### 1. Use it from Python

```python
from alyxspu import AlyxSPU

with AlyxSPU("COM5") as spu:              # "/dev/ttyUSB0" on Linux, omit if only one port exists
    print(spu.calc("sqrt(2)*3 + sin(pi/2)"))

    spu.build(inputs=2, hidden=4, outputs=1)
    spu.set_data([([0, 0], [0]), ([0, 1], [1]), ([1, 0], [1]), ([1, 1], [0])])   # XOR
    history = spu.train(epochs=3000, lr=0.5, on_loss=lambda e, loss: print(e, loss))
    print(spu.run([1, 0]))                # close to [1.0]
```

| Method | What it does |
|---|---|
| `AlyxSPU.list_ports()` | List serial ports |
| `calc(expr)` | Evaluate `+ - * / ^ ( )`, `pi sqrt sin cos tan exp ln abs` on the ESP32 |
| `build(i, h, o)` | New 1-hidden-layer sigmoid network (max 8 / 16 / 4) |
| `set_data(samples)`, `add_sample(x, y)`, `clear_data()` | Training set (max 32 samples, targets 0 to 1) |
| `train(epochs, lr, on_loss=None)` | Train on-device, returns `[(epoch, loss), ...]` |
| `run(inputs)` | Forward pass, returns the outputs |
| `info()`, `ping()`, `command(line)` | Status and raw access |

Errors from the device or the connection raise `AlyxError`.

### 2. Desktop app

```
alyxspu-studio
```

(or `python -m alyxspu`). Pick the port, connect, and use the calculator and neural network tabs. It needs tkinter, which ships with most Python installs.

### Key Features

- **ESP32-powered architecture:** Uses an ESP32 microcontroller as its primary processing hardware.
- **Mathematical computation:** Performs basic arithmetic and computational operations.
- **Neural network support:** Enables lightweight neural network training and experimentation.
- **Python control interface:** Provides a dedicated Python library for communication and device management.
- **USB serial communication:** Connects the SPU to a computer for sending commands and receiving results.
- **Embedded AI experimentation:** Makes it possible to explore machine learning concepts on low-power hardware.
- **Compact design:** Combines processing capabilities and control functionality in a small embedded platform.

### Architecture

The Alyx I100 operates using a simple hardware-software architecture. The ESP32 handles the computational workload, while the Python library running on a connected computer acts as the control center.

Through this architecture, users can develop Python applications that interact directly with the device, submit calculations, manage neural network configurations, and retrieve processing results.

### Purpose

The goal of the Alyx I100 is to make embedded processing and neural network experimentation more accessible. Rather than functioning as a conventional GPU or CPU, it introduces a simplified processing architecture focused on basic computation, educational AI applications, and experimentation with neural networks.

## Serial protocol

115200 baud, one text command per line: `PING`, `INFO`, `CALC <expr>`, `NET <i> <h> <o>`, `DATA x.. | y..`, `CLEAR`, `TRAIN <epochs> <lr>`, `RUN x..`. Replies start with `OK`, `ERR`, `RES`, `OUT` or `LOSS`.

## License

MIT
