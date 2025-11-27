# Hardware Interface

Hardware interface is our way to bridge between our program and the hardware, like microcontroller and IMU. 

## Microcontroller Firmware: `subcon_src`

Microcontroller Firwmware: https://github.com/Altair-Humanoid-GMRT/subcon_src.git

`subcon_src` is our firmware source code that send IMU data and button state in binary format. The format we send to SBC (single board computer): 

| Header   | Data Length | Data      | Checksum |
|----------|-------------|-----------|----------|
| 2 bytes  | 1 byte      | 40 bytes  | 1 byte   |

The `header` contains 0xFF 0xFF. `data length` contains the information of data length we are going to send. `Data` contains the actual IMU measurement like roll, pitch, yaw, linear acceleration, and angular velocity on x, y, z axis, plus button state information. `Checksum` contains a simple way to verify that the data received matches what was sent. If any bit flips during transmission, the recalculated checksum on the receiver side will differ. 

The axis convension for IMU (right-hand rule):

- X axis: Forward (+), backward (-)
- Y axis: Left (+), right (-)
- Z axis: Up (+), down (-)

## Serial Reading/ Receive

The SBC needs a program that read the serial transfered from the microcontroller. We can use PySerial (Python) or [serialib](https://github.com/imabot2/serialib) (C++).

> [!IMPORTANT]  
> The serial receive frequency on the SBC must be at least as high as, preferably higher than, the rate at which the microcontroller sends data.
