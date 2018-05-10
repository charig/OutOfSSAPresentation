## What happens when moving to a Stack Machine?

---

## Example

Source
``` 
example <- function(a) { x <- 3; x + a + x } 

```

Rir
```
push_  84 # [1] 3
stvar_  25 # x
ldvar_  25 # x
ldvar_  46 # a
add_
ldvar_  25 # x
add_
ret_

```

---

## PIR Stack Machine

```
ldarg_  1
stloc_  @0
push_  84 # [1] 3
stloc_  @1
ldloc_  @0
force_
stloc_  @2
ldloc_  @2
ldloc_  @1
add_
stloc_  @3
ldloc_  @1
ldloc_  @3
add_
ret_

```

---

## Problems of Stack

1. Mapping back Registers to Stack
2. SSA redundant copies may be more harmful that in machine code  

Note: 
The first kind of copies are just introduced because we are using stack machine.
The latter are intrinsical to moving out of ssa.


The problem is mainly that we have stack operands in rir, but then we move to a register based machine, is is difficult to map back to a stack machine so that we do not have to copy again the registers to the top of the stack. 

---

## Example

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

## Concepts

- Phi Congruence Classes
- Live Analysis
    - Live Ranges
    - Liveness Sets 
    - Interference Graphs
- Coalescing
- Register Allocator
- Spilling

Note:
So we start with a simple compiler, and now by needing to move out of ssa to a stack machine we had to revise and implements all this kind of concepts/algorithms,etc. 