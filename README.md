# STM32H747I_DISCO_Cpp_Scratch
No HAL even startup everything from scratch

First need to understand Assembly directives to write a startup file..

.syntax unified
By default, the assembler begins assembling all instructions in a file as 32-bit instructions. You can change the default action by using .syntax  [unified | divided] unified having both ARM and Thumb state.

.cpu cortex-m4
Select the target processor

.fpu softvfp
Select the floating-point unit, .fpu softvfp use s/w floating point unit .fpu fpv4-sp-d16 use h/w floating point 

.thumb
performs 16bit instructions

.global  g_pfnVectors
It makes a symbol visible to the linker, so other files (like C code) can access it.

g_pfnVectors is usually the vector table for Cortex-M processors.
It contains:
Initial stack pointer (first entry)
Reset handler address
Other exception and interrupt handlers

.global  Default_Handler

Cortex-M requires every interrupt vector to have a valid address.
If an interrupt occurs and its handler is missing, the processor jumps to Default_Handler, preventing undefined behavior.
Typically, Default_Handler loops indefinitely


.word  _sidata
.word is a 32bit wide 

_sidata it’s a symbol defined in the linker script.
Purpose: holds the initial values of the .data section in Flash.
.data section contains initialized global and static variables, which need to be copied from Flash to RAM on startup.

.word  _sdata

_sdata is a symbol defined in the linker script.
It marks the start address of the .data section in RAM.
.data section contains initialized global and static variables.

in c like 
int counter=10;      // Stored in .data
static int flag=10;  // Stored in .data

On reset, the Cortex-M processor does not automatically initialize RAM.
So the startup code must:
Copy initial values from Flash (_sidata)
Into RAM starting at _sdata

.word  _edata
edata is a symbol defined in the linker script.
It marks the end address of the .data section in RAM.
The .data section contains initialized global and static variables.

.word  _sbss
_sbss is a symbol defined in the linker script.
It marks the start address of the .bss section in RAM.
The .bss section contains uninitialized global and static variables, which should start as zero.

in c like 
int counter;      // Stored in .bss, initially zero
static int flag;  // Stored in .bss, initially zero

.word  _ebss
_ebss is a symbol defined in the linker script.
It marks the end address of the .bss section in RAM.
The .bss section contains uninitialized global and static variables, which should be zeroed at startup.
