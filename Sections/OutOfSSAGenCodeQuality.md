## Code Quality

TODO: Show PIR 2 RIR example without optimizations

Example:
As seen a naive translation out of SSA further increases the number of new variables and the code size, 
because PHI functions are replaced by variables and copies from/to these variables.

---

## Reformulating: Out of SSA is an aggressive **coalescing** problem!

Biossinot et al. (CGO '09):
> A definition of interference is missing.

---

## Coalescing

![](Images/notCoalescing.png) <!-- .element height="20%" width="85%" -->

Note:

---

![](Images/Coalescing.png) <!-- .element height="20%" width="85%" -->

Note:
