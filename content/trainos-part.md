+++
title = "TrainOS: microkernel from scratch PART 1"
date = 2023-10-03
draft = true
+++

So as part of the [Real-Time Programming](https://uwflow.com/course/cs452)
course at school, we are tasked with writing our own RTOS from scratch, with
the ultimate goal of corrdinating a set of model trains. This course is
nortourious for being one of the most time consuming courses in the CS
department, so I was really looking forward to it :skull:. This will be a blog series
documenting the struggles and mishaps through this process.

As part of the first deadline, context switching and a primitive task system
should be implemented.

## Project Setup

Our microkernel is intended to run on bare metal raspberry pi, with a special
'engineering hat' that gives the pi two UART ports, one for communicating with
the terminal, and the other for the train controller. We got to play around
with the train controller as part of the A0 assignment, sort of like a preview
into the course. The train controller is a absolutely ancient piece of hardware
called the *Märklin*, which is so old the documentation for how to use it is
given in BASIC.

We are given a starter Makefile for compiling our program into an image that
can run on the pi as well as some IO code for the UART. Thanks to this, getting
a 'hello world' program up and running was quick and easy.

From here on out it's just us and the processor, no operating system and no
abstractions. Let's get started shall we?

## Memory Allocator

In order to have context switching we need to introduce the concept of tasks.
To have these tasks, we need some sort of global task manager that can create
tasks for us and hand out Task IDs for us to use. Right away, I would like to 
employ some basic _software engineering_ and make use of opaque types for code
encapsulation. This is when we run into our first issue. *We don't have an OS*,
and thus, we don't have `malloc()`.

So natrually, our first order of buisness was to write our own allocator. Of
course, since we are gigabrain programmers we don't need to any pathetic issues
like fragmentation or fastbins or watchamacollits. I would like to introduce
the most innovative malloc implementation you have every seen. Ready for it?
Here it is:
```c
struct ArenaAllocator {
    size_t cursor;
    unsigned int* buf[ARENA_ALLOCATOR_SIZE];
};

void*
arena_alloc(size_t size)
{
    if (alloc->cursor + size >= ARENA_ALLOCATOR_SIZE) {
        LOG_WARN("arena allocator is out of memory");
        return 0;
    }

    void* ptr = alloc->buf + alloc->cursor;
    alloc->cursor += size;

    return ptr;
}

void
arena_free(void* ptr)
{
    /* NOP :D */
}
```
That's right, our heap is just a buffer with a cursor that constantly gets
advanced forward. Of course `free()` is nonesense and it should natrually be a
NO-OP. Perhaps in the future when the need arises a *proper* malloc will be
implemented, but this should get us up and running for now.

## Tasks

Our task table is also equally primitive to start, it's just a fixed buffer of
pointers to task objects, where the index of the task is also it's task id.
Perhaps in the future we could make use of some sort of task id reclamation?
Anyways, for starters, here is our task struct:
```c
typedef struct {

    // switchframe is at beginning of struct for easy access
    SwitchFrame* sf;

    Tid tid;
    Tid parent_tid;
    TaskState state;
    uint32_t priority;
    Addrspace addrspace;

} Task;
```

And we can just create a big array in static memory where the index corresponds
to the task id. When we create a new task we can just move a pointer to the
next avaliable task id. This is how we store task data. Currently super simple
and dumb, but it will get the job done.

## Context Switching

Now that we have tasks, we would like to be able to run one. Our kernel starts
booted in kernelmode, so the first order of buisness is to figure out how to
switch to usermode. 

## System Calls


