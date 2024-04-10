---
title: NFRs Guide
info: |
  ## Welcome to NFRs Guide
  This is a presentation about how non-functional requirements (NFRs) drive software evolution and why they're so important. By watching it you will get motivated to think about NFRs in your projects and understand how they can help you build better software.
theme: seriph
themeConfig:
  primary: '#1034a6'
layout: image
image: ./nfrs-guide-logo.svg
backgroundSize: contain
class: extended-horizon
transition: slide-up
hideInToc: true
highlighter: shiki
colorSchema: light
---

<style>
  .extended-horizon {
    background-color: #eceff4;
  }
  .shadow-icon {
    filter: drop-shadow(2px 4px 6px #000000);
  }
</style>

<div class="pt-12 absolute bottom-1 left-0 right-0 text-center">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-blue bg-opacity-10" color="black">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/evarga/nfrs-guide" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white shadow-icon">
    <carbon-logo-github />
  </a>
</div>

---

# Navigation

Hover in the bottom-left corner to see the navigation's controls panel, [learn more](https://sli.dev/guide/navigation.html).

## Keyboard Shortcuts

|     |     |
| --- | --- |
| <kbd>right</kbd> / <kbd>space</kbd>| next animation or slide |
| <kbd>left</kbd>  / <kbd>shift</kbd><kbd>space</kbd> | previous animation or slide |
| <kbd>up</kbd> | previous slide |
| <kbd>down</kbd> | next slide |

<img
  v-click
  class="absolute -bottom-9 -left-7 w-80 opacity-50"
  src="https://sli.dev/assets/arrow-bottom-left.svg"
  alt=""
/>
<p v-after class="absolute bottom-23 left-45 opacity-30 transform -rotate-10">Hover over here!</p>

---
hideInToc: true
---

# Table of Contents

<Toc minDepth="1" maxDepth="2"></Toc>

---
layout: section
---

# NFRs Impact on Software Evolution

---
layout: two-cols
layoutClass: gap-16
hideInToc: true
---

## Production Incident

```python
from array import array

def double_preceding(x: array) -> None:
    if x:
        temp = x[0]; x[0] = 0
        for i in range(1, len(x)):
            x[i] = 2 * temp; temp = x[i]
```

<div v-click>

### Current Situation

- The original developer has left the company
- There is no documentation
- There is no automated unit test

</div>

<div v-click="2">

You write a unit test and <span v-mark.circle.red>`test_identical`</span> fails.

</div>

::right::

<div v-click="2">

### Unit Test

```python {12-17|all}{at:'+1'}
from array import array
import unittest

class TestDoublePreceding(unittest.TestCase):
    def test_already_arranged(self):
        """Test with already arranged values."""
        argument = array('i', [5, 10, 15])
        expected = array('i', [0, 10, 20])
        double_preceding(argument)
        self.assertEqual(expected, argument)

    def test_identical(self):
        """Test with multiple identical values."""
        argument = array('i', [0, 1, 1])
        expected = array('i', [0, 0, 2])
        double_preceding(argument)
        self.assertEqual(expected, argument)

    def test_empty(self):
        """Test with an empty array."""
        argument = array('i', [])
        expected = array('i', [])
        double_preceding(argument)
        self.assertEqual(expected, argument)
```

</div>

---
layout: two-cols
layoutClass: gap-16
hideInToc: true
---

## Production Hotfix

```python
from array import array

def double_preceding(x: array) -> None:
    if x:
        temp, x[0] = x[0], 0
        for i in range(1, len(x)):
            x[i], temp = 2 * temp, x[i]
```

Functionality has been fixed, but there are still [code smells](https://en.wikipedia.org/wiki/Code_smell). 

<div v-click>

You decide to improve both the usability and supportability quality attributes.

</div>

::right::

<div v-click="1">

## 1st Improved Variant

```python
from array import array

def double_preceding(x: array) -> None:
    """
    Transforms the array by setting x[i] = 2 * x[i-1] and x[0] = 0.

    >>> x = array('i', [5, 10, 15])
    >>> double_preceding(x)
    >>> x
    array('i', [0, 10, 20])
    """
    if x:
        for i in range(-1, -len(x), -1):
            x[i] = 2 * x[i - 1]
        x[0] = 0
```

</div>

---
layout: two-cols
layoutClass: gap-16
hideInToc: true
---

<div v-click="[0,1]">

## Wrong Argumentation
I have eliminated the temporary variable.<br/><br/>
The code is now beautiful and fully aligned with the embedded documentation.<br/><br/>
It is faster.<br/><br/>
It uses less memory.<br/><br/>
It has fewer SLOC.<br/><br/>
I have improved the global evaluation score from 4.44 up to 5 (she used PyLint to evaluate this score).

</div>

::right::

<div v-click>

## Correct Argumentation
I have spent T<sub>1</sub> time to understand the original code.<br/><br/>
I have spent an additional T<sub>2</sub> time to refactor it to improve maintainability; no new risks were added.<br/>
This code is central to the core functioning of our system.<br/><br/>
Other developers will only need to spend T<sub>3</sub> time to understand it, where T<sub>3</sub> is 20% of T<sub>1</sub>.<br/><br/>
$$
ROI=\frac{\text{Maintenance Savings}} {\text{Refactoring Cost}}
$$
$$
   =\frac{0.8 ∗ T_1} {T_1 + T_2}.
$$

</div>

---
layout: two-cols
layoutClass: gap-16
hideInToc: true
---

## 2nd Improved Variant

```python 
import numpy as np

def double_preceding(x: np.ndarray) -> None:
    """
    Transforms the array by setting x[i] = 2 * x[i-1] and x[0] = 0.
 
    >>> x = np.array([5, 10, 15])
    >>> double_preceding(x)
    >>> x
    array([0, 10, 20])
    """
    if x.size != 0:
        x[:-x.size:-1] = 2 * x[-2::-1]
        x[0] = 0
```

::right::

- Fully vectorized solution using [NumPy](https://numpy.org/).
- Considerably faster than the previous version.

---
layout: two-cols
layoutClass: gap-16
hideInToc: true
---

<div v-click="[0,1]">

## Wrong Argumentation
I have solved the problem with Numpy, which is nowadays used by everybody.<br/><br/>
I have eliminated the for loop and reduced SLOC.<br/><br/>
The code is much faster now due to vectorized operations.<br/><br/>
This function will serve as an example how to work with Numpy.

</div>

::right::

<div v-click>

## Correct Argumentation
I’ve improved the performance of a critical part of our product based upon profiling. This has created a performance budget that was missing up so far.<br/><br/>
The function’s higher speed increases throughput in processing client requests. This reduces the risk of losing clients in peak periods as well as may positively impact the conversion rate.<br/><br/>
Numpy (a new framework) was introduced with minimal risks.

</div>

---
layout: full
hideInToc: true
---

## Evolution of the Code
Notice that only the first change was about functionality. All the rest were improvements in quality attributes. The business arguments were crucial in deciding upon them.

````md magic-move
```python
# Original code
from array import array

def double_preceding(x: array) -> None:
    if x:
        temp = x[0]; x[0] = 0
        for i in range(1, len(x)):
            x[i] = 2 * temp; temp = x[i]
```

```python
# Bugfix version
from array import array

def double_preceding(x: array) -> None:
    if x:
        temp, x[0] = x[0], 0
        for i in range(1, len(x)):
            x[i], temp = 2 * temp, x[i]
```

```python
# Improved usability and supportability
from array import array

def double_preceding(x: array) -> None:
    """
    Transforms the array by setting x[i] = 2 * x[i-1] and x[0] = 0.

    >>> x = array('i', [5, 10, 15])
    >>> double_preceding(x)
    >>> x
    array('i', [0, 10, 20])
    """
    if x:
        for i in range(-1, -len(x), -1):
            x[i] = 2 * x[i - 1]
        x[0] = 0
```

```python
# Improved performance
import numpy as np

def double_preceding(x: np.ndarray) -> None:
    """
    Transforms the array by setting x[i] = 2 * x[i-1] and x[0] = 0.
 
    >>> x = np.array([5, 10, 15])
    >>> double_preceding(x)
    >>> x
    array([0, 10, 20])
    """
    if x.size != 0:
        x[:-x.size:-1] = 2 * x[-2::-1]
        x[0] = 0
```
````

---
layout: section
---

# Planguage at Rescue

---

## Customer Support Portal Example
One crucial indicator how well issues are handled is an ability to promptly alarm all relevant participants when severe defects are reported by customers. Suppose you encounter the following NFR:
> The issue tracker must quickly notify all involved parties about the showstopper error.

Obviously, what <span v-mark.underline.orange>quickly</span> means here is not clear at all. Let us see how more formalism may help.

---
layout : full
---

## NFR Formulated in Planguage
The keywords of the Planguage are situated on the left side. Observe how this NFR is now testable and unambiguous as well as clearly states what is the intention behind it. 

```text {none|1|2-3|4-7|8|9-11|12-15|all}
Tag:      Usability.Issue handling
Gist:     The system must promptly alarm all relevant stakeholders anytime a showstopper error
          is reported by a customer and entered into a bug tracking system.
Ambition: Implement the necessary triggering mechanisms inside the issue tracker system 
          to send an alert to all relevant stakeholders anytime a showstopper bug 
          is recorded. The idea is to have an efficient issue resolution pipeline. The assumption
           is that an operator may instigate the issue correction process by entering a severe bug report.
Scale:    The time in seconds before all stakeholders receive a push notification.
Meter:    The median time in seconds between the bug recording time and delivery of push notifications. 
          The median time should avoid outliers, which may disproportionately skew the average time. 
          The expected fidelity of a time measurement is around 3 seconds.
Baseline: 30
Fail:     30
Goal:     20
Stretch:  10
```

---
layout: end
hideInToc: true
---

## <CheckeredFlag/> THE END <CheckeredFlag/>

**In Summary:**
You have seen that most changes in software happens due to NFRs and that they shape the architecture of a system. In order to gain control over them, they must be formulated in a clear and testable way. Planguage is a great tool for this purpose. It was also demonstrated that business arguments are crucial when deciding upon improvements that are "hidden" from non-technical people.

**Thank You:**
If you find this project useful, please consider giving it a star on GitHub! Just visit [NFRs Guide](https://github.com/evarga/nfrs-guide) and click on the ⭐ button in the top-right corner to show your support. Your stars make a big difference!
