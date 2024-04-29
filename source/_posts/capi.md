---
title: capi
date: 2024-04-29 21:51:45
tags:
---

# install

[user guide links](https://usermanual.wiki/Document/CommonAPICppUserGuide.1126244679/view)

## GitHub source code repo links

the step 3 depends on the step 1

the step 1 need to install boost library first like following

```bash
$ sudo apt-get install libboost-all-dev

```

1. [vsomeip](https://github.com/COVESA/vsomeip)
2. [capicxx-core-runtime](https://github.com/GENIVI/capicxx-core-runtime)
3. [capicxx-someip-runtime](https://github.com/GENIVI/capicxx-someip-runtime)

## code generate tools

this code generate tools depend on Java environment

```bash
$ sudo apt-get install openjdk-17-jdk

```

1. [CommonAPI core header generate](https://github.com/GENIVI/capicxx-core-tools)
2. [CommonAPI source code generate](https://github.com/GENIVI/capicxx-someip-tools)

# run helloworld

## big three

1. json config file

```json
{
    "unicast":"local",
    "logging":
    {
        "level":"debug",
        "console":"true"
    },

    "applications":
    [
        {
            "name":"HelloWorldService",
            "id":"0x4444"
        },

        {
            "name":"HelloWorldClient",
            "id":"0x5555"
        }
    ],

    "services":
    [
        {
            "service":"0x1111",
            "instance":"0x2222",
            "unreliable":"30509"
        }
    ],

    "routing":"HelloWorldService",
    "service-discovery":
    {
        "enable":"false"
    }
}

```

1. HelloWorld.fidl

```
package commonapi
interface HelloWorld {
  version {major 1 minor 0}
  method sayHello {
    in {
      String name
    }
    out {
      String message
    }
  }
}

```

1. HelloWorld.fdepl

```
import "platform:/plugin/org.genivi.commonapi.someip/deployment/CommonAPI-SOMEIP_deployment_spec.fdepl"
import "HelloWorld.fidl"
define org.genivi.commonapi.someip.deployment for interface commonapi.HelloWorld {
    SomeIpServiceID = 4660

    method sayHello {
        SomeIpMethodID = 3300
    }
}

define org.genivi.commonapi.someip.deployment for provider as MyService {
    instance commonapi.HelloWorld {
        InstanceId = "test"
        SomeIpInstanceID = 2200
    }
}

```

## then generate code

```bash
# ➜  helloworld ../commonapi_core_generator/commonapi-core-generator-linux-x86_64 -sk ./fidl/HelloWorld.fidl
➜  helloworld ../commonapi_someip_generator/commonapi-someip-generator-linux-x86_64 -ll verbos ./fidl/HelloWorld.fdepl

```

## write your code

1. HelloWorldStubImpl.hpp

```cpp
#ifndef HELLOWORLDSTUBIMPL_H_
#define HELLOWORLDSTUBIMPL_H_

#include <CommonAPI/CommonAPI.hpp>
#include <v1/commonapi/HelloWorldStubDefault.hpp>

class HelloWorldStubImpl: public v1::commonapi::HelloWorldStubDefault {
public:
    HelloWorldStubImpl();
    virtual ~HelloWorldStubImpl();
    virtual void sayHello(const std::shared_ptr<CommonAPI::ClientId> _client, std::string _name, sayHelloReply_t _return);
};

#endif /* HELLOWORLDSTUBIMPL_H_ */

```

1. HelloWorldStubImpl.cpp

```cpp
#include "HelloWorldStubImpl.hpp"

HelloWorldStubImpl::HelloWorldStubImpl() { }
HelloWorldStubImpl::~HelloWorldStubImpl() { }

void HelloWorldStubImpl::sayHello(const std::shared_ptr<CommonAPI::ClientId> _client, std::string _name, sayHelloReply_t _reply) {
    std::stringstream messageStream;
    messageStream << "Hello " << _name << "!";
    std::cout << "sayHello('" << _name << "'): '" << messageStream.str() << "'\\n";

    _reply(messageStream.str());
};

```

1. HelloWorldService.cpp

```cpp
#include <iostream>
#include <thread>
#include <CommonAPI/CommonAPI.hpp>
#include "HelloWorldStubImpl.hpp"

using namespace std;

int main() {
    std::shared_ptr<CommonAPI::Runtime> runtime = CommonAPI::Runtime::get();
    std::shared_ptr<HelloWorldStubImpl> myService = std::make_shared<HelloWorldStubImpl>();
    bool res = runtime->registerService("local", "test", myService);
    // bool res = runtime->registerService("192.168.43.133", "test", myService);
    std::cout << "Successfully Registered Service!" << std::endl;

    while (true) {
        std::cout << "Waiting for calls... (Abort with CTRL+C)" << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(30));
    }

    return 0;
}

```

1. HelloWorldClient.cpp

```cpp
#include <iostream>
#include <string>
#include <unistd.h>
#include <CommonAPI/CommonAPI.hpp>
#include <v1/commonapi/HelloWorldProxy.hpp>

using namespace v1::commonapi;

int main() {
    std::shared_ptr < CommonAPI::Runtime > runtime = CommonAPI::Runtime::get();
    std::shared_ptr<HelloWorldProxy<>> myProxy = runtime->buildProxy<HelloWorldProxy>("local", "test");
    // std::shared_ptr<HelloWorldProxy<>> myProxy = runtime->buildProxy<HelloWorldProxy>("192.168.43.133", "test");

    std::cout << "Checking availability!" << std::endl;
    while (!myProxy->isAvailable())
        usleep(10);
    std::cout << "Available..." << std::endl;

    CommonAPI::CallStatus callStatus;
    std::string returnMessage;

    while (true) {
        std::this_thread::sleep_for(std::chrono::seconds(1));
        myProxy->sayHello("xiepeng", callStatus, returnMessage);
        std::cout << "Got message: '" << returnMessage << "'\\n";
    }
    return 0;
}

```

1. cmake

```
cmake_minimum_required(VERSION 2.8)

project(helloworld)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread -std=c++0x")

include_directories(
    /home/helianthus/dev_tools/helloworld/src-gen
    /home/helianthus/dev_tools/capicxx-core-runtime-3.2.0/include
    /home/helianthus/dev_tools/capicxx-someip-runtime-3.2.0/include
    /home/helianthus/dev_tools/vsomeip-3.1.20.3/interface
)

link_directories(
    /home/helianthus/dev_tools/capicxx-core-runtime-3.2.0/build
    /home/helianthus/dev_tools/capicxx-someip-runtime-3.2.0/build
    /home/helianthus/dev_tools/vsomeip-3.1.20.3/build
)

add_executable(HelloWorldClient
    /home/helianthus/dev_tools/helloworld/HelloWorldClient.cpp
    /home/helianthus/dev_tools/helloworld/src-gen/v1/commonapi/HelloWorldSomeIPProxy.cpp
    /home/helianthus/dev_tools/helloworld/src-gen/v1/commonapi/HelloWorldSomeIPDeployment.cpp
)
target_link_libraries(HelloWorldClient CommonAPI CommonAPI-SomeIP vsomeip3)

add_executable(HelloWorldService
    /home/helianthus/dev_tools/helloworld/HelloWorldService.cpp
    /home/helianthus/dev_tools/helloworld/HelloWorldStubImpl.cpp
    /home/helianthus/dev_tools/helloworld/src-gen/v1/commonapi/HelloWorldSomeIPStubAdapter.cpp
    /home/helianthus/dev_tools/helloworld/src-gen/v1/commonapi/HelloWorldStubDefault.hpp
    /home/helianthus/dev_tools/helloworld/src-gen/v1/commonapi/HelloWorldSomeIPDeployment.cpp
)
target_link_libraries(HelloWorldService CommonAPI CommonAPI-SomeIP vsomeip3)

```

# 踩的几个坑

1. fidl 文件高版本有变动 ～～define org.genivi.commonapi.someip.deployment for provider MyService {～～应该为 define org.genivi.commonapi.someip.deployment for provider as MyService {
2. sudo ldconfig 会报 Configuration module could not be loaded! 其实是动态库的问题
3. vsomeip ld 找不到，高版本 cmake 要链接 vsomeip3
