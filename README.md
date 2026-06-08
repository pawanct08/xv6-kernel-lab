# xv6 Kernel Lab

**Author:** Pawan ([pawanct08](https://github.com/pawanct08))

Personal kernel lab based on [xv6-riscv](https://github.com/mit-pdos/xv6-riscv).
Extended with custom syscalls, a modified scheduler, and memory introspection — used as a hands-on foundation before Linux driver development.

## Goals

- Understand process lifecycle, trap handling, and context switching at the assembly level
- Implement custom syscalls to explore the user-kernel boundary
- Modify the scheduler to support static priority-based scheduling
- Map every xv6 concept to its Linux kernel equivalent

## Build & Run

Requires RISC-V toolchain and QEMU:

```bash
# Ubuntu/Debian
sudo apt install build-essential gdb-multiarch qemu-system-misc gcc-riscv64-unknown-elf binutils-riscv64-unknown-elf

make
make qemu        # boots xv6 in QEMU
# Ctrl-A X to exit QEMU
```

## Modifications

| Change | File(s) | Linux Equivalent |
|--------|---------|-----------------|
| `getproccount()` syscall — returns number of active processes | `kernel/sysproc.c`, `kernel/proc.c` | `proc_root_link` / `/proc` entries in the VFS layer |
| `memstat()` syscall — reports free physical memory pages | `kernel/sysproc.c`, `kernel/kalloc.c` | `si_meminfo()` → `/proc/meminfo` |
| Priority-based scheduler (static priority) | `kernel/proc.c` | `SCHED_FIFO` / `SCHED_RR` with `sched_setscheduler()` |

*(Table updated as each week's lab is completed)*

## Learning Map: xv6 → Linux

| xv6 Concept | Linux Equivalent |
|-------------|-----------------|
| `struct proc` | `struct task_struct` in `include/linux/sched.h` |
| `spinlock` / `acquire()` | `spin_lock_irqsave()` — used in interrupt context |
| `sleeplock` | `mutex_lock()` — sleepable, not safe in IRQ context |
| `usertrap()` / `kerneltrap()` | `do_IRQ()` + `handle_exception()` in arch/riscv |
| `kalloc()` / `kfree()` | `kmalloc()` / `kfree()` (slab allocator) |
| `uvmalloc()` | `do_mmap()` / `vm_mmap()` |
| `inode` layer | VFS `struct inode` |
| `struct file` | `struct file` (same concept, much richer in Linux) |

## Reference

- [xv6 book (RISC-V edition)](https://pdos.csail.mit.edu/6.828/2023/xv6/book-riscv-rev3.pdf) — free, ~100 pages, read the chapter matching what you're modifying
- [MIT 6.1810 course page](https://pdos.csail.mit.edu/6.828/2023/) — official lab assignments
- Upstream source: [mit-pdos/xv6-riscv](https://github.com/mit-pdos/xv6-riscv)

## Upstream Sync

```bash
git fetch upstream
git merge upstream/riscv-head
```
