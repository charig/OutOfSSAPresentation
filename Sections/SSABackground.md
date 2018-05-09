## <span style="color:blue">S</span>tatic <span style="color:blue">S</span>ingle <span style="color:blue">A</span>ssignment

The nature of the intermediate representation affects the speed and power of the
compiler.

---

## Motivation
It has been shown that some code optimization problems are NP-complete, or even undecidable. In practice, factors such as the programmer's willingness to wait for the compiler to complete its task place upper limits on the optimizations that a compiler implementor might provide.

---

## <span style="color:blue">C</span>ontrol <span style="color:blue">F</span>low <span style="color:blue">G</span>raph

```
BB0: if true then S1 else S2 
BB1: v1 = 1
     jmp BB3
BB2: v1 = 2
BB3: val := v1 + v2 \\ This 3-address operators are dubbed quads
```

* Is val a constant?
    - Is v1 a constant?
    - Is v2 a constant?


Note:
The CFG is a directed graph. The vertices represent basic blocks and the edges represent possible directions
of program control flow. A basic block is a section of code with no control flow.

---

## Alternatives

1. Vectors with assigment locations for each variable.
    - Carry lot of (unnecessary) information
2. Use-Def Chains
    - Merge chains before every use for each variable
3. SSA
    - Merge when definitions interfere 

Note: 
The compiler must carry information about assignments to each variable forward to all possible
uses of. This requires carrying vectors of information around, even to assignments that do not use b.

Use-def chains represent quick pointers from uses of a variable to the set of its last definitions.
However, many definitions can reach the quad along a different path in the CFG. 
When deciding what happened to b, the compiler must merge the information from each use-def chain.

SSA is an alternative to solve this. so called because each variable is assigned only once. 
Because of this, we only need one use-def chain per use of a variable. 

---

## SSA Example

```
if( … ) x := 1;             if( … ) x0 := 1;
else x := 2;                else x1 := 2;
… x…                        x2 := φ( x0, x1);
                            …x2…
```

Cliff Click (Thesis): 


> <span style="font-size: 0.5em">SSA form allows the convenient expression of a variety of data-flow analyses. 
> Quick access through use-def chains and the manifestation of merge points simplify a number of
> problems. In addition, use-def chains and SSA form allow the old bit vector and integer
> vector analyses to be replaced with sparse analyses. </span>


Note:
Building SSA form is not trivial. The compiler must rename the target variable
in each assignment to a new name; it must also rename the uses to be consistent with
the new names. Any time two CFG paths meet, the compiler needs a new name, even
when no assignment is evident. 

If a variable has D definitions and U uses, then there can be D x U clef-use chains. 
Encoded in SSA form, there can be at most E where E is the number of
edges in the control flow graph. 

---

## SSA Status
Since several data-flow optimizaions in SSA are fast and powerful, 
SSA is also a good choice for <span style="color:blue">J</span>ust <span style="color:blue">I</span>n <span style="color:blue">T</span>ime Compilers

1. HotSpot
2. C#
2. LLVM
3. Graal
4. Julia?
