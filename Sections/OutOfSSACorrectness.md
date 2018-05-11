## Correctness

... → SSA form → optimizations → SSA destruction

Note:
- ssa useful for optimizations
- but phis not executable (mention interpreting ssa paper, needs runtime hacks)
- ssa destruction = φ-functions elimination

---

## <span style="color:blue">C</span>onventional <span style="color:blue">SSA</span>

Giving the same name to all phi inputs and removing the phi does not change the semantics of the program

But: optimizations on SSA can break this property, ie. interference of phi arguments

Idea: Convert back to CSSA before destruction

Note:
- freshly constructed SSA is CSSA
- However CSSA is too restrictive:
- Disable optimizations or opt must mantain CSSA 

- then broken by opt: some phi args interfere, ie. live at the same time

- evolution from the first attempts

---

## Cytron et al. (1991)

"Naively, a *k*-input φ-function at entrance of a node *X* can be replaced by *k* ordinary assignments, one at the end of each control flow predecessor of *X*."

+ DCE + coloring

Note:
can be a lot of copies, so combine with dead code elmimination + coloring to coalesce.

---

## Cytron et al. (1991)

Original SSA form

![](Images/cytron1.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

Note:
eg. if sth assign 1 else assign 2 and then print

---

## Cytron et al. (1991)

Phi copies inserted

![](Images/cytron2.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

Note:
- for x copy in bb1
- for y copy in bb2
- use the copies in the phi

---

## Cytron et al. (1991)

Phi function removed

![](Images/cytron3.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

Note:
- finally, give all the inputs the same name and remove the phi
- looks reasonable for simple cases, but there are problems

---

## Briggs et al. (1998)

Fix Cytron: Problems with critical edges and the parallel semantics of the φ-functions

1. The lost copy problem
2. The swap problem

Note:
- critical edge if source multiple succ and sink multiple pred
- cannot insert copies! either executed along paths that don't lead to the phi, or destroying values from other predecessors
- edge splitting (new empty BB)
- lost copy: copy folding + critical edge; solution: critical edge splitting
- swap: copy folding + parallel semantics of phis; solution: insert copies of phi outputs

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

Note:
Here comes the problem: copy folding y = x1 to BB3 extended the live range of x1

---

## Briggs et al. (1998)

Phi copies inserted

![](Images/lostcopy3.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi function removed

![](Images/lostcopy4.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

Note:
incorrect: z has the value incremented one too many times

---

## Briggs et al. (1998)

Fix for the lost copy problem

![](Images/lostcopy5.png) <!-- .element width="28%" style="background:none; border:none; box-shadow:none;"-->

Note:
- Fix is to split the critical edge and put the copy there
- now x1 overwritten only if back edge taken

---

## Briggs et al. (1998)

Original code - the swap problem

![](Images/swap0.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
- skip if no time
- little more subtle and involved

---

## Briggs et al. (1998)

SSA form

![](Images/swap1.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
just swaps two variables in a loop

---

## Briggs et al. (1998)

Copy folding

![](Images/swap2.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
- copy folding causes input to phi defined by other phi in the same BB
- it loses the temporary

---

## Briggs et al. (1998)

Phi copies inserted

![](Images/swap3.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

---

## Briggs et al. (1998)

Phi functions removed

![](Images/swap4.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
Here is the problem: we overwrite the first value

---

## Briggs et al. (1998)

Fix for the swap problem

![](Images/swap5.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
- parallel semantics of phis - need to be executed simultaneously
- insert copies of phi outputs - can be costly
- all the phis form a dependency graph, break the cycles there by inroducing copies

---

## Sreedhar et al. (1999)

Change Briggs: insert copies of the φ-functions outputs (no need for edge splitting), φ-congruence classes (to minimize inserted copies)

Note:
- Phi Congruence Class = variables (transitively) connected through a PHI function
- heuristics to save some of the copies
- each phi input gets its congruence class, then unify them
- consider live out sets X congr class

---

## Sreedhar et al. (1999)

SSA form with phi copies inserted

![](Images/sreedhar1.png) <!-- .element width="25%" style="background:none; border:none; box-shadow:none;"-->

Note:
notice: x1 = x1' after the phi

---

## Sreedhar et al. (1999)

Copy of phi makes edge splitting unnecessary

![](Images/sreedhar2.png) <!-- .element width="50%" style="background:none; border:none; box-shadow:none;"-->

Note:
now after removing code is correct
compare with prev example w/ edge splitting

---

## Boissinot et al. (2009)

Revision of previous work + generic approach + fix Sreedhar: Considering live-out sets may not be enough

Note:
- Transform to CSSA ~ Sreedhar
- But copies inserted are parallel (sequentialization alg.)
- Coalescing used to improve, but interference tested using SSA properties - live ranges and value equality

---

## Boissinot et al. (2009)

Subtle problem for some branch instructions for Sreedhar

![](Images/boissinot1.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

Note:
- SSA form
- in BB3 read of u, that is input to phi - but from BB2!

---

## Boissinot et al. (2009)

Phi copies inserted as in Sreedhar et al.

![](Images/boissinot2.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

Note:
- notice: only copy in BB3
- because u is not in live-out of BB3
- thus phi-congr-class of u doesn't interfere with live-out of BB3 -> no copy in BB2

---

## Boissinot et al. (2009)

Branch on wrong value

![](Images/boissinot3.png) <!-- .element width="33%" style="background:none; border:none; box-shadow:none;"-->

Note:
After phi removed, branching on v not u

---

## Boissinot et al. (2009)

Fixed by considering *u* in the *Br* for interference check

![](Images/boissinot4.png) <!-- .element width="66%" style="background:none; border:none; box-shadow:none;"-->

Note:
- more generally consider live set just after the copy
- another: sometimes NOT POSSIBLE to split critical edge by inserting a copy
- eg. branch-and-decrement instruction that defines a value somewhere in between the blocks
- proved correctness
- + efficient + support renaming constraints
