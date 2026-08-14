# The Kernel

- An OS kernel is software

- The kernel is the core component of an OS

- It acts as a bridge, allowing the hardware to communicate with the software

- It allocates hardware resources—like deciding how much memory or processor time a program gets

- It runs in a special high-privilege mode (kernel mode) to safely manage the machine without letting normal user programs break the system

- Essentially, the kernel controls everything that happens on your system


# Common Kernel Types

- Monolithic Kernels: All operating system services run in one single block (used by Linux)

- Microkernels: Only the absolute bare-minimum tasks run at the core, while other services run separately (used by some embedded systems)

- Hybrid Kernels: A mix of both designs for a balance of speed and stability (used by Microsoft Windows and macOS)


# Linux History

- In 1969, Ken Thompson and Dennis Ritchie of Bell Laboratories developed the UNIX OS

- Over a decade later, Richard Stallman initiated the GNU (a recursive acronym for "GNU's Not UNIX") project

- The goal was to create a completely free and open-source UNIX-like operating system

- While the GNU project produced many essential components, including the GNU General Public License (GPL), its own kernel, named Hurd, was not completed in time

- In 1991, Linus Torvalds began developing a new kernel as a personal project

- This kernel, which we now know as the Linux Kernel, filled the missing piece of the GNU operating system

- The combination of the GNU tools and the Linux kernel created the complete, open-source operating system that is widely used today


# Linux Distros

- Linux if the kernel, not the OS

- While people often use "Linux" to describe the whole OS, the kernel is only one part of the system

- The complete OS built around the Linux kernel are called Linux Distributions, or Linux distros

- A Linux Distribution bundles the Linux kernel with system utilities, libraries, applications, and usually a package manager

- Many distros also include a desktop environment for graphical use