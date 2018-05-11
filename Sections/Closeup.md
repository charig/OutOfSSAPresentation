## Register Allocation

- Linear Scan
- Graph Coloring    

After or before moving out of SSA? <!-- .element: class="fragment" -->

---

## Closing up

- Phi Semantics
- Phi Congruence Classes
- Live Analysis
    - Live Ranges
    - Liveness Sets 
    - Interference Graphs
- Coalescing (registers and copies)
- Register Allocation
- Spilling


Note:
So we start with a simple compiler, and now by needing to move out of ssa to a stack machine we had to revise and implements all this kind of concepts/algorithms,etc. 

---

## Main References
- [Register allocation & spilling via graph coloring(CC '82)](https://dl.acm.org/citation.cfm?id=806984)
- [Cytron et al.: Efficiently computing SSA form and the control dependence graph (TOPLAS '91)](https://dl.acm.org/citation.cfm?id=115320)
- [Briggs et al.: Practical Improvements to the Construction and Destruction of SSA Form (SPE '98)](https://dl.acm.org/citation.cfm?id=295551)
- [Sreedhar et al.: Translating Out of SSA Form (SAS '99)](https://dl.acm.org/citation.cfm?id=718132)
- [Boissinot et al.: Revisiting Out-of-SSA Translation for Correctness, Code Quality and Efficiency (CGO '09)](https://dl.acm.org/citation.cfm?id=1545063)
