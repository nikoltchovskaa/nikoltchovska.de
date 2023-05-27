---
title: "Speeding up Python with Java"
date: 2023-05-27T11:43:00+02:00
draft: false 
summary: "Remember kids, 8 billion devices run Java."
---

Speeding up Python with C, C++, Rust or Fortran is pretty common. 
Today we'll do something different and use Java instead. While definitely uncool, its memory safe,
garbage collected and pretty quick due to JVM magic. 

As an example, we'll be implementing a very simple state machine that counts the number of times
a value passes a certain limit in a sequence of data. That's not a very useful thing per se, but 
the pattern of a state machine iterating over an array is pretty versatile and something Python is
really bad at due to its slow loops.

Let's start with the Python implementation:

```python
import numpy as np


def steps(arr: np.ndarray, limit) -> tuple[int, int]:
    up = 0
    down = 0
    state = 0
    for a in arr:
        if state == 0 and a > limit:
            up += 1
            state = 1
        elif state == 1 and a <= limit:
            down += 1
            state = 0
    return up, down


assert steps(np.array([0, 1, 0, 1]), 0.5) == (2, 1)
```

The Java version is also pretty straight forward:

```
public class Step {
    public int up;
    public int down;

    public Step(float[] arr, float limit) {
        int up = 0;
        int down = 0;
        int state = 0;
        for (int i = 0; i < arr.length; i++) {
            float a = arr[i];
            if (state == 0 && a > limit) {
                up += 1;
                state = 1;
            } else if (state == 1 && a <= limit) {
                down += 1;
                state = 0;
            }
        }

        this.up = up;
        this.down = down;
    }
}
```

To call it, we add a small wrapper using [jpype](https://github.com/jpype-project/jpype):

```python
import jpype

try:
    jpype.shutdownJVM()
except RuntimeError:
    pass

jpype.startJVM()
Step = jpype.JClass("Step")


def steps_java(arr: np.ndarray, limit: float):
    res = Step(jpype.JArray.of(arr), limit)
    return res.up, res.down
```

Jpype really nailed the ergonomics of using Java objects in python. Only the JVM handling feels a little clunky and 
is pretty slow on my machine. Lets compare the two versions:

```bash
❯ javac Step.java
❯ ipython3                
Python 3.11.3 (main, Apr  7 2023, 19:25:52) [Clang 14.0.0 (clang-1400.0.29.202)]
Type 'copyright', 'credits' or 'license' for more information
IPython 8.13.2 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from step import *

In [2]: arr = np.array(np.random.random(size=1000000), dtype=np.float32)

In [3]: %timeit steps_java(arr, .9)
5.31 ms ± 70.7 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [4]: %timeit steps_java(arr, .9) # after warmup
5.11 ms ± 111 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [5]: %timeit steps(arr, .9)
1.33 s ± 34.6 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)

In [6]: %timeit steps(arr, .9) # after warmup
1.23 s ± 67.2 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)

In [7]: assert steps(arr, .9) == steps_java(arr, .9) # both produce the same result

```

As you can see, the java version is around 200 times faster, even with the data conversion.
I also like the ergonomics and that the JVM saves me from cross compiling.


Remember kids, 8 billion devices run Java.
