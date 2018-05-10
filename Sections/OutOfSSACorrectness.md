## Correctness

---

Phi Congruence Class  =Int All variables (transitively) connected through a PHI function 

All variables within the same congruence class must not interfere <!-- .element: class="fragment" -->

Note:
One possible definition: all variables connected (transitevily) through PHI functions have non-overlapping live ranges.

---

## <span style="color:blue">C</span>onventional <span style="color:blue">SSA</span>

![](Images/CSSAExample.png) <!-- .element height="30%" width="70%" -->

In CSSA PHI congruence classes do not interfere by definition.<!-- .element: class="fragment" -->

Note: 
a0 = PHI(a1,...,an). If a0,...,an can be given the same name -> CSSA.
Since they do not interfere they can be replaced by the same name without interfering with the semantics of the original program 

---

## After copy propagation

After opt SSA variables arising from the same variable may have overlapping live ranges.

![](Images/afterCopyProp.png) <!-- .element height="10%" width="40%" class="fragment"-->

CSSA is lost. Same name for y and x1 breaks semantics. <!-- .element: class="fragment" -->

Note:
However CSSA is too restrictive:
- Disable optimizations or 
- Optimizations must mantain CSSA 

---

## Solution 

1. Optimize, then 
2. Convert to CSSA when moving out of SSA

---

## Naive approach: Insert copies everywhere!

TODO: Example.

Cytron et al '91

Note:
A k-input PHI at entrance of a node X can be replaced by k assignments at the end of each predecessor X. 
To optimize later, dead code elmimination + coloring.

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

## Swap Problem

TODO Example.

Note:

---

## Solution: Add more copies!!!

Example:

Briggs et al.

Note: