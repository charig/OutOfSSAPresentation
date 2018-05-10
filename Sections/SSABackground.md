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

---

## Moving Out of SSA

... -> SSA form -> optimizations -> SSA destruction (ie. φ-functions elimination)

Optimizations create situations where the SSA variables arising from the same original variable now have overlapping live ranges.

Note: optimizations easier in SSA... examples?
- but they can break the conventional SSA property (CSSA - renaming all φ input and output operands to the same name preserves semantics)
- freshly constructed SSA is CSSA, optimizations break this

---

## Factors to Take into Account

1. Correctness
2. Code Quality
3. Efficiency

Note: 2009 CGO Paper Revisits Out-of-SSA and found from previous approaches that they were:

1. Incorrect
2. Incomplete
3. Overly pessimistic
4. Too expensive

---

## Cytron et al. (1991)

"Naively, a *k*-input φ-function at entrance of a node *X* can be replaced by *k* ordinary assignments, one at the end of each control flow predecessor of *X*."

+ DCE + coloring

---

## Briggs et al. (1998)

Fix Cytron: Problems with critical edges - the φ-functions in one BB have parallel semantics. Eg. the lost-copy problem, the swap problem.

---

## Sreedhar et al. (1999)

Fix Briggs: φ-congruence classes, and inserting copies in the BB of φ-functions

---

## Leung & George, Budimlić et al., Rastello et al.

papers identifying and dealing with renaming constraints and dedicated registers

---

## Boissinot et al. (2009)

Revisit previous work
Fix Briggs: Considering live-out sets may not be enough + sometimes NOT POSSIBLE to split critical edge by inserting a copy. Copies inserted are parallel (sequentialization alg.), coalescing used to improve, but intersection tested using SSA properties - live ranges and value equality
