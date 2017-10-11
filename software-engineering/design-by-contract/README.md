# Design by contract in a nutshell

*Design by contract is well-described by many sources on the Internet. I
won't repeat those details that may be found elsewhere. This nutshell is
not meant to be comprehesive. Rather, it is meant to point out some
common mistakes that programmers make when using design by contract
principles.*

## Introduction

Consider the following function:

```python
# python

# Sum of array elements from 'start' up to (but not including) 'end'.
def array_sum(arr, start, end):
    sum = 0
    while start != end:
        sum += arr[start]
        start += 1
    return sum
```

We can call that function in a pathological way:

```python
# python

arr = ['a', 'b', 'c']
start = 1000
end = -1000
sum = array_sum(arr, start, end)
```

The pathological function call will not produce "correct" results. In
fact, it's not clear what the "correct" result of the pathological call
should be. We'll say that the pathological call produces **undefined**
results.

**Design by contract** is an attempt to document the conditions in which
we expect a piece of code to produce a **well-defined** result.

The conditions in which a piece of code will produce a well-defined
result are called **preconditions**. The well-defined result of running
a piece of code are called **postconditions**.

Let's apply design by contract to our `array_sum` function:

```python
# python

# SYNOPSIS
#
# Sum of array elements from 'start' up to (but not including) 'end'.
#
# PRECONDITIONS
# 
# - start <= end
# - start >= 0
# - end <= len(arr)
# - 'arr' is an array of numbers
#
# POSTCONDITIONS
#
# - Function terminates.
# - Return value is the sum of 'arr' elements from 'start' up to (but
#   not including) 'end'
def array_sum(arr, start, end):
    sum = 0
    while start != end:
        sum += arr[start]
        start += 1
    return sum
```

## Avoid defensive programming

Now, some programmers might be tempted to do some precondition
checking. For instance:

```python
# python

def array_sum(arr, start, end):
    if start > end:
        raise RuntimeError('Precondition violation: start <= end')
    sum = 0
    while start != end:
        sum += arr[start]
        start += 1
    return sum
```

Note however that this introduces a new well-defined behavior, so we
must update our preconditions and postconditions accordingly.

```python
# python

# SYNOPSIS
#
# Sum of array elements from 'start' up to (but not including) 'end'.
#
# PRECONDITIONS
# 
# - start >= 0
# - end <= len(arr)
# - 'arr' is an array of numbers
#
# POSTCONDITIONS
#
# - If 'start > end'
#   - Raises RuntimeError
# - Else
#   - Function terminates.
#   - Return value is the sum of 'arr' elements from 'start' up to (but
#     not including) 'end'
def array_sum(arr, start, end):
    if start > end:
        raise RuntimeError('Precondition violation: start <= end')
    sum = 0
    while start != end:
        sum += arr[start]
        start += 1
    return sum
```

Notice that we removed the `start <= end` precondition. Why? By
definition, preconditions are the set of conditions for which a function
call is well-defined. As soon as we add code to handle the condition
where `start > end`, we are explicitly adding well-defined behavior for
that condition. Therefore, it is no longer a precondition. It is a
normal condition that `array_sum` expects it may encounter.

Also, we had to add some postconditions. This illustrates the fact that
by adding the check for `start > end`, we are actually adding another
feature to `array_sum`. That's right, it's a feature. If you
intentionally handle a case in a function, it means that you expect to
encounter that case during normal operation, and the way the function
handles that case becomes a feature that you have to support, maintain,
and unit test.

Personally, I don't think this feature is worth the effort. Any function
call that makes use of this feature is likely a bug in the caller.
Therefore, we should leave `start <= end` as a precondition (i.e.
something that is the caller's responsibility to verify), and remove the
check for `start > end`.

In conclusion:

- If you check a precondition, it is no longer a precondition. It is a
  condition that you explicitly handle in a well-defined way. It is now
  a feature.
  - The exception to this rule is `assert` statements which are only
    enabled during special *debug* runs to help diagnose bugs. These
    assert statements are not features. However, if the `assert`
    statements run in normal operation (outside of the *debug* runs),
    then they become full-fledged features.
- Rely on your caller to ensure preconditions are met. Let behavior of
  your function remain undefined if your preconditions are not met.

## Leave obvious preconditions unspoken

Preconditions are always incomplete. Period.

Sure, we can get really specific and try to completely define the set of
conditions under which our function behaves in a defined way. In the
extreme case, we would have to enumerate every possible argument and
global variable in our program, and describe how the function will
behave in each possible state of the computer. We could even enumerate
every possible state of the computer's memory, and how our function will
behave in that state. Of course, this is impractical.

No matter how much you restrict your preconditions, there will always be
holes that can cause your function to produce undefined results.

Some common holes are:

- Integer overflow
- Recursion limit
- Out of memory
- Network / connection failure

Some less common holes are:

- Memory corruption
- Hardware faults
- Power failure

So, your preconditions are incomplete. Accept it. And don't bother to
document obvious preconditions (e.g. that numbers are within array
bounds, et cetera).

