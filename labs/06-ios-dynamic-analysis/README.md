# iOS Dynamic Analysis

## Retain Cycle, Memory Issues

### ARC

Swift uses [*Automatic Reference Counting* (ARC)](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting/) to track and manage your app's memory usage.
In most cases, this means that memory management "just works” in Swift, and you don't need to think about memory management yourself.
ARC automatically frees up the memory used by class instances when those instances are no longer needed.

Here's an example of how Automatic Reference Counting works.
This example starts with a simple class called `Person`, which defines a stored constant property called `name`:

![1](https://github.com/user-attachments/assets/3cd60ce9-de20-473c-8e3c-c7241c07c7e2)


The `Person` class has an initializer that sets the instance's name property and prints a message to indicate that initialization is underway.
The `Person` class also has a deinitializer that prints a message when an instance of the class is deallocated.

The next code snippet defines three variables of type `Person?`, which are used to set up multiple references to a new `Person` instance in subsequent code snippets.
Because these variables are of an optional type (`Person?`, not `Person`), they're automatically initialized with a value of `nil`, and don't currently reference a `Person` instance.

```
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

You can now create a new `Person` instance and assign it to one of these three variables:

```
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

Note that the message *"John Appleseed is being initialized"* is printed at the point that you call the `Person` class's initializer.
This confirms that initialization has taken place.

Because the new `Person` instance has been assigned to the `reference1` variable, there's now a strong reference from `reference1` to the new `Person` instance.
Because there's at least one strong reference, ARC makes sure that this `Person` is kept in memory and isn't deallocated.

If you assign the same `Person` instance to two more variables, two more strong references to that instance are established:

```
reference2 = reference1
reference3 = reference1
```

There are now three strong references to this single `Person` instance.

If you break two of these strong references (including the original reference) by assigning nil to two of the variables, a single strong reference remains, and the `Person` instance isn't deallocated:

```
reference1 = nil
reference2 = nil
```

ARC doesn't deallocate the `Person` instance until the third and final strong reference is broken, at which point it's clear that you are no longer using the `Person` instance:

```
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

### Retain cycle

A memory leak in iOS is when an amount of allocated space in memory cannot be deallocated due to retain cycles. Since Swift uses [Automatic Reference Counting (ARC)](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting/), a retain cycle occurs when two or more objects hold strong references to each other. As a result these objects retain each other in memory because their retain count would never decrement to 0, which would prevent deinit from ever being called and memory from being freed.

### Strong Reference Cycles Between Class Instances

In the examples above, ARC is able to track the number of references to the new Person instance you create and to deallocate that Person instance when it's no longer needed.

However, it's possible to write code in which an instance of a class never gets to a point where it has zero strong references. This can happen if two class instances hold a strong reference to each other, such that each instance keeps the other alive. This is known as a strong reference cycle.

Please download the xcode project from [here](https://drive.google.com/file/d/1YOCovi0tCm2Op13vqn3PIPO3LpBCjTKu/view?usp=sharing) (or [here](https://drive.google.com/file/d/1ZjHWWGw52IOVScQiDbSTWT8AY2n8ZxS3/view?usp=sharing))  and run the app. (or [here](https://drive.google.com/file/d/1kFGyUeijSv6KkAp7q8RrFT70P22_OeUC/view?usp=drive_link))

Open the View1 inside the project. This contains an example that leads to a retain cycle. Run the app and press the buttons from the View1 in order to observe what is happening differently from the example above. Is deinit called?

What is happening after step 2 is that a strong reference cycle between Person and Apartment has been done. Thus, the Person instance now has a strong reference to the Apartment instance, and the Apartment instance has a strong reference to the Person instance. Therefore, when you break the strong references (step 3) held by the john and unit4A variables, the reference counts don't drop to zero, and the instances aren't deallocated by ARC.

![2](https://github.com/user-attachments/assets/b2419f90-cc9b-46dc-be51-22672e5a15fa)

Neither deinitializer was called when you set these two variables to nil. The strong reference cycle prevents the Person and Apartment instances from ever being deallocated, causing a memory leak in your app.
 
Here's how the strong references look after you set the john and unit4A variables to nil:

![3](https://github.com/user-attachments/assets/b40334c4-2620-445a-b7ba-7864af0f9bfc)

### Detecting Memory Leaks

#### Using the Instruments Tool

How can we know if there are memory leaks in our app? In the next section we will go over an introduction to Xcode's memory graph debugger and Instruments Developer Tool and how can we use them in order to detect memory leaks.

Please open Instruments from the Xcode menu:

![4](https://github.com/user-attachments/assets/88741540-8016-4228-a04a-a9297b68093c)


Select leaks from the menu below:

![5](https://github.com/user-attachments/assets/699f9cd1-e0cc-428c-9fb0-5787f66715f0)

Steps to configure before usage:
Select the simulator where you have runned the app
Select the app "RetainCycle” as the target

The Instruments tool can be used to diagnose memory leaks during application runtimes. After launching the application, you can see a live preview of how many resources are being consumed on the Allocations line and whether or not any memory leaks have been detected on the Leaks line.


After you've completed the steps above you can press on the record button in order to start the diagnose. Now go into the "Retain Cycle Example” view and play around.

![6](https://github.com/user-attachments/assets/58080e7d-fce6-4467-8cbc-9a2388bfcba7)



If leaks are detected, you'll be provided with detailed information including a stack trace and sometimes (unfortunately not always) a memory graph that visually presents how the retain cycle was created. It should be noted that the app can be used to track the runtime on both the simulator and physical device.

#### Using the Analyze tool

The Analyze tool is a function in Xcode for statically analyzing code after potential memory leaks. Once the analysis has finished running, the developer is provided with a list of suspicious places in the code.

![7](https://github.com/user-attachments/assets/11b418eb-faa9-4b09-8406-809b6f9833ad)

#### Memory Graph

Complementary with the tools above you can use memory graph. The image below is a classic example of a tell-tale memory leak sign. It shows entering and exiting an ARC view, whereupon exiting the memory should be released and on entering the resources should not be consumed at the same level as before. 

We can see from the image that the resources arrange themselves in a characteristic pyramid pattern and do not deallocate. So every time you enter View1 and press on steps 1 and 2 new class instances will be made, instances that would not be cleaned by ARC.

![8](https://github.com/user-attachments/assets/2a837da3-1761-4819-a5c5-764282b2c567)


#### Debug Memory Graph

Let's run the app again and try to find where the retain cycle is located.
Go into View1 and press on the 3 steps in order to simulate the cycle again.
Press on the button bellow in order to open the memory graph.

![9](https://github.com/user-attachments/assets/cefeefa6-8237-41f6-8aa1-c485a310fc9c)

Your application will stop. And your Xcode will look like this. Do you see something suspect?

![10](https://github.com/user-attachments/assets/cfeffa82-525f-4dff-9deb-5d7e5383c20b)

Press continue from the menu. Go back one view then come back and repeat the 3 steps again. After that open the graph again. 

![11](https://github.com/user-attachments/assets/ec531c26-d7b0-45df-9033-706fae5dffeb)

You should observe something like this:

![12](https://github.com/user-attachments/assets/e69229a1-5b4d-46e9-8ba1-7ff9d27dbcaa)


Numbers of Apartment and Person instances increased. Also we can observe from the graph that one references another. Thus this is our problem and we have a retain cycle between them. In the next part we will try to fix the issue.

### Fixing

Swift provides two ways to resolve strong reference cycles when you work with properties of class type: weak references and unowned references.

Weak and unowned references enable one instance in a reference cycle to refer to the other instance without keeping a strong hold on it. The instances can then refer to each other without creating a strong reference cycle.

Use a weak reference when the other instance has a shorter lifetime — that is, when the other instance can be deallocated first. In the Apartment example above, it's appropriate for an apartment to be able to have no tenant at some point in its lifetime, and so a weak reference is an appropriate way to break the reference cycle in this case. In contrast, use an unowned reference when the other instance has the same lifetime or a longer lifetime.

Open the View2 swift file from the project. 


 A weak reference is a reference that doesn't keep a strong hold on the instance it refers to, and so doesn't stop ARC from disposing of the referenced instance. This behavior prevents the reference from becoming part of a strong reference cycle. You indicate a weak reference by placing the weak keyword before a property or variable declaration.

Because a weak reference doesn't keep a strong hold on the instance it refers to, it's possible for that instance to be deallocated while the weak reference is still referring to it. Therefore, ARC automatically sets a weak reference to nil when the instance that it refers to is deallocated. And, because weak references need to allow their value to be changed to nil at runtime, they're always declared as variables, rather than constants, of an optional type.

You can check for the existence of a value in the weak reference, just like any other optional value, and you will never end up with a reference to an invalid instance that no longer exists.

Here's how the references look now that you've linked the two instances together:

![13](https://github.com/user-attachments/assets/6888f73f-7aec-4f08-8060-332cdcc78a41)

The Person instance still has a strong reference to the Apartment instance, but the Apartment instance now has a weak reference to the Person instance. This means that when you break the strong reference held by the john variable by setting it to nil, there are no more strong references to the Person instance.


Because there are no more strong references to the Person instance, it's deallocated and the tenant property is set to nil. 

![14](https://github.com/user-attachments/assets/c25280ab-da24-4260-ac2f-db094e37fb18)

The only remaining strong reference to the Apartment instance is from the unit4A variable. If you break that strong reference, there are no more strong references to the Apartment instance. Because there are no more strong references to the Apartment instance, it too is deallocated.

![15](https://github.com/user-attachments/assets/e261db6a-e0ee-484e-9dd2-4815744ab9c1)

Run the app again and navigate inside the view containing the solution. Test with the tools again. Can you find the cycle after step 3 is pressed? Is deinit called?

### Other fix solution with "unowned”

Like a weak reference, an unowned reference does not keep a stronghold on the instance it refers to (means it can use to break the retain cycle). Unlike a weak reference, however, an unowned reference is used when the other instance has the same lifetime or a longer lifetime.

Use weak if you are not sure your capture self in a closure may or may not available in some cases.
Use unowned if you are 100% sure that self will be available when you're closure executes.

More details [here](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting/#Unowned-References).

### Other examples of retain cycles

You saw above how a strong reference cycle can be created when two class instance properties hold a strong reference to each other. You also saw how to use weak and unowned references to break these strong reference cycles.

A strong reference cycle can also occur if you assign a closure to a property of a class instance, and the body of that closure captures the instance. This capture might occur because the closure's body accesses a property of the instance, such as self.someProperty, or because the closure calls a method on the instance, such as self.someMethod(). In either case, these accesses cause the closure to "capture” self, creating a strong reference cycle.

![16](https://github.com/user-attachments/assets/1e80faf1-d4bd-4169-bfb9-1107f2a67776)

![17](https://github.com/user-attachments/assets/92300a34-cc98-4c4c-a014-305d891d204d)

### Why are memory leaks a problem?

Memory leaks increase the memory footprint incrementally in your app, and when it reaches a certain threshold the operating system (iOS) this triggers a memory warning. If that memory warning is not handled, your app would be force-killed, which is an OOM (Out of memory) crash. As you can see, memory leaks can be very problematic if a substantial leak occurs because after using your app for a period of time, the app would crash.

![18](https://github.com/user-attachments/assets/f14a6d0c-6cc8-492e-99af-7f1d0cbd6cb9)

In addition, memory leaks can introduce side effects in your app. Typically this happens when observers are retained in memory when they should have been deallocated. These leaked observers would still listen to notifications and when triggered the app would be prone to unpredictable behaviors or crashes. 

## Binary App Analysis

Binary app analysis requires doing both static and dynamic analysis.

For static analysis, you can use the common tools on macOS such as plutil, otool and others. For non-macOS devices, you can use [Ghidra](https://ghidra-sre.org/).

For dynamic analysis, an excellent tool, used for both Android and iOS devices is [Frida](https://github.com/june5079/soFrida). [This](https://infosecwriteups.com/unlocking-potential-exploring-frida-objection-on-non-jailbroken-devices-without-application-ed0367a84f07) is a tutorial on using Frida on non-jailbroken iOS devices.

### Task

Go through the [iOS Pentesting Guide](https://book.hacktricks.wiki/en/mobile-pentesting/ios-pentesting/index.html) and check all tools that can be used for security assessment of iOS applications.

## Kernel Analysis

### Background for iOS emulation

While MacOS can run on an intel processor, thus abiding to x86 architecture such as privilege rings 0-3, 0 being the most privileged, iOS is built to run only on an ARM architecture. This puts up some difficulties for hackers and security researchers alike. However, despite these challenges, a team managed to achieve a quite comprehensive emulation of iOS 14 [(Project status · TrungNguyen1909/qemu-t8030 Wiki · GitHub)](https://github.com/TrungNguyen1909/qemu-t8030/wiki/Project-status).

### Privilege rings, x86 vs ARM

As stated, the iOS kernel doesn't use rings 0-3, but EL0 - EL2, (EL = elevation level) and GL2 with GL1 which are lateral levels for elevation levels. As the name suggests, they are guarded levels for increased separation of privilege.

![19](https://github.com/user-attachments/assets/e18e267d-3c85-406a-af66-6cf166ac87b9)

Figure extracted from blackhat presentation.

The team even managed to patch qemu in order to emulate even the GENTER and GEXIT instructions respectively which are needed to enter and exit the guarded levels.

Sadly, this project is archived and no longer maintained, however, if built right, it can still be used to emulate an iPhone 14, the best part being that it's open-source and it works on Linux too since it leverages qemu.

### Building from scratch

If one would want to build the environment from scratch, these are the steps:
Tip: you can skip to the next point if you get the VM provided.
The setup was done using Vmware Workstation 17.5. Note: if the host does not support VT-x or AMD-V and/or it was not enabled in the Processor settings of the VM, "-enable-kvm” can be removed if it throws errors, yet the VM will be slower.

1. Create an ubuntu VM, in this case 22.04.4 LTS was used.
* Use a sufficient amount of space, 80GB should be enough.
2. Inside the ubuntu VM which will be the host, follow the instructions listed here: [USB and Restore · TrungNguyen1909/qemu-t8030 Wiki · GitHub](https://github.com/TrungNguyen1909/qemu-t8030/wiki/USB-and-Restore)
* An ubuntu server was used in this case (22.04.4).
* When installed, this command was used: ```./qemu-system-x86_64 -enable-kvm -cdrom ubuntu-22.04.4-live-server-amd64.iso -m 4096 -vga virtio -smp 4 -device usb-ehci,id=ehci -device usb-tcp-remote,bus=ehci.0```
* Tips for installing patched idevicerestore:
	Libimobiledevice-glue-dev will be needed to be installed from source
Which will require libplist to be installed from source
Before compiling and installing idevicerestore, make the changes presented in this issue: [Linux VM couldn't find the emulated device · Issue #55 · TrungNguyen1909/qemu-t8030 · GitHub](https://github.com/TrungNguyen1909/qemu-t8030/issues/55) 
After idevicerestore finally compiles and is installed, it may throw an error that a library is not found. To fix this, add `/usr/local/lib/` line to `/etc/ld.so.conf.d/mylibs.conf`
* When started, this command was used: ```../qemu-t8030/build/qemu-system-x86_64 -enable-kvm -boot order=c -m 4096 -vga none -device virtio-vga,xres=640,yres=480 -smp 4 -usb -device usb-ehci,id=ehci -device usb-tcp-remote,bus=ehci.0 -drive file=ubu.qcow2 -monitor telnet:127.0.0.1:1236,server,nowait```
3. Follow the instructions here [Bringing up the emulator · TrungNguyen1909/qemu-t8030 Wiki · GitHub](https://github.com/TrungNguyen1909/qemu-t8030/wiki/Bringing-up-the-emulator) and DO NOT START the emulator first, always start the linux companion first.
4. You can use gdb to attach to the running kernel since qemu provides a gdb stub

### Getting the provided VM

[Ioslab2.7z](https://ctipub-my.sharepoint.com/:u:/g/personal/andrei_sorescu_stud_etti_upb_ro/EdESkbtQNOFKojjNIU-LRPMBwh3zkAutOPMu0S2kBxvxKg?e=9lubKN)

Pass: ioslab2

Since building it from scratch may take too much time, a VM is provided. The VM was built using Vmware Workstation 17.5, as such, request a trial for vmware workstation and open the provided VM. (it may work with Vmware Player as well)

VM user: ioslab2

VM pass:ioslab2


In order to attach to the running kernel of the emulated iPhone, the following steps are required:

1. Open a terminal and cd ~/linux_companion
* Start the linux companion by typing `./start_vm.sh`.
* Connect to the VNC by opening another terminal and starting `vinagre`, pressing connect and typing `127.0.0.1:5900`.
* Wait for it to boot.
* Linux companion user: lin_co
* Linux companion pass: linco

![20](https://github.com/user-attachments/assets/2bf5eeb6-b373-4994-82ea-a9be2b98011f)
![21](https://github.com/user-attachments/assets/cc26131d-63fe-43ce-a842-f8374ca98c96)

2. Open another terminal tab and `cd ~/iphone`.
* Type `./run.sh -snapshot` which will start the emulated iPhone.
* There is no need to connect to the VNC for this one.
3. Open yet another terminal tab and type `gdb-multiarch`.
* Inside gdb type `target remote localhost:1234`.
* This should break wherever the execution was. Beware that there are no symbols or mappings accessible.
* Gdb can be used as usual by issuing `c` to continue the execution or `^c` (Ctrl-c) to break into the debugger. [GDB-cheat-sheet.pdf (gabriellesc.github.io)](https://gabriellesc.github.io/teaching/resources/GDB-cheat-sheet.pdf)
4. Feel free to attach the debugger earlier in the execution and observe the pause of the booting process in the other terminal tab as well as breaking into the debugger at random times to observe the program counter pointing into kernel space or into user space. Hint: ARM has the same convention for canonical addresses as x86.
5. BONUS: try to modify register values or memory pointed by register values or addresses from the stack, does the kernel panics? Is it visible in the other terminal window?


Useful links:

https://github.com/TrungNguyen1909/qemu-t8030

https://www.blackhat.com/us-22/briefings/schedule/#truemu-an-extensible-open-source-whole-system-ios-emulator-27223

TruEMU: An Extensible, Open-Source, Whole-System iOS Emulator - https://www.youtube.com/watch?v=PBeIW2PDFVk&ab_channel=BlackHat

## Challenges

Use static analysis ( plistutil, Ghidra, and other tools presented in the iOS Pentesting Tutorial and  solve the following challenges:

[1] ~ ~Great Success - https://ctftime.org/writeup/24406~ ~  (unfortunatelly the .ipa file is gone :( but let us know if you find it)

[2] AVL Bank - https://github.com/avltree9798/iOS-CTF

[3] MemGraph CTF - https://developer.apple.com/news/?id=keebiiyl -> [hint 💡](https://developer.apple.com/news/?id=3st92oup)
