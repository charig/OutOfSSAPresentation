## <span style="color:blue">S</span>tatic <span style="color:blue">S</span>ingle <span style="color:blue">A</span>ssignment 

Developed at IBM Research during the 80 

---

## Motivation 

- Some code optimizations are NP-complete or even undecidable <!-- .element: class="fragment" -->
- The nature of the intermediate representation affects the speed and power of the compiler <!-- .element: class="fragment" -->

Note: 
In practice, factors such as the programmer's willingness to wait for the compiler to complete its task place upper limits on the optimizations that a compiler implementor might provide.

Historical Context. Throughout the 1980s, various (IRs) were proposed to encapsulate 
data dependence in a way that enabled fast and accurate dataflow analysis. 
The motivation was the exposure of direct links between variable definitions and uses, 
known as def-use chains, enabling efficient propagation of data-flow information. 
Example IRs include the program dependence graph [146] and program dependence web [252].

---

## SSA is mainly a naming convention for variables

A program is in SSA form if each variable is assigned exactly once <!-- .element: class="fragment" -->

<div style="text-align: left; float: left; width: 50%">
``` 
x = 1;
y = x + 1;
x = 2;
z = x + 1;
```                     
</div> <!-- .element: class="fragment" -->
<div style="text-align: right; float: right; width: 50%">
``` 
x1 = 1;
y = x1 + 1;
x2 = 2;
z = x2 + 1;
```                     
</div>
<!-- .element: class="fragment" -->

Referencial Transparency <!-- .element: class="fragment" -->

Note: 

Note that one assignment in the program text does not prevent multiple assignments to a variable during program execution (loops).

Referentially transparent programs are more amenable to formal methods and reasoning, 
since the meaning of an expression depends only on its subexpressions and not on the 
order of evaluation or side-effects of other expressions.

---

## Intuition

Unique names for distinct entities reduces uncertainty and imprecision

Note:
If a variable has D definitions and U uses, then there can be D x U def-use chains. 
Encoded in SSA form, there can be at most E where E is the number of
edges in the control flow graph. 


---

## PHI Functions
<div style="text-align: left; float: left; width: 50%">
![](Images/BranchNoSSACode.png) <!-- .element height="20%" width="40%" style="background:none; border:none; box-shadow:none; "-->
</div>
<div style="text-align: right; float: right; width: 50%">
![](Images/BranchSSACFG.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;" class="fragment"-->
</div>

Note:
In terms of their position, φ-functions⋆are generally placed at control-flow merge points, i.e., at the heads of basic blocks that have multiple predecessors in control-flow graphs. The behavior of the φ-function is to select dynamically the value of the parameter associated with the actually executed control-flow path into b. This parameter value is assigned to the fresh variable name, on the left-hand-side of the φ-function.

---

## Parallel Copy Semantics

![](Images/ParallelCopy.png) <!-- .element height="20%" width="60%" style="background:none; border:none; box-shadow:none;"-->

---

## Data-Flow Analysis Example

![](Images/constantPropagation.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;" -->

Note:
Difference between sparse and dense representations 

Def-use and use-def chains represent quick pointers from uses/def of a variable to the set of its uses/definitions.
Useful for lot of data-flow analysis.
In SSA each variable is assigned only once. Because of this, we only need one use-def chain per use of a variable

---

## SSA enables the convenient expression of several **data-flow analyses**

---

## Analyses Easier in SSA

* Copy progragation
* Constant propagation
* Liveness analysis
* Redundancy elimination
* ...

---

### Industrial Strength Compilers Use SSA 
* GCC
* LLVM
* JIT Compilers
    - HotSpot JVM
    - V8
    - Graal

Note: 
Cliff Clicks Thesis: 
Quick access through use-def chains and the manifestation of merge points simplify a number of
problems. In addition, use-def chains and SSA form allow the old bit vector and integer
vector analyses to be replaced with sparse analyses. 

[comment]: ## <span style="color:blue">C</span>ontrol <span style="color:blue">F</span>low <span style="color:blue">G</span>raph

[comment]:  ```
[comment]:  BB0: v1 = 2
[comment]:      push true 
[comment]:      branchIfTrue BB2 
[comment]:  BB1: v1 = 1
[comment]:      jmp BB3
[comment]:  BB2: v1 = 2
[comment]: BB3: val := v1 + v2 \\ This 3-address operators are dubbed quads
[comment]: ```

[comment]:  * Is val a constant? 
[comment]:     - Is v1 a constant? 
[comment]:     - Is v2 a constant? 


[comment]:  Note:
[comment]:  The CFG is a directed graph. The vertices represent basic blocks and the edges represent possible directions
[comment]:  of program control flow. A basic block is a section of code with no control flow.

[comment]: Data-flow analysis collects information about programs at compile time in order to make optimizing code transformations.

[comment]: ---

[comment]: ---

[comment]: ## SSA Example

[comment]: ![](Images/zeroAnalysis.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;" -->

[comment]: Note:

[comment]: When a program is translated into SSA form, variables are renamed at definition points. For certain data-flow problems (e.g. constant propagation) this is exactly the set of program points where data-flow facts may change

[comment]: 1. Vectors with assigment locations for each variable.
[comment]: The compiler must carry information about assignments to each variable forward to all possible
[comment]: uses of. This requires carrying vectors of information around, even to assignments that do not use b.

---

## Moving Out of SSA

Factors to take into account:

1. Correctness
2. Code Quality
3. Efficiency

Note: 2009 CGO Paper Revisits Out-of-SSA and found from previous approaches that they were:

1. Incorrect
2. Incomplete
3. Overly pessimistic
4. Too expensive