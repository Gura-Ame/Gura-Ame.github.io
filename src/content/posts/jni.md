---
title: How to Implement and Compile Java Native Methods Using JNI
published: 2025-06-29
description: A complete guide to declaring, implementing, and compiling native methods in Java using JNI and GCC.
tags: [Java, JNI, Native, GCC, Tutorial]
category: Programming
draft: false
---

### 1. Declaring a Native Method in a Java Class

In Java, use the `native` keyword to declare a method:

```java
package org.example;

public class Main {
    public static native void accessNative();

    public static void main(String[] args) {
        // Load the DLL (Windows) or .so (Linux/macOS) using the full path
        System.load("C:/full/path/to/org_example_Main.dll");  // Change to the actual path
        accessNative();
    }
}
```

---

### 2. Generating Header Files Using `javac -h`

Run the following command in the terminal to generate the `.h` header files:

```bash
javac -h . src/org/example/Main.java
```

> 🔧 The option `-h .` specifies that the `.h` files will be output to the current directory.

---

### 3. Creating and Implementing the C Source File

Create a `.c` source file, for example `org_example_Main.c`, and **copy the corresponding function declarations** from the `.h` header file generated earlier by `javac -h`.

For example, in `org_example_Main.h` you might see:

```c
/*
 * Class:     org_example_Main
 * Method:    accessNative
 * Signature: ()V
 */
JNIEXPORT void JNICALL Java_org_example_Main_accessNative
  (JNIEnv *, jclass);
```

Please copy this snippet into your `.c` file and **add parameter names** (otherwise it won't compile):

```c
#include <jni.h>
#include "org_example_Main.h"

JNIEXPORT void JNICALL Java_org_example_Main_accessNative
  (JNIEnv *env, jclass clazz) {
    // Native logic is implemented here
    return;
}
```

> 💡 **Tip**:
> - `JNIEnv *env` is the environment pointer provided by JNI, used to call Java methods, access fields, and more.
> - `jclass clazz` represents the calling class parameter for static methods; for instance methods, it is `jobject obj`.

---

### 4. Compiling to a Shared Library (DLL / SO) Using GCC

Use the appropriate command depending on your operating system:

#### ✅ Windows (Generating a `.dll`)

```bash
gcc -I"%JAVA_HOME%\include" ^
    -I"%JAVA_HOME%\include\win32" ^
    -shared -o org_example_Main.dll org_example_Main.c
```

>  PS: I have only tested this on `Windows`; the instructions for `Mac` and `Linux` were provided by ChatGPT.

#### ✅ Linux (Generating a `.so`)

```bash
gcc -I"$JAVA_HOME/include" \
    -I"$JAVA_HOME/include/linux" \
    -fPIC -shared -o org_example_Main.so org_example_Main.c
```

#### ✅ macOS (Generating a `.dylib`)

```bash
gcc -I"$JAVA_HOME/include" \
    -I"$JAVA_HOME/include/darwin" \
    -fPIC -dynamiclib -o org_example_Main.dylib org_example_Main.c
```

> ✅ Please make sure that `JAVA_HOME` is properly set and that `gcc` is installed on your system (on Windows, you can use MinGW).

---

### 🧯 Error Encountered: Can't load this .dll (machine code=0x14c)...

If you encounter the following error when running Java:

```
Can't load this .dll (machine code=0x14c) on a AMD 64-bit platform
```

This means the DLL you compiled is 32-bit (x86), while your JVM is 64-bit (x64), or vice versa.

✅ Please use the following command to check which architecture your `gcc` is:

```bash
gcc -dumpmachine
```

You should see output like this:

```
x86_64-w64-mingw32   ← indicates 64-bit
```

If you see something like this:

```
mingw32     ← indicates 32-bit ❌
```

It means you are using the wrong architecture compiler and should switch to the 64-bit version.

---

### ✅ Solution

You can use [MSYS2](https://www.msys2.org/) to install the 64-bit MinGW:

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc
```

Then open the `MSYS2 MinGW 64-bit` terminal (or add the ucrt-x86_64-gcc directory to your environment variables), and recompile your C file into a 64-bit `.dll` using the `gcc` in that environment:

```bash
gcc -I"$JAVA_HOME/include" \
    -I"$JAVA_HOME/include/win32" \
    -shared -o org_example_Main.dll org_example_Main.c
```

> ✅ Make sure the DLL architecture matches the JVM architecture to load and run smoothly!

---
