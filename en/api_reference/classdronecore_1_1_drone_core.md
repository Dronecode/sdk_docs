# dronecore::DroneCore Class Reference
`#include: dronecore.h`

----


This is the main class of **DroneCore MAVLink API Library** (for the Dronecode Platform). 


It is used to discover vehicles and manage active connections.


An instance of this class must be created (first) in order to use the library. The instance must be destroyed after use in order to break connections and release all resources. 


## Public Types


Type | Description
--- | ---
std::function< void(uint64_t uuid)> [event_callback_t](#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3) | Callback type for discover and timeout notifications.

## Public Member Functions


Type | Name | Description
---: | --- | ---
&nbsp; | [DroneCore](#classdronecore_1_1_drone_core_1a0b94dd09cd46faa41742d3720f210aa2) () | Constructor.
&nbsp; | [~DroneCore](#classdronecore_1_1_drone_core_1abbaedb6fd922c023e53611b484b38582) () | Destructor.
[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) | [add_any_connection](#classdronecore_1_1_drone_core_1a384ae5189b047dd3df8d7e90c42fa021) (const std::string & connection_url=DEFAULT_UDP_CONNECTION_URL) | Adds Connection via URL.
[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) | [add_udp_connection](#classdronecore_1_1_drone_core_1a7a04fbacf95eb6b21418032c8287dfbb) (int local_port_number=DEFAULT_UDP_PORT) | Adds a UDP connection to the specified port number.
[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) | [add_tcp_connection](#classdronecore_1_1_drone_core_1a725640cb53c0d077e753ea2d22717b68) (const std::string & remote_ip=DEFAULT_TCP_REMOTE_IP, int remote_port=DEFAULT_TCP_REMOTE_PORT) | Adds a TCP connection with a specific IP address and port number.
[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) | [add_serial_connection](#classdronecore_1_1_drone_core_1abde7ed4d42875dc85c73d34fedab2902) (const std::string & dev_path=DEFAULT_SERIAL_DEV_PATH, int baudrate=DEFAULT_SERIAL_BAUDRATE) | Adds a serial connection with a specific port (COM or UART dev node) and baudrate as specified.
const std::vector< uint64_t > & | [device_uuids](#classdronecore_1_1_drone_core_1a20490717da3893be2c6965b905a7c1db) () const | Get vector of device UUIDs.
[Device](classdronecore_1_1_device.md) & | [device](#classdronecore_1_1_drone_core_1a5bac6e419e56a1f77a51adef98e94e7c) () const | Get the first discovered device.
[Device](classdronecore_1_1_device.md) & | [device](#classdronecore_1_1_drone_core_1a7d18869c8c8f518af78bee313b554a2a) (uint64_t uuid)const | Get the device with the specified UUID.
bool | [is_connected](#classdronecore_1_1_drone_core_1a2adf0c6fc5521fa8f446e048a09f5ec1) () const | Returns `true` if exactly one device is currently connected.
bool | [is_connected](#classdronecore_1_1_drone_core_1a2f78c2263df997d38cf508e327fcde23) (uint64_t uuid)const | Returns `true` if a device is currently connected.
void | [register_on_discover](#classdronecore_1_1_drone_core_1a864ec7349eba67b02b8b3792f6c388f9) ([event_callback_t](classdronecore_1_1_drone_core.md#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3) callback) | Register callback for device discovery.
void | [register_on_timeout](#classdronecore_1_1_drone_core_1ad8c0dc0100449d21a46a787c810e8978) ([event_callback_t](classdronecore_1_1_drone_core.md#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3) callback) | Register callback for device timeout.

## Static Public Attributes


static constexpr auto [DEFAULT_UDP_CONNECTION_URL](#classdronecore_1_1_drone_core_1aef2ad569dd9a7d0762b1d17d21e7f598) = "udp://:14540" - 


static constexpr int [DEFAULT_UDP_PORT](#classdronecore_1_1_drone_core_1aa989b494349529f412b36984a46f2ca6) = 14540 - 


static constexpr auto [DEFAULT_TCP_REMOTE_IP](#classdronecore_1_1_drone_core_1ade2086911d3052093fb87717f58a99b2) = "127.0.0.1" - 


static constexpr int [DEFAULT_TCP_REMOTE_PORT](#classdronecore_1_1_drone_core_1a0ded56f7f5873f17e424343ed7b2e5af) = 5760 - 


static constexpr auto [DEFAULT_SERIAL_DEV_PATH](#classdronecore_1_1_drone_core_1a67928c93b8b8f2ab65cf28a4b6d9436f) = "/dev/ttyS0" - 


static constexpr int [DEFAULT_SERIAL_BAUDRATE](#classdronecore_1_1_drone_core_1a2b20d34acc312ffe6197aebdc6769a86) = 57600 - 


## Constructor & Destructor Documentation


### DroneCore() {#classdronecore_1_1_drone_core_1a0b94dd09cd46faa41742d3720f210aa2}
```cpp
dronecore::DroneCore::DroneCore()
```


Constructor.


### ~DroneCore() {#classdronecore_1_1_drone_core_1abbaedb6fd922c023e53611b484b38582}
```cpp
dronecore::DroneCore::~DroneCore()
```


Destructor.

Disconnects all connected vehicles and releases all resources.

## Member Typdef Documentation


### typedef event_callback_t {#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3}

```cpp
typedef std::function<void(uint64_t uuid)> dronecore::DroneCore::event_callback_t
```


Callback type for discover and timeout notifications.


**Parameters**

* **uuid** - UUID of device (or MAVLink system ID for devices that don't have a UUID).

## Member Function Documentation


### add_any_connection() {#classdronecore_1_1_drone_core_1a384ae5189b047dd3df8d7e90c42fa021}
```cpp
ConnectionResult dronecore::DroneCore::add_any_connection(const std::string &connection_url=DEFAULT_UDP_CONNECTION_URL)
```


Adds Connection via URL.

Supports connection: Serial, TCP or UDP. Connection URL format should be:
<ul>

<li><p>UDP - udp://[Bind_host][:Bind_port]</p></li>
<li><p>TCP - tcp://[Server_host][:Server_port]</p></li>
<li><p>Serial - serial://[Dev_Node][:Baudrate]</p></li></ul>


Default URL : udp://:14540.
<ul>

<li><p>Default Bind host IP is localhost(127.0.0.1)</p></li></ul>

**Parameters**

* const std::string & **connection_url** - connection URL string.

**Returns**

&emsp;[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) - The result of adding the connection.

### add_udp_connection() {#classdronecore_1_1_drone_core_1a7a04fbacf95eb6b21418032c8287dfbb}
```cpp
ConnectionResult dronecore::DroneCore::add_udp_connection(int local_port_number=DEFAULT_UDP_PORT)
```


Adds a UDP connection to the specified port number.


**Parameters**

* int **local_port_number** - The local UDP port to listen to (defaults to 14540, the same as mavros).

**Returns**

&emsp;[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) - The result of adding the connection.

### add_tcp_connection() {#classdronecore_1_1_drone_core_1a725640cb53c0d077e753ea2d22717b68}
```cpp
ConnectionResult dronecore::DroneCore::add_tcp_connection(const std::string &remote_ip=DEFAULT_TCP_REMOTE_IP, int remote_port=DEFAULT_TCP_REMOTE_PORT)
```


Adds a TCP connection with a specific IP address and port number.


**Parameters**

* const std::string & **remote_ip** - Remote IP address to connect to (defaults to 127.0.0.1).
* int **remote_port** - The TCP port to connect to (defaults to 5760).

**Returns**

&emsp;[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) - The result of adding the connection.

### add_serial_connection() {#classdronecore_1_1_drone_core_1abde7ed4d42875dc85c73d34fedab2902}
```cpp
ConnectionResult dronecore::DroneCore::add_serial_connection(const std::string &dev_path=DEFAULT_SERIAL_DEV_PATH, int baudrate=DEFAULT_SERIAL_BAUDRATE)
```


Adds a serial connection with a specific port (COM or UART dev node) and baudrate as specified.


**Parameters**

* const std::string & **dev_path** - COM or UART dev node name/path (defaults to "/dev/ttyS0").
* int **baudrate** - Baudrate of the serial port (defaults to 57600).

**Returns**

&emsp;[ConnectionResult](namespacedronecore.md#namespacedronecore_1a42d7afdc816d7f750e1a8d4282da0ddc) - The result of adding the connection.

### device_uuids() {#classdronecore_1_1_drone_core_1a20490717da3893be2c6965b905a7c1db}
```cpp
const std::vector<uint64_t>& dronecore::DroneCore::device_uuids() const
```


Get vector of device UUIDs.

This returns a vector of the UUIDs of all devices that have been discovered. If a device doesn't have a UUID then [DroneCore](classdronecore_1_1_drone_core.md) will instead use its MAVLink system ID (range: 0..255).

**Returns**

&emsp;const std::vector< uint64_t > & - A reference to the vector containing the UUIDs.

### device() {#classdronecore_1_1_drone_core_1a5bac6e419e56a1f77a51adef98e94e7c}
```cpp
Device& dronecore::DroneCore::device() const
```


Get the first discovered device.

This returns the first discovered device or a null device if no device has yet been found.

**Returns**

&emsp;[Device](classdronecore_1_1_device.md) & - A reference to a device.

### device() {#classdronecore_1_1_drone_core_1a7d18869c8c8f518af78bee313b554a2a}
```cpp
Device& dronecore::DroneCore::device(uint64_t uuid) const
```


Get the device with the specified UUID.

This returns a device for a given UUID if such a device has been discovered and a null device otherwise.

**Parameters**

* uint64_t **uuid** - UUID of device to get.

**Returns**

&emsp;[Device](classdronecore_1_1_device.md) & - A reference to the specified device.

### is_connected() {#classdronecore_1_1_drone_core_1a2adf0c6fc5521fa8f446e048a09f5ec1}
```cpp
bool dronecore::DroneCore::is_connected() const
```


Returns `true` if exactly one device is currently connected.

Connected means we are receiving heartbeats from this device. It means the same as "discovered" and "not timed out".


If multiple devices have connected, this will return `false`.

**Returns**

&emsp;bool - `true` if exactly one device is connected.

### is_connected() {#classdronecore_1_1_drone_core_1a2f78c2263df997d38cf508e327fcde23}
```cpp
bool dronecore::DroneCore::is_connected(uint64_t uuid) const
```


Returns `true` if a device is currently connected.

Connected means we are receiving heartbeats from this device. It means the same as "discovered" and "not timed out".

**Parameters**

* uint64_t **uuid** - UUID of device to check.

**Returns**

&emsp;bool - `true` if device is connected.

### register_on_discover() {#classdronecore_1_1_drone_core_1a864ec7349eba67b02b8b3792f6c388f9}
```cpp
void dronecore::DroneCore::register_on_discover(event_callback_t callback)
```


Register callback for device discovery.

This sets a callback that will be notified if a new device is discovered.


**Note** Only one callback can be registered at a time. If this function is called several times, previous callbacks will be overwritten.

**Parameters**

* [event_callback_t](classdronecore_1_1_drone_core.md#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3) **callback** - Callback to register.

### register_on_timeout() {#classdronecore_1_1_drone_core_1ad8c0dc0100449d21a46a787c810e8978}
```cpp
void dronecore::DroneCore::register_on_timeout(event_callback_t callback)
```


Register callback for device timeout.

This sets a callback that will be notified if no heartbeat of the device has been received in 3 seconds.


**Note** Only one callback can be registered at a time. If this function is called several times, previous callbacks will be overwritten.

**Parameters**

* [event_callback_t](classdronecore_1_1_drone_core.md#classdronecore_1_1_drone_core_1abb488f975ee7e199cd1cb08a317a52c3) **callback** - Callback to register.

## Field Documentation


### DEFAULT_UDP_CONNECTION_URL {#classdronecore_1_1_drone_core_1aef2ad569dd9a7d0762b1d17d21e7f598}

```cpp
constexpr auto dronecore::DroneCore::DEFAULT_UDP_CONNECTION_URL = "udp://:14540"
```


### DEFAULT_UDP_PORT {#classdronecore_1_1_drone_core_1aa989b494349529f412b36984a46f2ca6}

```cpp
constexpr int dronecore::DroneCore::DEFAULT_UDP_PORT = 14540
```


### DEFAULT_TCP_REMOTE_IP {#classdronecore_1_1_drone_core_1ade2086911d3052093fb87717f58a99b2}

```cpp
constexpr auto dronecore::DroneCore::DEFAULT_TCP_REMOTE_IP = "127.0.0.1"
```


### DEFAULT_TCP_REMOTE_PORT {#classdronecore_1_1_drone_core_1a0ded56f7f5873f17e424343ed7b2e5af}

```cpp
constexpr int dronecore::DroneCore::DEFAULT_TCP_REMOTE_PORT = 5760
```


### DEFAULT_SERIAL_DEV_PATH {#classdronecore_1_1_drone_core_1a67928c93b8b8f2ab65cf28a4b6d9436f}

```cpp
constexpr auto dronecore::DroneCore::DEFAULT_SERIAL_DEV_PATH = "/dev/ttyS0"
```


### DEFAULT_SERIAL_BAUDRATE {#classdronecore_1_1_drone_core_1a2b20d34acc312ffe6197aebdc6769a86}

```cpp
constexpr int dronecore::DroneCore::DEFAULT_SERIAL_BAUDRATE = 57600
```

