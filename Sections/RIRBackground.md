## <span style="color:blue">R</span> <span style="color:blue">I</span>ntermediate <span style="color:blue">R</span>epresentation

RIR encompasses a whole (eventually speculative) compilation infrastructure for R.

---

## RIR

---

```
function(x)
  x+y
```

---

```
function(x)
  x+y
```

to

```
guard_fun_  `+` == .Primitive(+)
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

## PIR

---

```
function(x)
  x+y
```

be explicit
```
%0  = LdArg    0
e1  = MkEnv    parent=<R_GlobalEnv>, x=%0
%2  = LdVar    x, e1
%3  = Force    %2
%4  = LdVar    y, e1
%5  = Force    %4
%6  = Add      %3, %5
      Return   %6
```

---

```
function(x)
  x+y
```

promote loads
```
%0  = LdArg    0
e1  = MkEnv    parent=<R_GlobalEnv>, x=%0

%3  = Force    %0
%4  = LdVar    y, e1
%5  = Force    %4
%6  = Add      %3, %5
      Return   %6
```

---

```
function(x)
  x+y
```

promote loads
```
%0  = LdArg    0
e1  = MkEnv    parent=<R_GlobalEnv>, x=%0

%3  = Force    %0
%4  = LdVar    y, <R_GlobalEnv>
%5  = Force    %4
%6  = Add      %3, %5
      Return   %6
```

---

```
function(x)
  x+y
```

look, no env
```
%0  = LdArg    0


%3  = Force    %0
%4  = LdVar    y, <R_GlobalEnv>
%5  = Force    %4
%6  = Add      %3, %5
      Return   %6
```

---

```
function(x) {
  g()
  x+y
}
```

---

```
function(x)
  { g(); x+y }
```

not always that easy
```
%0  = LdFun     g, <R_GlobalEnv>
%1  = LdArg     0
e2  = MkEnv     parent=<R_GlobalEnv>, x=%1
%3  = Call      e2, %0                      ; env leak
...
```

---

```
function(x) {
  if (x)
    x <- 1
  else
    x <- 2
  x
}
```

---

```
function(x) {
  if (x) x <- 1 else x <- 2
  x
}
```

look, no mutable vars
```
BB0
  %0  = LdArg      0
        ...
        Branch     -> BB1 (if true) | BB2 (if false)
BB1
  %1  = LdConst    1
  goto BB3
BB2
  %2  = LdConst    2
  goto BB3
BB3
  %3  = Phi        %1:BB1, %2:BB2
  Return           %3
```

