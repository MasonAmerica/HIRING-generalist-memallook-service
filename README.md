Mason Engineering - Software Engineer exercise
===

# "Memallook" Service - a visual memalloc simulation

Your objective is to write a simple HTTP-based service named `memallook`. This service's primary job
is to maintain a stateful record of memory allocations performed in a buffer and return it when requested.
In other words, it is a toy memory allocator fronted by a service.

## Operations
The service supports a set of operations discussed below.

### NEW
This is the first operation that must be invoked before any of the others is invoked. This operation
creates the internally maintained virtual heap, and expects two parameters:
* `p`, the page size in bytes
* `N`, the number of available pages
These parameters together give you the total available memory space as `N * p`.

This operation must respond with a heap identifier of the form `HEAP-nnn` where `nnn` is an integer
between `100` and `999`.

Note:
* It is an error if any other operation is invoked prior to this one
* If this operation is invoked more than once, subsequent calls must be NOPs

### ALLOC

The `ALLOC` command "allocates" a block of `M` bytes in the memory buffer and returns
a unique tag `T` which sort of can be treated like a pointer. If `M` bytes can no longer be
allocated, the service must return an error.

### DEALLOC

The `DEALLOC` command deallocates a previously allocated block with tag `T`. If `T` is
unknown, this is an error.

### SHOW

The `SHOW` command returns the state of the memory using a simple character matrix
with an `x` displaying an occupied page, and a tag character implying a free page (see example below).
This grid/matrix is then followed by a listing of all tags allocated and the number of bytes
allocated for each.

### RESET
The `RESET` command clears any maintained state (i.e. clears the heap) and starts over, causing the
service to expect a `NEW` command again.

## Examples
Note each command run below against the service is shown as _OPERATION parameters_
as opposed to HTTP requests and responses.

```
NEW with p=16, N=64

// ok, buffer of size 1024 bytes created
```

Now allocate 16 bytes:
```
ALLOC 16
Return: success, tag 1
// ok, tag = "1"
```

Allocate 2K more (expected to fail since our total heap is only 1K):
```
ALLOC 2048
Return: error, allocation failed
// allocation failed
```

Now let's see what the heap looks like:
```
SHOW
1...............
................
................
................

<Allocations by tag>
1: 16 bytes
```
Notice that tag `1` is shown indicating that a page was allocated for it.

Let's allocate `32` more bytes:
```
ALLOC 32
Return: success, tag 2
// ok, tag = "2"
```

Check:
```
SHOW
122.............
................
................
................

<Allocations by tag>
1: 16 bytes
2: 32 bytes
```

Now let's deallocate the first allocation using its tag `1`:
```
DEALLOC tag=1
Return: Success
// deallocation succeeded
```

Now deallocate an unknown tag:
```
DEALLOC tag=99
Return: Failed, unknown tag
// deallocation failed, unknown tag
```

Check:
```
SHOW
.22.............
................
................
................

<Allocations by tag>
2: 32 bytes
```

## NOTES
* Display matrix sizing can be chosen arbitrarily and does not need to match
  the example above
* The tag uses strings "1", "2", etc. This is also only a suggestion


# Stretch Addon 1

> NOTE: This is purely optional and will not affect scoring or the interview
> evaluation process. We understand many candidates have existing jobs
> demanding their attention.

If you reach this stage, consider adding support for a command `DEFRAG` that
will perform defragmentation of the memory buffer if possible.

## Example
Continuing the above example:

```

SHOW
.22.............
................
................
................

<Allocations by tag>
2: 32 bytes

DEFRAG
Return: Success


SHOW
22..............
................
................
................

<Allocations by tag>
2: 32 bytes

```

# Submission

You are to build a service that responds over HTTP. Unless you have been instructed otherwise, you may use
any of the following languages/stacks:

* Golang
* Java
* Python 2/3
* Node.js

If you have a different one you prefer, please contact your interviewer.

## DOs
* Fork this repo to your own user space, and add @scorpiodawg, @trevorhalvorson, @grimkey
  as outside collaborators. If you prefer to keep your work private, you may
  [duplicate](https://help.github.com/en/articles/duplicating-a-repository)
  it instead.
* Push all changes to the `master` branch of your fork, and let us know when you're ready
  to officially submit. We will fork your repo and review your code. Changes made after that
  will likely be ignored, so please do submit only after you are ready
* Do ensure your final submission includes everything so that the project can be
  **built, deployed, runnable and accessible locally**.
* Do write self-documenting code
* Do include a `SOLUTION.md` with the following:
  * what major design decisions did you have to make
  * why did you choose any frameworks or libraries used
  * a references section listing any _significant_ resources used during development
    (significant StackOverflow questions, articles, books, etc. You do not need to
    include links to questions about language syntax, for e.g.)
* Do implement your solution as though you intend to productionize it eventually

## DONTs
- Don't jump right in if you have any doubts as to whether your choice of language/
  framework might not be suitable for our review (e.g. less common ones such as
  Clojure, Fortran, Elixir, etc.). Please contact us ahead of time to clarify.

## Timing
We expect this project to take approximately 2-6 hours of your time. However,
everyone is different so please treat these numbers as suggestions. If you find
yourself blocked, or stuck spending inordinate amounts of time in a specific area,
it might be best to reach out to us -- we want to be respectful of your time and
it is our job to ensure you have a pleasant experience.

## Feedback

We are always looking for feedback on the question itself. If, after reading through the
problem, you have questions on its clarity or suggestions on how to improve it, we're all
ears! You may leave a file named `FEEDBACK.md` when you submit your code.

**We look forward to seeing your work!**
