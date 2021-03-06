# Drob - Dynamic Rewriter and Optimizer of Binary code

This library implements application-guided rewriting of binary
functions at runtime. Binary functions can be optimized and specialized
based on runtime information. In contrast to transparent binary optimization,
only selected binary functions are rewritten. No metadata (e.g. debug
information) is required.

Drob is not designed to insert arbitrary new code (e.g.,
instrumentation) into existing binary code, rather to rewrite and optimize
existing code by dropping or replacing instructions. Drob implements
the following optimizations:
* Simple loop unrolling (single-superblock loops)
* Block layout optimizations
* Dead code elimination
* Dead register write elimination
* Memory operand address optimizations
* Instruction specialization

Internally, binary code is converted into an architecture-specific
intermediate representation, on which analyses and optimizations are
performed.

A rewritten function has the same signature as the original binary
function. The same parameters have to be supplied when calling the
rewritten binary function.

## !!! PROTOTYPE QUALITY !!!

This library was created as a prototype in the context of a Master's thesis
at the TUM (Technical University of Munich). Besides some tests and
some benchmarks, it hasn't seen much testing. Especially, only a very
small subset of the x86-64 ISA is modeled yet.

## General Restrictions

The focus is on binary code generated by a compiler. For now only the entry
function is analyzed and optimized. Called functions can be rewritten, but
not optimized. Optimizing called functions can be supported in the future
in many cases by inlining all non-recursive functions into the entry
function.
* Only x86-64 is supported
* Only the System V AMD64 ABI for x86-64 (a.k.a. Linux) is supported.
* Privileged instructions and registers are not supported.
* Self-modifying code is not supported.
* Exceptions and signals are not supported.
* ROP and non-local branches are not supported.
* ICFG reconstruction is kept very simple (e.g. indirect branches or calls
  like in jump tables cannot be resolved, however such code can be partially
  rewritten).
* Functions that do crazy things with the stack pointer are not supported.

## x86-64 Restrictions

Driven by benchmarks and simple tests, only a subset of x86-64 instructions
and features is supported in Drob. Besides basic instructions
(MOV, ADD, XOR), support for some Intel SSE instructions has been added.

Not supported for now are
* Intel AVX instructions including VEX prefixes
* Intel MMX instructions
* Legacy x87 FPU instructions including registers
* LOCK, REPNE, REP and segment override prefixes.
* Segmentation.
* Advanced instructions like the INT instruction
* Advanced features like Intel MPX.

Instructions accessing global variables via RIP-relative addressing
can under some conditions not be rewritten. Some unmodeled instructions
can be rewritten, however prohibit optimizations.

## License

LGPLv3+

Drob uses the Intel XED decoder on x86-64, release under the Apache License 2.0.

# Copyright

David Hildenbrand <davidhildenbrand@gmail.com>
