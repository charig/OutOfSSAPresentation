## Code Quality

TODO: Show PIR 2 RIR example without optimizations

Note:
As seen a naive translation out of SSA further increases the number of new variables and the code size, 
because PHI functions are replaced by variables and copies from/to these variables.

---

## Reformulating: Out of SSA is an aggressive **coalescing** problem!

Biossinot et al. (CGO '09):
> A definition of interference is missing.

---

## First Definition

Two variables interfere if one is live at a definition point of the other and this definition is not a copy between the two variables.

---

## Coalescing

![](Images/notCoalescing.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:

---

![](Images/Coalescing.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:

---

![](Images/coalescingII.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:

---

TODO: Example

One can notice that, with this conservative interference definition, when a and b are coalesced, the set of interferences of the new variable may be strictly smaller than the union of interferences of a and b . Thus, simply merging the two corresponding nodes in the interference graph is an over-approximation with respect to the interference definition. For example, in a block with two suc- cessive copies b = a and c = a where a is defined before, and b and c (and pos- sibly a ) are used after, it is considered that b and c interfere but that none of them interfere with a . However, after coalescing a and b , c should not interfere anymore with the coalesced variable. Hence the interference graph would have to be updated or rebuilt.

Note: 
Updating interference graph is costly

---

## Beause of SSA

- The “has-the-same-value” binary relation defined on variables is an equivalence relation <!-- .element: class="fragment" -->
- The value is the variable whose definition dominates the definitions of all other variables in the class <!-- .element: class="fragment" -->

---

## Second Definition

a interfere with b if: 
- live(a) intersects live(b) and,
- Value(a) != Value(b)
