## <span style="color:blue">R</span> <span style="color:blue">I</span>ntermediatte <span style="color:blue">R</span>epresentation

RIR encompasses a whole (eventually speculative) compilation infrastructure for R.

---

### RIR

```
function(x)
  x+y
```

to

```
   guard_fun_  + == 0x14bcc70
   ldvar_  x
   ldvar_  y
   add_
   ret_
```

But what are `x` and `y`?

---

### "call-by-environment"


```
  y <- 1
  f <- function(x)
    x + y
  f(2)
```

The call creates a fresh environment

```
[parent = R_GlobalEnv, x = 2]
```

and passes it to the callee.

---

### PIR

```
function(x)
  x+y
```

```
  val^   %0  = LdArg     0
  env    e1  = MkEnv     parent=<environment: R_GlobalEnv>, x=%0
  val^?  %2  = LdVar     x, e1
  val    %3  = Force     %1
  val^?  %4  = LdVar     y, e1
  val    %5  = Force     %1
  val    %6  = Add       %1, %1
  void         Return    %1
```

look, no env

```
  val^   %0  = LdArg     0
  val    %1  = Force     %0
  val^?  %2  = LdVar     y, <environment: R_GlobalEnv>
  val    %3  = Force     %2
  val    %4  = Add       %1, %3
  void         Return    %4
```
