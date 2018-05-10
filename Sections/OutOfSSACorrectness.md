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

... -> SSA form -> optimizations -> SSA destruction (ie. φ-functions elimination)

Optimizations create situations where the SSA variables arising from the same original variable now have overlapping live ranges.

Note: optimizations easier in SSA... examples?
- but they can break the conventional SSA property (CSSA - renaming all φ input and output operands to the same name preserves semantics)
- freshly constructed SSA is CSSA, optimizations break this

---

## Cytron et al. (1991)

"Naively, a *k*-input φ-function at entrance of a node *X* can be replaced by *k* ordinary assignments, one at the end of each control flow predecessor of *X*."

+ DCE + coloring

Note:
A k-input PHI at entrance of a node X can be replaced by k assignments at the end of each predecessor X. 
To optimize later, dead code elmimination + coloring.

---

## Cytron et al. (1991)

Original SSA form

![](Images/cytron1.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

---

## Cytron et al. (1991)

Phi copies inserted

![](Images/cytron2.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

---

## Cytron et al. (1991)

Phi function removed

![](Images/cytron3.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Fix Cytron: Problems with critical edges + the φ-functions in one BB have parallel semantics.

- The lost-copy problem
- The swap problem

Edge BBi -> BBj is critical if |succ(BBi)| > 1 && |pred(BBj)| > 1
=> cannot insert copies! either executed along paths that don't lead to the phi, or destroying values from other predecessors
=> edge splitting (new empty BB in the middle: BBi -> BBk -> BBj)

lost copy: copy folding + critical edge; solution: critical edge splitting

swap: copy folding causing input to phi defined by other phi in the same BB + parallel semantics of phis; solution: insert copies of phi outputs (but for all phis can be costly, so try to linearize the list and minimize the copies)

Note:
Problems in correctness are due to:
1. Critical edges
2. Branches that define variables
3. Register renaming constraints
4. Phi functions parallel copy semantics.

A critical edge is an edge which is neither the only edge leaving its source block, nor the only edge entering its destination block.

---

## Briggs et al. (1998)

Original code - the lost copy problem

![](Images/lostcopy0.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

SSA form

![](Images/lostcopy1.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Copy folding

![](Images/lostcopy2.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi copies inserted

![](Images/lostcopy3.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi function removed

![](Images/lostcopy4.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Fix for the lost copy problem

![](Images/lostcopy5.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Original code - the swap problem

![](Images/swap0.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

SSA form

![](Images/swap1.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Copy folding

![](Images/swap2.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi copies inserted

![](Images/swap3.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi functions removed

![](Images/swap4.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Fix for the swap problem

![](Images/swap5.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Sreedhar et al. (1999)

Change Briggs: inserting copies in the BB of φ-functions (removes need for edge splitting), φ-congruence classes (heuristics - three types - to minimize number of inserted copies)

---

## Sreedhar et al. (1999)

Original SSA form

![](Images/sreedhar1.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Sreedhar et al. (1999)

Copy of phi makes edge splitting unnecessary

![](Images/sreedhar2.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

---

## Leung & George, Budimlić et al., Rastello et al.

papers identifying and dealing with renaming constraints and dedicated registers

---

## Boissinot et al. (2009)

Revisit previous work + generic approach

Fix Sreedhar: Considering live-out sets may not be enough + sometimes NOT POSSIBLE to split critical edge by inserting a copy. 

- Transform to CSSA ~ Sreedhar
- But copies inserted are parallel (sequentialization alg.)
- Coalescing used to improve, but interference tested using SSA properties - live ranges and value equality
- also support for renaming constraints, and can be implemented efficiently

---

## Boissinot et al. (2009)

Original SSA form

![](Images/boissinot1.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

---

## Boissinot et al. (2009)

Phi copies inserted as in Sreedhar et al.

![](Images/boissinot2.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

---

## Boissinot et al. (2009)

Branch on wrong value

![](Images/boissinot3.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

---

## Boissinot et al. (2009)

Fixed by considering *u* in the *Br* for interference check

![](Images/boissinot4.png) <!-- .element width="66%" style="background:none; border:none; box-shadow:none;"-->
