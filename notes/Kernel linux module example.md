### Before start
Need to install linux headers and other utils
```shell
sudo apt install build-essential linux-headers-$(uname -r)
```

### Example of simple char device
```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/fs.h>
#include <linux/device.h>
#include <linux/uaccess.h>

#define DEVICE_NAME "hello_device"
#define CLASS_NAME "hello_class"
#define HELLO_STRING "Hello device\n"

static int major_number;
static struct class* hello_class = NULL;
static struct device* hello_device = NULL;

static int device_open(struct inode *inode, struct file *file) { return 0; }
static int device_release(struct inode *inode, struct file *file) { return 0; }

static ssize_t device_read(struct file *file, char __user *buffer, size_t len, loff_t *offset) {
    size_t hello_len = strlen(HELLO_STRING);

    if (*offset >= hello_len) return 0;
    if (len > hello_len - *offset) len = hello_len - *offset;

    if (copy_to_user(buffer, HELLO_STRING + *offset, len)) return -EFAULT;

    *offset += len;
    return len;
}

static struct file_operations fops = {
    .read = device_read,
    .open = device_open,
    .release = device_release,
};

// Create modul in system
static int __init hello_init(void) {
    // Регістрація символічного пристрою
    major_number = register_chrdev(0, DEVICE_NAME, &fops);
    if (major_number < 0) {
        printk(KERN_ALERT "Hello device failed to register a major number\n");
        return major_number;
    }

    // Створення класу пристрою
    hello_class = class_create(THIS_MODULE, CLASS_NAME);
    if (IS_ERR(hello_class)) {
        unregister_chrdev(major_number, DEVICE_NAME);
        printk(KERN_ALERT "Failed to register device class\n");
        return PTR_ERR(hello_class);
    }

    // Створення пристрою
    hello_device = device_create(hello_class, NULL, MKDEV(major_number, 0), NULL, DEVICE_NAME);
    if (IS_ERR(hello_device)) {
        class_destroy(hello_class);
        unregister_chrdev(major_number, DEVICE_NAME);
        printk(KERN_ALERT "Failed to create the device\n");
        return PTR_ERR(hello_device);
    }

    printk(KERN_INFO "Hello device created successfully\n");
    return 0;
}

// Delete model from system
static void __exit hello_exit(void) {
    device_destroy(hello_class, MKDEV(major_number, 0));
    class_unregister(hello_class);
    class_destroy(hello_class);
    unregister_chrdev(major_number, DEVICE_NAME);
    printk(KERN_INFO "Hello device unregistered\n");
}

module_init(hello_init);
module_exit(hello_exit);

// modul info
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Viktor Viskov");
MODULE_DESCRIPTION("A simple Linux char device that returns a string");
MODULE_VERSION("1.0");
```

### Example of Makefile
```Makefile
obj-m += hello_device.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean

```
#### Insert module
```bash
sudo insmod hello_device.ko
```

#### Right permitions
```shell
sudo chmod 666 /dev/hello_device2
```


#### Remove module
```shell
sudo rmmod hello_device
```

#### Add to run persistant

1. Add module name to `/etc/modules`   
```shell
hello_module
```
2. Copy module to `/lib/modules/$(uname -r)/`
```shell
sudo cp hello_device.ko /lib/modules/$(uname -r)/
```
3. Update list with modules
```shell
sudo depmod
```

==After kernel update need to do step **2** and **3** again==