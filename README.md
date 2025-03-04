# i2c_can

Python library to communicate with the Longan Labs I2C CAN Bus Module

Also sold as the SEEED STUDIO 113020111 I2C CAN-BUS Module

## This library is nowhere near as safe, good, advanced, robust OR **SAFE** as https://github.com/commaai/panda so don't use it to drive a car.


## Overview


add text


## Features

- tbc

## Installation

Install the TPMS library using pip:

```bash
pip install i2c_can
```
## Usage

[TL;DR](https://github.com/SamSkjord/TPMS/tree/main?tab=readme-ov-file#tldr)

add text

## Example Code

### add examples


# TLDR
```bash
pip install i2c_can
```

```python
from i2c_can import I2C_CAN, CAN_500KBPS, CAN_OK, CAN_MSGAVAIL
import time

def initialize_can():
    i2c_can = I2C_CAN(0x25)  # Replace with the actual I2C address
    i2c_can.begin()
    
    print("begin to init can")
    
    while True:
        if i2c_can.begin_CAN(CAN_500KBPS) == CAN_OK:
            break
        print("CAN BUS FAIL!")
        time.sleep(0.1)
    print("CAN BUS OK!")
    return i2c_can

def send_obd2_request(i2c_can, pid):
    request = [0x02, 0x01, pid, 0x00, 0x00, 0x00, 0x00, 0x00]
    i2c_can.send_msg_buf(0x7DF, 0, 0, 8, request)
    print(f"Sent OBD2 request for PID {pid:02X}")

def receive_obd2_response(i2c_can):
    if i2c_can.check_receive() == CAN_MSGAVAIL:
        length, buf = i2c_can.read_msg_buf()
        if length:
            can_id = i2c_can.get_can_id()
            print(f"Received CAN ID: {can_id:X}")
            print("Data: ", ' '.join(f"{byte:02X}" for byte in buf))
            return buf
    return None

def parse_response(pid, response):
    if pid == 0x0C:  # Engine RPM
        rpm = ((response[3] * 256) + response[4]) / 4
        print(f"Engine RPM: {rpm}")
    elif pid == 0x0D:  # Vehicle Speed
        speed = response[3]
        print(f"Vehicle Speed: {speed} km/h")
    elif pid == 0x05:  # Engine Coolant Temperature
        temp = response[3] - 40
        print(f"Engine Coolant Temperature: {temp} °C")

def main():
    i2c_can = initialize_can()

    # List of PIDs to monitor
    pids = [0x0C, 0x0D, 0x05]  # Engine RPM, Vehicle Speed, Engine Coolant Temperature

    while True:
        for pid in pids:
            send_obd2_request(i2c_can, pid)
            response = receive_obd2_response(i2c_can)
            if response:
                if response[2] == pid:
                    parse_response(pid, response)
            time.sleep(1)  # Delay between requests for different PIDs

if __name__ == "__main__":
    main()

```

## TODO

Check it actually works...

## License
This project is licensed under the MIT License - see the LICENSE file for details.
