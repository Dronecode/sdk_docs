# Example: Offboard Velocity

This example shows how to how to control a vehicle in *Offboard mode* using velocity commands (in both the NED and body frames).

![Offboard Mode - Velocity Control QGC Screenshot](../../assets/examples/offboard_mode/qgc_offboard_velocity.png)


## Running the Example {#run_example}

The example is built and run [as described here](../examples/README.md#trying_the_examples) (the standard way). 

The example terminal output should be similar to that shown below:

> **Note** This is from a debug build of DroneCore. A release build will omit the "Debug" messages.

```
$ ./offboard 
ubuntu@ubuntu:~/DroneCore/example/offboard_velocity/build$ ./offboard 
Wait for device to connect via heartbeat
[03:48:52|Info ] New device on: 127.0.0.1:14557 (udp_connection.cpp:210)
[03:48:52|Debug] MAVLink: info: [logger] file: rootfs/fs/microsd/log/2017-11-14/2 (device_impl.cpp:225)
[03:48:53|Debug] Discovered 4294967298 (dronecore_impl.cpp:234)
Waiting for device to be ready
...
Waiting for device to be ready
Device is ready
Armed
[03:49:07|Debug] MAVLink: info: ARMED by arm/disarm component command (device_impl.cpp:225)
In Air...
[03:49:07|Debug] MAVLink: info: Using minimum takeoff altitude: 2.50 m (device_impl.cpp:225)
[03:49:07|Debug] MAVLink: info: Takeoff detected (device_impl.cpp:225)
[03:49:07|Debug] MAVLink: critical: Using minimum takeoff altitude: 2.50 m (device_impl.cpp:225)
[NED] Offboard started
[NED] Turn to face East
[NED] Go North and back South
[NED] Turn to face West
[NED] Go up 2 m/s, turn to face South
[NED] Go down 1 m/s, turn to face North
[NED] Offboard stopped
[BODY] Offboard started
[BODY] Turn clock-wise and climb
[BODY] Turn back anti-clockwise
[BODY] Wait for a bit
[BODY] Fly a circle
[BODY] Wait for a bit
[BODY] Fly a circle sideways
[BODY] Wait for a bit
[BODY] Offboard stopped
[03:50:31|Debug] MAVLink: info: Landing at current position (device_impl.cpp:225)
Landed
```

## How it works

The operation of most of this code is discussed in the guide: [Offboard Control](../guide/offboard.md).

## Source code {#source_code}

> **Tip** The full source code for the example [can be found on Github here](https://github.com/dronecore/DroneCore/tree/{{ book.github_branch }}/example/offboard_velocity).


[CMakeLists.txt](https://github.com/dronecore/DroneCore/blob/{{ book.github_branch }}/example/offboard_velocity/CMakeLists.txt)

```make
cmake_minimum_required(VERSION 2.8.12)

project(offboard)

if(NOT MSVC)
    add_definitions("-std=c++11 -Wall -Wextra -Werror")
else()
    add_definitions("-std=c++11 -WX -W2")
    add_definitions("-D_USE_MATH_DEFINES") # For M_PI
    include_directories(${CMAKE_SOURCE_DIR}/../../install/include)
    link_directories(${CMAKE_SOURCE_DIR}/../../install/lib)
endif()

add_executable(offboard
    offboard_velocity.cpp
)

target_link_libraries(offboard
    dronecore
    dronecore_action
    dronecore_offboard
    dronecore_telemetry
)
```

[offboard_velocity.cpp](https://github.com/dronecore/DroneCore/blob/{{ book.github_branch }}/example/offboard_velocity/offboard_velocity.cpp)

```cpp
/**
 * @file offboard_velocity.cpp
 * @brief Example that demonstrates offboard velocity control in local NED and body coordinates
 *
 * @authors Author: Julian Oes <julian@oes.ch>,
 *                  Shakthi Prashanth <shakthi.prashanth.m@intel.com>
 * @date 2017-10-17
 */

#include <chrono>
#include <cmath>
#include <dronecore/action.h>
#include <dronecore/dronecore.h>
#include <dronecore/offboard.h>
#include <dronecore/telemetry.h>
#include <iostream>
#include <thread>

using namespace dronecore;
using std::this_thread::sleep_for;
using std::chrono::milliseconds;
using std::chrono::seconds;

#define ERROR_CONSOLE_TEXT "\033[31m" //Turn text on console red
#define TELEMETRY_CONSOLE_TEXT "\033[34m" //Turn text on console blue
#define NORMAL_CONSOLE_TEXT "\033[0m"  //Restore normal console colour

// Handles Action's result
inline void action_error_exit(Action::Result result, const std::string &message)
{
    if (result != Action::Result::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message << Action::result_str(
                      result) << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}

// Handles Offboard's result
inline void offboard_error_exit(Offboard::Result result, const std::string &message)
{
    if (result != Offboard::Result::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message << Offboard::result_str(
                      result) << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}

// Handles connection result
inline void connection_error_exit(ConnectionResult result, const std::string &message)
{
    if (result != ConnectionResult::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message
                  << connection_result_str(result)
                  << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}

// Logs during Offboard control
inline void offboard_log(const std::string &offb_mode, const std::string msg)
{
    std::cout << "[" << offb_mode << "] " << msg << std::endl;
}

/**
 * Does Offboard control using NED co-ordinates.
 *
 * returns true if everything went well in Offboard control, exits with a log otherwise.
 */
bool offb_ctrl_ned(std::shared_ptr<dronecore::Offboard> offboard)
{
    const std::string offb_mode = "NED";
    // Send it once before starting offboard, otherwise it will be rejected.
    offboard->set_velocity_ned({0.0f, 0.0f, 0.0f, 0.0f});

    Offboard::Result offboard_result = offboard->start();
    offboard_error_exit(offboard_result, "Offboard start failed");
    offboard_log(offb_mode, "Offboard started");

    offboard_log(offb_mode,  "Turn to face East");
    offboard->set_velocity_ned({0.0f, 0.0f, 0.0f, 90.0f});
    sleep_for(seconds(1)); // Let yaw settle.

    {
        const float step_size = 0.01f;
        const float one_cycle = 2.0f * (float)M_PI;
        const unsigned steps = 2 * unsigned(one_cycle / step_size);

        offboard_log(offb_mode,  "Go North and back South");
        for (unsigned i = 0; i < steps; ++i) {
            float vx = 5.0f * sinf(i * step_size);
            offboard->set_velocity_ned({vx, 0.0f, 0.0f, 90.0f});
            sleep_for(milliseconds(10));
        }
    }

    offboard_log(offb_mode,  "Turn to face West");
    offboard->set_velocity_ned({0.0f, 0.0f, 0.0f, 270.0f});
    sleep_for(seconds(2));


    offboard_log(offb_mode, "Go up 2 m/s, turn to face South");
    offboard->set_velocity_ned({0.0f, 0.0f, -2.0f, 180.0f});
    sleep_for(seconds(4));

    offboard_log(offb_mode,  "Go down 1 m/s, turn to face North");
    offboard->set_velocity_ned({0.0f, 0.0f, 1.0f, 0.0f});
    sleep_for(seconds(4));

    // Now, stop offboard mode.
    offboard_result = offboard->stop();
    offboard_error_exit(offboard_result, "Offboard stop failed: ");
    offboard_log(offb_mode, "Offboard stopped");

    return true;
}

/**
 * Does Offboard control using body co-ordinates.
 *
 * returns true if everything went well in Offboard control, exits with a log otherwise.
 */
bool offb_ctrl_body(std::shared_ptr<dronecore::Offboard> offboard)
{

    const std::string offb_mode = "BODY";

    // Send it once before starting offboard, otherwise it will be rejected.
    offboard->set_velocity_body({0.0f, 0.0f, 0.0f, 0.0f});

    Offboard::Result offboard_result = offboard->start();
    offboard_error_exit(offboard_result, "Offboard start failed: ");
    offboard_log(offb_mode, "Offboard started");

    offboard_log(offb_mode, "Turn clock-wise and climb");
    offboard->set_velocity_body({0.0f, 0.0f, -1.0f, 60.0f});
    sleep_for(seconds(5));

    offboard_log(offb_mode, "Turn back anti-clockwise");
    offboard->set_velocity_body({0.0f, 0.0f, 0.0f, -60.0f});
    sleep_for(seconds(5));

    offboard_log(offb_mode, "Wait for a bit");
    offboard->set_velocity_body({0.0f, 0.0f, 0.0f, 0.0f});
    sleep_for(seconds(2));

    offboard_log(offb_mode, "Fly a circle");
    offboard->set_velocity_body({5.0f, 0.0f, 0.0f, 30.0f});
    sleep_for(seconds(15));

    offboard_log(offb_mode, "Wait for a bit");
    offboard->set_velocity_body({0.0f, 0.0f, 0.0f, 0.0f});
    sleep_for(seconds(5));

    offboard_log(offb_mode, "Fly a circle sideways");
    offboard->set_velocity_body({0.0f, -5.0f, 0.0f, 30.0f});
    sleep_for(seconds(15));

    offboard_log(offb_mode, "Wait for a bit");
    offboard->set_velocity_body({0.0f, 0.0f, 0.0f, 0.0f});
    sleep_for(seconds(8));

    offboard_result = offboard->stop();
    offboard_error_exit(offboard_result, "Offboard stop failed: ");
    offboard_log(offb_mode, "Offboard stopped");

    return true;
}

int main(int, char **)
{
    DroneCore dc;

    ConnectionResult conn_result = dc.add_udp_connection();
    connection_error_exit(conn_result, "Connection failed");

    // Wait for the device to connect via heartbeat
    while (!dc.is_connected()) {
        std::cout << "Wait for device to connect via heartbeat" << std::endl;
        sleep_for(seconds(1));
    }

    // Device got discovered.
    Device &device = dc.device();
    std::shared_ptr<Action> action = std::make_shared<Action>(&device);
    std::shared_ptr<Offboard> offboard = std::make_shared<Offboard>(&device);
    std::shared_ptr<Telemetry> telemetry = std::make_shared<Telemetry>(&device);

    while (!telemetry->health_all_ok()) {
        std::cout << "Waiting for device to be ready" << std::endl;
        sleep_for(seconds(1));
    }
    std::cout << "Device is ready" << std::endl;

    Action::Result arm_result = action->arm();
    action_error_exit(arm_result, "Arming failed");
    std::cout << "Armed" << std::endl;

    Action::Result takeoff_result = action->takeoff();
    action_error_exit(takeoff_result, "Takeoff failed");
    std::cout << "In Air..." << std::endl;
    sleep_for(seconds(5));


    //  using local NED co-ordinates
    bool ret = offb_ctrl_ned(offboard);
    if (ret == false) {
        return EXIT_FAILURE;
    }

    //  using body co-ordinates
    ret = offb_ctrl_body(offboard);
    if (ret == false) {
        return EXIT_FAILURE;
    }

    const Action::Result land_result = action->land();
    action_error_exit(land_result, "Landing failed");

    // We are relying on auto-disarming but let's keep watching the telemetry for a bit longer.
    sleep_for(seconds(10));
    std::cout << "Landed" << std::endl;

    return EXIT_SUCCESS;
}
```
