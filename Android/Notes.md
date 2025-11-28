
## What does API stand for?

API stands for Application Programming Interface. In the context of APIs, the word Application refers to any software with a distinct function. Interface can be thought of as a contract of service between two applications. This contract defines how the two communicate with each other using requests and responses. Their API documentation contains information on how developers are to structure those requests and responses.


## How do APIs work?

API architecture is usually explained in terms of client and server. The application sending the request is called the client, and the application sending the response is called the server. So in the weather example, the bureau’s weather database is the server, and the mobile app is the client. 

There are four different ways that APIs can work depending on when and why they were created.

they are 
### SOAP APIs
### RPC APIs
### Websocket APIs
### REST APIs


**ANDROID ARCHITECTURE:**

![[Pasted image 20250804210921.png]]
### Applications

Applications is the top layer of android architecture. The pre-installed applications like home, contacts, camera, gallery etc and third party applications downloaded from the play store like chat applications, games etc. will be installed on this layer only. It runs within the Android run time with the help of the classes and services provided by the application framework.


### Application framework

The Application Framework is a core part of Android that gives developers the tools and services they need to build apps. It provides access to device features like hardware, screen display, and system resources. It includes several important services that make it easier to build powerful and consistent Android apps without having to create everything from scratch. The services are as follows:

1. Activity Manager - Manages the app’s activities and their life cycle (like opening, pausing, or closing screens).
2. Notification Manager - Allows apps to show alerts or updates to the user.
3. Package Manager - Keeps track of all the apps installed on the device.
4. Window Manager - Handles the placement and appearance of windows on the screen.
5. Content Providers - Help apps share data with other apps (like contacts or photos).
6. View System - Controls how things (like buttons or text) appear on the screen.


###  Application runtime

Android Runtime environment is one of the most important part of Android. It contains components like core libraries and the Dalvik virtual machine(DVM). Mainly, it provides the base for the application framework and powers our application with the help of the core libraries. Like Java Virtual Machine (JVM), ****Dalvik Virtual Machine (DVM)**** is a register-based virtual machine and specially designed and optimized for android to ensure that a device can run multiple instances efficiently. It depends on the layer Linux kernel for threading and low-level memory management. The core libraries enable us to implement android applications using the standard JAVA or Kotlin programming languages.

| Feature                | Description                                                              |
| ---------------------- | ------------------------------------------------------------------------ |
| **Memory Management**  | Allocates and frees memory while the app runs (e.g., garbage collection) |
| **Program Execution**  | Loads and runs the compiled or interpreted code                          |
| **System Interaction** | Handles input/output, files, and hardware communication                  |
| **Error Handling**     | Manages crashes, exceptions, and other runtime errors                    |
| **Security Checks**    | Ensures restricted access to system-level features                       |


###  Platform libraries

The Platform Libraries includes various C/C++ core libraries and Java based libraries such as Media, Graphics, Surface Manager, OpenGL etc. to provide a support for android development.

- ****Media**** library provides support to play and record an audio and video formats.
- ****Surface manager**** responsible for managing access to the display subsystem.
- ****SGL**** and ****OpenGL**** both cross-language, cross-platform application program interface (API) are used for 2D and 3D computer graphics.
- ****SQLite**** provides database support and ****FreeType**** provides font support.
- ****Web-Kit**** This open source web browser engine provides all the functionality to display web content and to simplify page loading.
- ****SSL (Secure Sockets Layer)**** is security technology to establish an encrypted link between a web server and a web browser.


###  Linux Kernel

Linux Kernel is heart of the android architecture. It manages all the available drivers such as display drivers, camera drivers, Bluetooth drivers, audio drivers, memory drivers, etc. which are required during the runtime. The Linux Kernel will provide an abstraction layer between the device hardware and the other components of android architecture. It is responsible for management of memory, power, devices etc. The features of Linux kernel are:

- ****Security:**** The Linux kernel handles the security between the application and the system.
- ****Memory Management:**** It efficiently handles the memory management thereby providing the freedom to develop our apps.
- ****Process Management:**** It manages the process well, allocates resources to processes whenever they need them.
- ****Network Stack:**** It effectively handles the network communication.
- ****Driver Model:**** It ensures that the application works properly on the device and hardware manufacturers responsible for building their drivers into the Linux build.

| Area                   | What it Does                                                                |
| ---------------------- | --------------------------------------------------------------------------- |
| **Process Management** | Manages running programs (called processes), including multitasking         |
| **Memory Management**  | Allocates RAM to different programs and keeps them from crashing each other |
| **Device Drivers**     | Talks to hardware (keyboard, mouse, disk, GPU, etc.)                        |
| **File System**        | Manages how data is stored and retrieved on disks                           |
| **Networking**         | Handles internet and local network communication                            |
| **Security**           | Controls user permissions, system calls, isolation, etc.                    |

### What is an **APK**?

**APK** stands for **Android Package Kit**. It’s the **file format used to distribute and install apps on Android devices**.

### `AndroidManifest.xml` 🗂️

- **What it is:** The **blueprint** of the app.

- **Purpose:**

- Declares app name, version, and permissions

- Declares components: activities, services, broadcast receivers, content providers

