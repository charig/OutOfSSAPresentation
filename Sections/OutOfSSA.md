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

> A definition of interference for variables involved in PHI functions is missing.

*[Revisiting Out-of-SSA Translation for Correctness, Code Quality and Efficiency (CGO '09)](https://dl.acm.org/citation.cfm?id=1545063)*

Note: 2009 CGO Paper Revisits Out-of-SSA and found from previous approaches that they were:

1. Incorrect
2. Incomplete
3. Overly pessimistic
4. Too expensive

---

## <span style="color:blue">C</span>onventional <span style="color:blue">SSA</span>

![](Images/CSSAExample.png) <!-- .element height="30%" width="70%" -->

However CSSA is too restrictive:

- Disable optimizations or
- Optimizations must mantain CSSA

Note: 
a0 = PHI(a1,...,an). If a0,...,an can be given the same name -> CSSA.
All variables connected (transitevily) through PHI functions have non-overlapping live ranges.
Since they do not overlap they can be replaced by the same name without interfering with the semantics of the original program 

[comment]: ---

[comment]: ## After copy propagation

[comment]: ![](Images/afterCopyProp.png) <!-- .element height="10%" width="40%" -->

[comment]: CSSA is lost. If we use the same name for *y* and *x1* we break the semantics.

---

## Lost Copy Problem

![](Images/lostCopy.png) <!-- .element height="20%" width="85%" -->

Note:
Problems in correctness are due to:
1. Critical edges
2. Branches that define variables
3. Register renaming constraints
4. Phi functions parallel copy semantics.

A critical edge is an edge which is neither the only edge leaving its source block, nor the only edge entering its destination block.

---

## Naive Approach

Insert copies everywhere!!!

TODO Example.

Note:
A k-input PHI at entrance of a node X can be replaced by k assignments at the end of each predecessor X. 
To optimize later, dead code elmimination + coloring.

---

## Swap Problem

TODO Example.

```
BB 1:
  x <- ...
  y <- ...
BB 2:
  t <- x
  x <- y
  y <- t
  conditional jump to BB 2 or BB 3
BB 3:
  ... <- x
  ... <- y
```

Note:

---

## Solution proposed

Add more copies!!!

Note:

---

## Code Quality

A naive translation out of SSA further increases the number of new variables and the code size, 
because PHI functions are replaced by variables and copies from/to these variables.

Example:
TODO





---

## distribute into prev slides:

---

## distribute into prev slides:

1. Cytron et al. (1991): "Naively, a *k*-input φ-function at entrance of a node *X* can be replaced by *k* ordinary assignments, one at the end of each control flow predecessor of *X*."
+ DCE + coloring

---

## distribute into prev slides:

2. Briggs et al. (1998): Fix Cytron: Problems with critical edges - the φ-functions in one BB have parallel semantics. Eg. the lost-copy problem, the swap problem.

---

## distribute into prev slides:

3. Sreedhar et al. (1999): Fix Briggs: φ-congruence classes, and inserting copies in the BB of φ-functions

---

## distribute into prev slides:

4. (papers identifying and dealing with renaming constraints and dedicated registers)

---

## distribute into prev slides:

5. Boissinot et al. (2009): revisit previous work, Fix Briggs: Considering live-out sets may not be enough + sometimes NOT POSSIBLE to split critical edge by inserting a copy. Copies inserted are parallel (sequentialization alg.), coalescing used to improve, but intersection tested using SSA properties - live ranges and value equality

