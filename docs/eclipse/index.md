# Eclipse IDE

The ARC GNU Eclipse IDE consists of the Eclipse IDE combined with an Eclipse
CDT Managed Build Extension plug-in for the ARC GNU Toolchain and GDB embedded
debugger plug-in for ARC, based on the Zylin Embedded CDT plug-in.  The ARC GNU
IDE supports the development of managed C/C++ applications for ARC processors
using the ARC GNU toolchain for bare metal applications (elf32).

The ARC GNU IDE provides support for the following functionality:

* Support for the ARC EM, ARC HS, ARC 600 and ARC 700 Processors
* Support for little and big endian configurations
* Ability to create C/C++ projects using the ARC elf32 cross-compilation
  toolchain
* Configuration of toolchain parameters per project
* Configuration of individual options (such as preprocessor, optimization,
  warnings, libraries, and debugging levels) for each toolchain component:

    * GCC Compiler
    * GDB Debugger
    * GAS assembler
    * Size binutils utility, etc.

* Support for MIPS ARC EM Starter Kit and AXS10x.
* Configuration of debug and run configurations for supported FPGA Development
  Systems and debug probes (Digilent HS1/HS2 or Ashling Opella-XD).
* GDB-based debugging using **Debug** perspective providing detailed debug
  information (including breakpoints, variables, registers, and disassembly)

ARC GNU plugins for Eclipse have following requirements to the system:

* OS: Windows 10, Ubuntu Linux 22.04 LTS, Ubuntu Linux 24.04 LTS and CentOS 8 development host systems
* Eclipse 2018-12 (part of Windows installer)
* CDT version 9.6.0 (part of Windows installer)
* Java VM version >= 1.8 is required (part of Windows installer)