- Specifies min/max Android versions, themes, etc.

- ⚠️ This file is in **binary XML format** (not human-readable) unless decoded using tools like `apktool`

### `classes.dex` 📜

- **What it is:** The **compiled app code** in **Dalvik Executable (DEX)** format.
    
- **Purpose:**
    - Contains all your app's compiled Java/Kotlin classes

- Runs on the **ART (Android Runtime)** or older **Dalvik VM**

- 🔁 You can use `dex2jar` or `jadx` to reverse it into readable Java code.


### `res/` 🎨

- **What it is:** **Compiled resources** like XML layouts, images, colors, etc.
res/
  ├── layout/      → XML files for UI layout
  ├── drawable/    → Images and vector assets
  ├── values/      → Strings, colors, dimensions
  └── mipmap/      → Launcher icons
### `assets/` 📂

- **What it is:** Raw files bundled **without compilation**

| File Type                      | Description / Use Case                                                            |
| ------------------------------ | --------------------------------------------------------------------------------- |
| `.txt`, `.json`                | Configuration files, app settings, or static data                                 |
| `.html`, `.css`, `.js`         | Web content for WebView apps                                                      |
| `.db`, `.sqlite`               | Preloaded databases                                                               |
| `.mp3`, `.ogg`, `.wav`         | Audio files                                                                       |
| `.ttf`, `.otf`                 | Fonts used in the app                                                             |
| `.zip`, `.pdf`, `.csv`, `.xml` | Any raw data files used by the app logic                                          |
| **Custom folders**             | You can also organize files in subfolders (`assets/fonts/`, `assets/data/`, etc.) |

### `META-INF/` 🔐

- **What it is:** Cryptographic metadata
    
- **Contents:**
  META-INF/
  ├── CERT.RSA         → Public key certificate (signs the app)
  ├── CERT.SF          → Signature file with checksums
  └── MANIFEST.MF      → Lists SHA-1 checksums of all files

- - Ensures **app integrity and authenticity**
- Required for the system to **trust and install** the APK

---> content of the META_INF/

| File Name                         | Description                                                                                    |     |
| --------------------------------- | ---------------------------------------------------------------------------------------------- | --- |
| **`MANIFEST.MF`**                 | Lists all the files in the APK along with their **SHA-1 hashes**                               |     |
| **`CERT.SF`**                     | Contains the **hashes of the files listed in `MANIFEST.MF`**, and is signed with a private key |     |
| **`CERT.RSA`** (or `.DSA`, `.EC`) | The **digital certificate** containing the **public key** used to verify the APK’s signature   |     |

**Activity of the lifecyle:**

- onCreate: Called when the activity is first created; used for setting up the user interface, initializing variables, and loading data.
- onStart: Invoked when the activity becomes visible to the user.
- onResume: Triggered when the activity gains focus and the user can interact with it.
- onPause: Called when another activity comes in front (but this one is still partially visible); pause ongoing processes or save temporary data.
- onStop: Called when the activity is no longer visible to the user.
- onDestroy: The final call before the activity is removed from memory; used for cleanup and releasing resources.
## **Services**

### ➤ What is it?

A **Service** is a component that runs in the **background** to perform long-running operations without a user interface.

### ➤ Example:

- Playing music while using another app.

- Downloading files in the background.

- Checking for updates in the background.


## **Content Providers**

### ➤ What is it?

A **ContentProvider** manages **access to a structured set of data**. It acts as an interface between your app and data stored (often in a database or file system), and allows **data sharing** between apps.

### ➤ Example:

- Contacts provider gives access to phone contacts.

- Media provider gives access to images/videos.

- Your app can also create its own provider.

## What is a Broadcast Receiver?

A **BroadcastReceiver** is a component in Android that listens for **broadcast messages (intents)** from:

- The **system** (like battery low, boot completed, airplane mode change),
    
- Or **your app** (custom events).
    

It’s like a **background event listener** — it doesn’t show any UI, but it lets your app respond to changes in the device or other app behavior. 


## What is "Signature Permission"?

A **Signature permission** is a **high-level permission** in Android that allows **only apps signed with the same certificate** (as the declaring app) to access certain features or data.

## 📦 Why Signature Permission Exists

Sometimes, multiple apps are developed by the **same developer** (or company), and they want to **share sensitive functions or data** between these apps **securely**.

To ensure **no other external app** can access those functions or data, Android uses **signature-level protection**.



## exported=true:

In Android, `exported="true"` is an **attribute used in the manifest file** to indicate **whether a component (such as an activity, service, receiver, or provider) can be accessed by other apps**.

|`exported` value|Meaning|
|---|---|
|`true`|The component **can be launched** or accessed by other apps (outside your app).|
|`false`|The component is **internal-only**, and other apps **cannot access it**.|

### What is an **Intent** in Android?

An **Intent** is a **message object** that Android uses to request an action from another app component.

|Action|Intent Does|
|---|---|
|Open another screen|Start a new Activity|
|Send data|Pass data between Activities|
|Start camera|Launch camera app|
|Share text|Share content via other apps|
|Receive SMS|Notify your app via BroadcastReceiver|


