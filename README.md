## What is it?

This is a fork of the GCC project https://gcc.gnu.org
with additional changes to the `releases/gcc-8` branch.

All the changes you find in this fork address the
[AVR](https://en.wikipedia.org/wiki/AVR_microcontrollers) backend.

## Why is it here?

The latest of the
[GCC v8 releases](https://gcc.gnu.org/gcc-8/changes.html#avr), v8.5,
was created from `releases/gcc-8` in May 2021,
and then that branch was closed.

Some patches are back-ported from v14 to v8 because newer versions
of GCC may have some problems that v8 does not have.
However, v8 has also some problems and shortcoming, which this fork
tries to address.

### Bugs Fixed

* [PR92606](https://gcc.gnu.org/PR92606): Inter-procedural analysis
optimizes data across address-spaces and PROGMEM.
This is fixed in v14 for address-spaces but still persists
for PROGMEM.  The local fix just disables the bogus IPA optimization.

* [PR101188](https://gcc.gnu.org/PR101188): A rare wrong-code bug that
can be tracket back to GCC v5 and beyond.  It's fixed in v14 but
not in v13.2, and it is unclear if the fix will be back-ported to the
v13 branch.

* [PR107201](https://gcc.gnu.org/PR107201): The `-nodevicelib` option
did not work as expected for devices which start with "AVR", like AVR32DA32.

* The options `-g -mrelax -flto` did not work together and failed with
an `--relax and -r may not be used together` error from the linker.

### Extensions

* Support all devices that avr-gcc v14 supports.
For a complete list, see the
[online documentation](https://gcc.gnu.org/onlinedocs/gcc/AVR-Options.html)
on <code>&#8209;mmcu=</code>.

* [PR112944](https://gcc.gnu.org/PR112944): Support command-line option
`-m[no-]rodata-in-ram`.
When Binutils support [PR31124](https://sourceware.org/PR31124), then
read-only data on AVR64* and AVR128* devices is located in program memory.
For a detailed description with further reading, see the
[GCC v14 Release Notes](https://gcc.gnu.org/gcc-14/changes.html#avr).

* In C++ compilations, don't implicitly wrap headers found per
`-isystem` in `extern "C" { ... }`.
