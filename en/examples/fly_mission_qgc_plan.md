# Example: Fly QGroundControl Plan Mission

The [Fly QGroundControl Plan Mission](https://github.com/dronecore/DroneCore/tree/{{ book.github_branch }}/example/fly_qgc_mission) example shows how to import a *QGroundControl* mission plan, upload it to a vehicle, run the mission, and then command Return mode ("RTL").

![Fly Mission QGC Screenshot - From Plan](../../assets/examples/fly_qgc_mission/fly_qgc_plan_mission_example_qgc.jpg)


## Running the Example {#run_example}

The example is built and run in the normal way ([as described here](../examples/README.md#trying_the_examples)). 

> **Tip** By default the example will load a sample plan from the plugin unit test: [/plugins/mission/qgroundcontrol_sample.plan](https://github.com/dronecore/DroneCore/blob/{{ book.github_branch }}/plugins/mission/qgroundcontrol_sample.plan). Alternatively you can specify the plan to load when you start the example:
  ```
  ./fly_qgc_mission <path of QGC Mission plan>
  ```


The example terminal output should be similar to that shown below:

> **Note** This is from a debug build of DroneCore. A release build will omit the "Debug" messages.

```
$ ./fly_qgc_mission 
Usage: ./fly_qgc_mission <path of QGC Mission plan>
Importing mission from Default mission plan: ../../../plugins/mission/qgroundcontrol_sample.plan
Waiting to discover device...
[02:25:09|Info ] New device on: 127.0.0.1:14557 (udp_connection.cpp:211)
[02:25:09|Debug] MAVLink: info: DISARMED by auto disarm on land (device.cpp:247)
[02:25:09|Debug] Discovered 4294967298 (dronecore_impl.cpp:219)
Discovered device with UUID: 4294967298
Waiting for device to be ready
Waiting for device to be ready
Device ready
Found 8 mission items in the given QGC plan.
Uploading mission...
[02:25:11|Debug] Send mission item 0 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 1 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 2 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 3 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 4 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 5 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 6 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 7 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 8 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 9 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 10 (mission_impl.cpp:781)
[02:25:11|Debug] Send mission item 11 (mission_impl.cpp:781)
[02:25:12|Debug] Send mission item 12 (mission_impl.cpp:781)
[02:25:12|Debug] Send mission item 13 (mission_impl.cpp:781)
[02:25:12|Debug] Send mission item 14 (mission_impl.cpp:781)
Mission uploaded.
Arming...
[02:25:12|Info ] Mission accepted (mission_impl.cpp:146)
Armed.
Starting mission.
[02:25:12|Debug] MAVLink: info: ARMED by arm/disarm component command (device.cpp:247)
[02:25:12|Debug] MAVLink: info: [logger] file: rootfs/fs/microsd/log/2018-02-15/0 (device.cpp:247)
Started mission.
[02:25:12|Debug] MAVLink: info: Executing mission. (device.cpp:247)
[02:25:12|Debug] MAVLink: info: Takeoff to 15.0 meters above home. (device.cpp:247)
[02:25:12|Debug] MAVLink: info: Takeoff detected (device.cpp:247)
Mission status update: 0 / 8
Mission status update: 1 / 8
Mission status update: 1 / 8
Mission status update: 1 / 8
Mission status update: 1 / 8
Mission status update: 2 / 8
Mission status update: 2 / 8
Mission status update: 2 / 8
Mission status update: 2 / 8
Mission status update: 3 / 8
Mission status update: 3 / 8
Mission status update: 3 / 8
Mission status update: 3 / 8
Mission status update: 4 / 8
Mission status update: 4 / 8
Mission status update: 4 / 8
Mission status update: 4 / 8
Mission status update: 5 / 8
Mission status update: 5 / 8
Mission status update: 5 / 8
Mission status update: 5 / 8
Mission status update: 6 / 8
Mission status update: 6 / 8
Mission status update: 6 / 8
Mission status update: 6 / 8
Mission status update: 7 / 8
Mission status update: 7 / 8
Mission status update: 7 / 8
[02:26:19|Debug] MAVLink: info: Mission finished, loitering. (device.cpp:247)
Mission status update: 8 / 8
Commanding RTL...
Commanded RTL.
```

## How it works

The example application performs the following operations:
1. Imports QGC mission items from **.plan** file.
1. Uploads mission items to vehicle.
1. Sets up mission progress monitoring.
1. Starts the mission from the first mission item.
1. Commands the *Return*/RTL action once the mission completes.

The specific code for importing missions is discussed in the guide: [Missions > Import a Mission from a QGC Plan](../guide/missions.md#import_qgc_plan).

## Source code {#source_code}

> **Tip** The full source code for the example [can be found on Github here](https://github.com/dronecore/DroneCore/tree/{{ book.github_branch }}/example/fly_qgc_mission).


[CMakeLists.txt](https://github.com/dronecore/DroneCore/blob/{{ book.github_branch }}/example/fly_qgc_mission/CMakeLists.txt)

```make
cmake_minimum_required(VERSION 2.8.12)

project(fly_qgc_mission)

if(NOT MSVC)
    add_definitions("-std=c++11 -Wall -Wextra -Werror")
else()
    add_definitions("-std=c++11 -WX -W2")
    include_directories(${CMAKE_SOURCE_DIR}/../../install/include)
    link_directories(${CMAKE_SOURCE_DIR}/../../install/lib)
endif()

add_executable(fly_qgc_mission
    fly_qgc_mission.cpp
)

target_link_libraries(fly_qgc_mission
    dronecore
    dronecore_action
    dronecore_mission
    dronecore_telemetry
)
```

[fly_qgc_mission.cpp](https://github.com/dronecore/DroneCore/blob/{{ book.github_branch }}/example/fly_qgc_mission/fly_qgc_mission.cpp)

```cpp
/**
* @file fly_qgc_mission.cpp
*
* @brief Demonstrates how to import mission items from QGroundControl plan,
* and fly them using DroneCore.
*
* Steps to run this example:
* 1. (a) Create a Mission in QGroundControl and save them to a file (.plan) (OR)
*    (b) Use a pre-created sample mission plan in "plugins/mission/qgroundcontrol_sample.plan".
*    Click [here](https://user-images.githubusercontent.com/26615772/31763673-972c5bb6-b4dc-11e7-8ff0-f8b39b6b88c3.png) to see what sample mission plan in QGroundControl looks like.
* 2. Run the example by passing path of the QGC mission plan as argument (By default, sample mission plan is imported).
*
* Example description:
* 1. Imports QGC mission items from .plan file.
* 2. Uploads mission items to vehicle.
* 3. Starts mission from first mission item.
* 4. Commands RTL once QGC Mission is accomplished.
*
* @author Shakthi Prashanth M <shakthi.prashanth.m@intel.com>,
*         Julian Oes <julian@oes.ch>
* @date 2018-02-04
*/

#include <dronecore/action.h>
#include <dronecore/dronecore.h>
#include <dronecore/mission.h>
#include <dronecore/telemetry.h>
#include <functional>
#include <future>
#include <iostream>
#include <memory>

#define ERROR_CONSOLE_TEXT "\033[31m" //Turn text on console red
#define TELEMETRY_CONSOLE_TEXT "\033[34m" //Turn text on console blue
#define NORMAL_CONSOLE_TEXT "\033[0m"  //Restore normal console colour

using namespace dronecore;
using namespace std::chrono; // for seconds(), milliseconds()
using namespace std::this_thread; // for sleep_for()

// Handles Action's result
inline void handle_action_err_exit(Action::Result result, const std::string &message);
// Handles Mission's result
inline void handle_mission_err_exit(Mission::Result result, const std::string &message);
// Handles Connection result
inline void handle_connection_err_exit(ConnectionResult result,
                                       const std::string &message);

int main(int argc, char **argv)
{
    // Locate path of QGC Sample plan
    std::string qgc_plan = "../../../plugins/mission/qgroundcontrol_sample.plan";

    if (argc != 2) {
        std::cout << "Usage: " << argv[0] << " <path of QGC Mission plan>\n";
        std::cout << "Importing mission from Default mission plan: " << qgc_plan << std::endl;
    } else if (argc == 2) {
        std::cout << "Importing mission from mission plan: " << qgc_plan << std::endl;
        qgc_plan = argv[1];
    }

    DroneCore dc;

    {
        auto prom = std::make_shared<std::promise<void>>();
        auto future_result = prom->get_future();

        std::cout << "Waiting to discover device..." << std::endl;
        dc.register_on_discover([prom](uint64_t uuid) {
            std::cout << "Discovered device with UUID: " << uuid << std::endl;
            prom->set_value();
        });

        ConnectionResult connection_result = dc.add_udp_connection();
        handle_connection_err_exit(connection_result, "Connection failed: ");

        future_result.get();
    }

    dc.register_on_timeout([](uint64_t uuid) {
        std::cout << "Device with UUID timed out: " << uuid << std::endl;
        std::cout << "Exiting." << std::endl;
        exit(0);
    });

    // We don't need to specify the UUID if it's only one device anyway.
    // If there were multiple, we could specify it with:
    // dc.device(uint64_t uuid);
    Device &device = dc.device();
    std::shared_ptr<Action> action = std::make_shared<Action>(&device);
    std::shared_ptr<Mission> mission = std::make_shared<Mission>(&device);
    std::shared_ptr<Telemetry> telemetry = std::make_shared<Telemetry>(&device);

    while (!telemetry->health_all_ok()) {
        std::cout << "Waiting for device to be ready" << std::endl;
        sleep_for(seconds(1));
    }

    std::cout << "Device ready" << std::endl;

    // Import Mission items from QGC plan
    Mission::mission_items_t mission_items;
    Mission::Result import_res = Mission::import_qgroundcontrol_mission(mission_items, qgc_plan);
    handle_mission_err_exit(import_res, "Failed to import mission items: ");

    if (mission_items.size() == 0) {
        std::cerr << "No missions! Exiting..." << std::endl;
        exit(EXIT_FAILURE);
    }
    std::cout << "Found " << mission_items.size() << " mission items in the given QGC plan." <<
              std::endl;

    {
        std::cout << "Uploading mission..." << std::endl;
        // Wrap the asynchronous upload_mission function using std::future.
        auto prom = std::make_shared<std::promise<Mission::Result>>();
        auto future_result = prom->get_future();
        mission->upload_mission_async(
        mission_items, [prom](Mission::Result result) {
            prom->set_value(result);
        });

        const Mission::Result result = future_result.get();
        handle_mission_err_exit(result, "Mission upload failed: ");
        std::cout << "Mission uploaded." << std::endl;
    }

    std::cout << "Arming..." << std::endl;
    const Action::Result arm_result = action->arm();
    handle_action_err_exit(arm_result, "Arm failed: ");
    std::cout << "Armed." << std::endl;

    // Before starting the mission subscribe to the mission progress.
    mission->subscribe_progress(
    [](int current, int total) {
        std::cout << "Mission status update: " << current << " / " << total << std::endl;
    });

    {
        std::cout << "Starting mission." << std::endl;
        auto prom = std::make_shared<std::promise<Mission::Result>>();
        auto future_result = prom->get_future();
        mission->start_mission_async(
        [prom](Mission::Result result) {
            prom->set_value(result);
            std::cout << "Started mission." << std::endl;
        });

        const Mission::Result result = future_result.get();
        handle_mission_err_exit(result, "Mission start failed: ");
    }

    while (!mission->mission_finished()) {
        sleep_for(seconds(1));
    }

    // Wait for some time.
    sleep_for(seconds(5));

    {
        // Mission complete. Command RTL to go home.
        std::cout << "Commanding RTL..." << std::endl;
        const Action::Result result = action->return_to_launch();
        if (result != Action::Result::SUCCESS) {
            std::cout << "Failed to command RTL (" << Action::result_str(result) << ")" << std::endl;
        } else {
            std::cout << "Commanded RTL." << std::endl;
        }
    }

    return 0;
}

inline void handle_action_err_exit(Action::Result result, const std::string &message)
{
    if (result != Action::Result::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message << Action::result_str(
                      result) << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}

inline void handle_mission_err_exit(Mission::Result result, const std::string &message)
{
    if (result != Mission::Result::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message << Mission::result_str(
                      result) << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}

// Handles connection result
inline void handle_connection_err_exit(ConnectionResult result,
                                       const std::string &message)
{
    if (result != ConnectionResult::SUCCESS) {
        std::cerr << ERROR_CONSOLE_TEXT << message
                  << connection_result_str(result)
                  << NORMAL_CONSOLE_TEXT << std::endl;
        exit(EXIT_FAILURE);
    }
}
```

