## Code Quality

Note:
As seen a naive translation out of SSA further increases the number of new variables and the code size, 
because PHI functions are replaced by variables and copies from/to these variables.

---

## Out of SSA Quality is an aggressive **coalescing** problem! 

Note:

A definition of interference is missing=

---

## Coalescing

![](Images/coalescingCode.png) <!-- .element width="40%" style="background:none; border:none; box-shadow:none;"-->

---

## Interference I

Two variables interfere if 
1. one is live at a definition point of the other, and 
2. this definition is not a copy between the two variables

---

## Example

![](Images/notCoalescing.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:
After the interference graph is built, unnecessary
register copy operations are eliminated by coalescing
or combining the nodes which are the source and targets
of copy operations if these nodes do not interfere with each other.
Once two nodes have been coalesced, they must be allocated to the same 
register, and the copy operation becomes unnecessary.

---

![](Images/Coalescing.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:

---

![](Images/coalescingII.png) <!-- .element height="20%" width="85%" style="background:none; border:none; box-shadow:none;"-->

Note:

---

## Interference is too conservative
<div style="float: left; width: 25%">
![](Images/codeCoalescingConsevative.png) <!-- .element height="40%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 25%">
![](Images/conservativeCoalescingIntG.png) <!-- .element height="30%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 25%">
![](Images/codeCoalescingConsevativeII.png) <!-- .element height="40%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 25%">
![](Images/conservativeCoalescingIntGII.png) <!-- .element height="10%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->

Note:
When a and b are coalesced, the interferences of the new variable may be strictly smaller than the union of interferences of a and b. Simply merging the nodes in the interference graph is an over-approximation. 

For example, in a block with two successive copies b = a and c = a where a is defined before, and b and c (and possibly a) are used after, it is considered that b and c interfere but that none of them interfere with a. However, after coalescing a and b , c should not interfere anymore with the coalesced variable. Hence the interference graph would have to be updated or rebuilt.

But updating the interference graph is costly

---

## Beause of SSA

- The “has-the-same-value” binary relation defined on variables is an equivalence relation <!-- .element: class="fragment" -->
- The value is the variable whose definition dominates the definitions of all other variables in the class <!-- .element: class="fragment" -->

---

## Second Definition

a interfere with b if: 
- live(a) intersects live(b) and,
- Value(a) != Value(b)

---

## Value-Aware Interference

<div style="float: left; width: 20%">
![](Images/codeCoalescingConsevative.png) <!-- .element height="40%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 20%">
![](Images/aggressiveCoalescingIntG.png) <!-- .element height="30%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 20%">
![](Images/codeCoalescingConsevativeII.png) <!-- .element height="40%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 20%">
![](Images/aggresiveCoalescingIntGII.png) <!-- .element height="10%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->
<div style="float: left; width: 20%">
![](Images/codeCoalescingAggressive.png) <!-- .element height="10%" style="background:none; border:none; box-shadow:none;"-->
</div> <!-- .element: class="fragment" -->

---

## Efficciency

Time and memory consuming issues: <!-- .element: class="fragment" -->

- Interference Graph    <!-- .element: class="fragment" -->
    - Construction + updates <!-- .element: class="fragment" -->
- Liveness analysis on inflated #vars <!-- .element: class="fragment" -->
- Interference checks between each two vars <!-- .element: class="fragment" -->     
