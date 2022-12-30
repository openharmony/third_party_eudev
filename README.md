# eudev
udev is a generic device manager running as a daemon on a Linux system and listening (through a netlink socket) for uevents the kernel sends out if a new device is initialized or a device is removed from the system. udev comes with a set of rules that match against exported values of the event and properties of the discovered device. A matching rule will name and create a device node and run configured programs to set up and configure the device.

Each time a change happens within the device structure, the kernel emits a uevent, which is obtained by the device manager. The device manager then follows the declared rules. Based on the information contained in the uevent, it finds the rule or rules it needs to trigger and performs the required actions. These actions may involve the creation or deletion of device files, and may also trigger the loading of particular firmware files into kernel memory.

## Directory Structure

```
README.md               Introduction in English
README_zh.md            Introduction in Chinese
COPYING                 Copyright file
hwdb/                   Hardware database file
rules.d/                Rule file
export_include/         Exported header file
src/                    C++ source code
test/                   Test file
man/                    Help file
```

## How OpenHarmony Integrates libudev
### 1. Importing Header File
```cpp
#include "libudev.h"
```
### 2. Adding Reference to BUILD.gn
```cpp
deps += [ "//third_party/eudev:libudev" ]
```
### 3. Example of Invoking the udev Function
```cpp
#include <stdio.h>
#include "libudev.h"

void test_device(struct udev *udev, const char *syspath)
{
    struct udev_device *device;
    printf("looking at device: %s\n", syspath);
    device = udev_device_new_from_syspath(udev, syspath);
    if (device == NULL) {
        printf("no device found\n");
        return;
    }
    printf("*** device: %p ***\n", device);
    const char *str = udev_device_get_devnode(device);
    if (str != NULL) {
        printf("devname:   '%s'\n", str);
    }
    dev_t devnum = udev_device_get_devnum(device);
    if (major(devnum) > 0) {
        printf("devnum:    %u:%u\n", major(devnum), minor(devnum));
    }
    udev_device_unref(device);
}

int main(int argc, char *argv[])
{
    struct udev *udev = udev_new();
    printf("udev context: %p\n", udev);
    if (udev == NULL) {
        printf("no context\n");
        return 1;
    }
    const char *path = "/sys/devices/virtual/mem/null";
    test_device(udev, path);
    udev_unref(udev);
    return 0;
}
```

## udev Document

Official document: https://github.com/eudev-project/eudev

## Copyright

See [COPYING](COPYING).
