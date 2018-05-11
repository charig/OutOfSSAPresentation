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
x = y + 3
```

Native Code:
```
add r1, 3
mov r2, r1
```

RIR:
```
load loc(y)
push 3
add 
store loc(x)
```

---

## Ideally

If
1. y is just produced and,
2. the results is consumed afterwards and,
3. consumed just once:

```
# code that leaves y on top of the stack
    push 3
    add 
# code that consumes the result
```

---

## Solutions

Spilling phase?

Note:    

---

## Mappings + extra PHI copies

Source
``` 
function(x) { if (x) y <- 1 else y <- 2; y }
```

RIR
```
   ldvar_  5 # x
   asbool_
   brtrue_  0
   push_  8 # [1] 2
   stvar_  9 # y
   br_  1
0:
   push_  10 # [1] 1
   stvar_  9 # y
1:
   ldvar_  9 # y
   ret_

```

---

## RIR After PIR Naive

<div style="text-align: left; float: left; width: 30%">

```
   ldarg_  0
   stloc_  @1
   ldloc_  @1
   force_
   stloc_  @2
   ldloc_  @2
   aslogical_
   stloc_  @3
   ldloc_  @3
   asbool_
   stloc_  @4
   ldloc_  @4
   brtrue_  0
```
</div> <!-- .element: class="fragment" -->
<div style="text-align: left; float: right; width: 70%">
```
   push_  8 # [1] 2
   stloc_  @5
   movloc_  @5 -> @0  # ssa copy predecessor 
   br_  1
0:
   push_  10 # [1] 1
   stloc_  @6
   movloc_  @6 -> @0  # ssa copy predecessor
1:
   movloc_  @0 -> @7  # ssa copy after phi
   ldloc_  @7
   ret_
```
</div> <!-- .element: class="fragment" -->