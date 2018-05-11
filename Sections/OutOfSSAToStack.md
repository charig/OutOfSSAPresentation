## What Happens When Moving to a Stack Machine?

---


## Problems of Stack

1. Mapping back Registers to Stack
2. SSA redundant copies may be more harmful than in machine code  

Note: 
The first kind of copies are just introduced because we are using stack machine.
The latter are intrinsical to moving out of ssa.


The problem is mainly that we have stack operands in rir, but then we move to a register based machine, is is difficult to map back to a stack machine so that we do not have to copy again the registers to the top of the stack. 

---

## Mapping Regs to Stack

SSA
```
function(x, y)
  x + y
```

naively using local variables
```
load_arg 0
store_local x
load_arg 1
store_local y
load_local x
load_local y
add
```

---

## Solutions

Discovering stack discipline within one BB:

```
+a +b -b -a
 \  \_/  /
  -------
```

```
+a +b -b -a -a
 \  \_/  /  /
  ------'---
```
<!-- .element: class="fragment" -->

```
+a +b -a -b
 \  \ /  /
  ---x---
```
<!-- .element: class="fragment" -->

---

### Sinking Phis to stack

```
function(x) { if (x) y <- 1 else y <- 2; y }
```

<div>
unnecessary local variable p
```
   push_      2
   set_local  p
   br_  1
0:
   push_      1
   set_local  p
1:
   ld_local   p
   ret_
```
</div><!-- .element: class="fragment" -->
