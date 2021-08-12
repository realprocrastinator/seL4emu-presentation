presentation layout

1. welcome 
2. what is sel4
   1. what kernel models we have? micro monolithic hybrid
   2. what is micro kernel? and why?

3. since micro kernel is good but what about developing on the microkernel?

4. problem defined -> we have a lot of primitives, developing involves using tools and as simple as possible

5. give out my motivation of this thesis & project. Why we  need this?

6. How do we think about this problem? - an aspect of virtualization on different layers

7. - ISA -- existing solution: 

     - QEMU or standard platform virtualisation (latter leads to recursive virtualisation if seL4 wants to run in hypervisor mode, see Turtles)
     - But worst performance

   - Kernel ABI approach

     - redirect traps to a signal handler or use PTRACE (explain trade-offs)

   - syscall library API:

     -  best performance, saves redundant kernel entries

         source-code but not binary compatible

         should be architecture-neutral

8. - Introduing my approach 
   - Source code aompatible
   - Binary compatible

9. Introducing how what will be the criteria