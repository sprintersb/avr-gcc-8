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

* [PR88236](https://gcc.gnu.org/PR88236),
[PR87376](https://gcc.gnu.org/PR87376),
[PR115726](https://gcc.gnu.org/PR115726): Fixed code for reading from
address-space __memx (fixed in v12.5, v13.4 and v14.2+).

* [PR98762](https://gcc.gnu.org/PR98762): Fixed a wrong code bug on
Reduced Tiny (fixed in v12.5, v13.4 and v14.2+).

* [PR116407](https://gcc.gnu.org/PR116407): Fixed a
"relocation truncated to fit" error from the linker
(fixed in v13.4, and v14.3+).

* [PR117500](https://gcc.gnu.org/PR117500): Use a proper error
message rather than running into an "internal compiler error"
when an inline assembly `%i` operand gets an address that's not
a suitable I/O location (fixed in v13.4, and v14.3+).

* [PR117659](https://gcc.gnu.org/PR117659): Wrong code
for `int24 << 16` (fixed in v12.5, v13.4, and v14.3+).

* [PR117744](https://gcc.gnu.org/PR117744): Wrong code
when a load only partially clobbers an address register
(fixed in v12.5, v13.4, and v14.3+).

* [PR64242](https://gcc.gnu.org/PR64242#c42): Wrong code
for nonlocal goto / longjmp (fixed in v12.5, v13.4, and v14.3+).


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

* [PR116056](https://gcc.gnu.org/PR116056): Support function attribues
`signal(n)`, `interrupt(n)` and `noblock`, see also the
[GCC v15 Release Notes](https://gcc.gnu.org/gcc-15/changes.html#avr).

* Support built-in function `__builtin_avr_mask1`, see the
[GCC v15 Release Notes](https://gcc.gnu.org/gcc-15/changes.html#avr).

* In C++ compilations, don't implicitly wrap headers found per
`-isystem` in `extern "C" { ... }`.

### Problems Avoided

The GCC v8 release avoids some problems of more recent compiler versions,
namely the following performance regressions from the register allocator:

* [PR118012](https://gcc.gnu.org/PR118012): Expensive code
(bit extract + extend + neg + and) instead of `sbrc`/`sbrs`.
This issue was introduced with [v13](https://gcc.gnu.org/r13-4459) and
won't be fixed prior to v16 (release spring 2026).

* [PR110093](https://gcc.gnu.org/PR110093): Move frenzy leading to code bloat.

* [PR114243](https://gcc.gnu.org/PR114243): `-fsplit-wide-types` bloats
`float` code by more than 50%.
As a work-around, `-fno-split-wide-types` can be used,
though that option cannot be recommended in general.

* [PR90706](https://gcc.gnu.org/PR90706): Useless code generated for
stack / register operations (partially fixed in v12.3+).
