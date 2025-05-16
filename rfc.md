## Requirements

- **ROS2 Iron**
- **Python 3.8**

## Node Architecture

```text
+----------------------+        +----------------------+
|  ROS 2 Client Node   |        |     Modbus Node      |
|----------------------|        |----------------------|
| - Subscriber          <--------  - Publisher         |
| - Service Client      <------->  - Service Server    |
+----------------------+        +----------------------+
                                        |
              -------------------------------------------------
             /                       |                         \
+------------------+     +------------------+     +------------------+
| IO-Link Master   |     |  Agrowtek GCX    |     |   Stride IO      |
+------------------+     +------------------+     +------------------+
```

The *publisher* from the modbus node publishes data read from the registers of the devices to the *subscriber* at a specified polling rate. The polling rate for each device is as follows:

- **Stride I/O** – 1 second  
- **Agrowtek GCX** – 1 second  
- **IO-Link Master** – 0.2 second

The polling rates are set as a parameter with the following names:

- stride_io_polling_interval 
- agrowtek_gcx_polling_interval
- io_link_master_polling_interval

These polling rates can be configured within the ROS2 parameters with the command:

`ros2 param set <polling_rate_parameter> <polling_rate>`

Within the codebase, the publishers are created with the function:

`create_publisher(format, topic, polling_rate)`

The subscribers need to listen to the specific topic to receive information.


The *Service Client* can communicate with the *Service Server* in order to write data to the registers of the connected devices. If needed, it can also be used to send a read request as well. After the *Service Client* sends a message for a request, it will receive a result code that either depicts a success or an error.

- SUCCESS = 0
- ERROR_INVALID_REGISTER = 1
- ERROR_WRITE_FAILED = 2
- ERROR_READ_FAILED = 3
