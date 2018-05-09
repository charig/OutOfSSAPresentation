## Moving Out of SSA

Optimizations create situations where the SSA variables arising from the same original variable now have overlapping live ranges.

---

## Factors to Take into Account

1. Correctness
2. Code Quality
3. Efficiency

> A definition of interference for variables involved in PHI functions is missing.

*[Revisiting Out-of-SSA Translation for Correctness, Code Quality and Efficiency (CGO '09)](https://dl.acm.org/citation.cfm?id=1545063)

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

