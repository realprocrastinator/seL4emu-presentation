# Thesis Statement

## Thesis topic

(An/Two) approach(es) to develop and evaluate emulation seL4 of Linux OS.

## Description of the topic

In this research project, we are going to find a way to emulate the seL4 kernel so that seL4 applications can run on Linux-based operating systems with source code recompile. The final product will be an emulation layer that allows seL4 applications to run directly on Linux-based operating systems without having to resort to hardware virtualization. This provides many advantages, such as enabling the use of Linux debugging and profiling tools for the emulated seL4 applications.

## Motivation

- Providing an easy way for the seL4 application developer to do rapid prototyping in a Linux environment. The unmodified seL4 application source code can be targeted to the seL4 Linux emulation environment in addition to targeting the real seL4 environment.

- Enables seL4 applications to run on Linux, which enables us to:

- - Leverage Linux tools for debugging. Although it is possible to use hardware virtualization to debug seL4 applications such as by using Qemu, debuggers are often confused because the stack frames might be occupied by different code caused by context switching done by the seL4 kernel, which typically results in breakpoints unexpectedly triggered or missed and wrong values on the inspected variables.
  - Access to the rich inputs and outputs provided by the host's Linux system, such as files and networking, without having to develop a device-specific driver first.

## Survey the literature

### Existing solution

Qemu provides a hardware virtualization feature, allowing arbitrary operating systems to run safely in a host system without affecting the host system. Qemu also provides a debugging interface (GDB server). However, debugging under such interfaces is typically difficult because of the guest operating system's context switching. Hardware virtualizations will also unquestionably creates an overhead.

### Related Work

- Cygwin provides a method to run Unix applications on top of Windows natively. This is achieved by compiling Linux application source code under Cygwin with minimal modification. Cygwin provides a dynamic-link library as an API compatibility layer. Inspired by this idea, the first approach will provide the emulation at the seL4 syscall layer. 

## Options of this project

To achieve the emulation functionalities the following approaches can be considered:

**Approach 1:**

- Providing an alternative seL4 syscall wrapper libraries that can be linked to seL4 client apps. Instead of invoking the real seL4 syscalls, this wrapper library will instead dispatch the syscall request to a server app that behaves like a seL4 kernel and provides seL4 syscall semantics.

- The model is as below:

  ![](D:\MyOnedrive\OneDrive - UNSW\UNSW CS\9991_Research\presentation\model1.svg)

- The above figure illustrates the main program flow of this approach. A seL4 client app can be linked with the libseL4 emulation layer called libseL4-emu and other helper libraries. Assuming that the seL4 client app exclusively uses the libseL4 library to interact with the seL4 kernel, the application will proceed to perform as expected even though it is running as a regular Linux process since the libseL4-emu provides the same semantics as the real libseL4 library running on the real seL4 kernel.

- The emulation of this approach uses a client-server model. The libseL4-emu on the seL4 client app side acts as a client. And the seL4 kernel emulator on the other side acts like a server. The client and the server communicates using UDS (Unix Domain Socket) and other IPC mechanisms provided by Linux. The kernel emulator provides the emulation of seL4 capability space, IPC, and scheduling context management. 

**Pros**

- Relatively easy to implement.

- Emulation is fast as:

- - All user-mode codes are run as-is without any sort of virtualization or emulation.
  - seL4 syscalls will only invoke regular Unix socket communications, which is also very performant.
  - No emulation is involved in the seL4 kernel emulator. The seL4 kernel emulator is just a regular Linux application implementing the seL4 semantics.

**Cons**

- No binary compatibility: seL4 applications that wish to be emulated have to be recompiled from their source code.

**Approach 2** (Might be a future work if only manage to do approach one)

- Based on ptrace (TODO)

**Pros**

- Binary compatibility. Emulated seL4 applications can be run as-is directly on a Linux system without recompilation.

**Cons**

- IPC heavy seL4 applications will be slower due to the performance overhead involved in trapping syscalls by ptrace.
- Have to provide our debugging interface as at most one process can ptrace a single process. Most Linux debuggers also utilise ptrace.