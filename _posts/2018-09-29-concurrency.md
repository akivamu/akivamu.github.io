---
layout: post
title: Concurrency programming
tags:
- Algorithms
---

Some concepts/definitions:

- Parallel/concurrent parties:
  - They can be multi threads, multi processes or multi nodes in network
  - They can be executed in parallel
- Critical section, shared resource:
  - Could a memory block, a database or file, that above parties access
  at the same time

Working in parallel, there could be some issues:

- Race condition: e.g. 2 threads race to read and write to a shared memory location, causing
unpredictable result. See [simple example](https://en.wikipedia.org/wiki/Race_condition#Software)
- Dead lock: e.g. each thread is waiting for another to take action, causing every parties
are waiting against others, and the excuting is hang.

## Lock, spinlock, mutex

It's all about resource sharing. Locking is designed to enforce mutual exclusion (limited access) on a shared resource:

- The shared resource is associated with a lock
- Each party need to acquire that lock before access the resource

=> Lock make sure parallel accesses become serial.

An analogy about locking is toilet accessing:

- The toilet: the shared resource
- The key to toilet: the lock

Whenever anyone needs access to the toilet (resource), he needs to acquire the key (lock).  
If the key is not available (means the toilet is locked), he needs to wait (There may be multiple
person waiting at the same time)  
When the key is returned (lock is release), the waiting people will contend to acquire the lock
(Who would acquire the lock should be decided by system, bases on set of rules)  
The one who takes the lock then use the toilet exclusive.

The waiting person behaviour could be:

- 1- Regularly checking if the key is released
  - This is called: spinlock
- 2- Go sleep. When the current user releases his lock, he will notify to all waiters. The waiters are waked up.
  - This is called: mutex

Go sleep and wake up operations are expensive, so should be careful when choose spinlock or mutex.  
Read [Spinlock vs Mutex](https://stackoverflow.com/a/5870415/6445037)

Using lock, we has to pay for overhead: lock needs extra memory, CPU operation to test/acquire/release.  
When thread try to acquire the lock, if not free, there is difference:

### Implementation

An example to implement spinlock, using [test-and-set instruction](https://en.wikipedia.org/wiki/Test-and-set)

```C
function Lock(boolean *lock) {
    while (test_and_set(lock) == 1);
}
```

The `test_and_set()` must be atomic, usually with the help of hardware.

In software, we can implement it like this (with atomic constraint):

```C
#define LOCKED 1

int TestAndSet(int* lockPtr) {
    int oldValue;

    // -- Start of atomic segment (example only) --
    oldValue = *lockPtr;
    *lockPtr = LOCKED;
    // -- End of atomic segment --

    return oldValue;
}
```

## Semaphore

### Binary semaphore vs lock

Using above analogy about toilet:

- The toilet: the shared resource
- The key to toilet: the semaphore (binary)

The binary semaphore has 2 operations on it:

- Take: try to acquire the key. If the key is not available, go sleep.
- Give: return the key and notify the waiters

In this analogy, binary semaphore looks similar to lock: take and give are performed by single user
(exclusive to a single user)

### Producer and consumer

Let's take another analogy: produce and consume bread.

Here we have the bread maker (producer) and bread eater (consumer).  
The consumer must wait for the bread ready. If no bread available, it must wait.  
The producer makes bread and notify the consumer.

Semaphore is difference to the lock: take and give are performed by both users:

- The consumer will take
- The producer will give

Of course the resource here (bread) is difference to the key above. So is the concept of semaphore and lock

In short:

- A lock (mutex, spinlock) is a locking mechanism
- Semaphore is signaling mechanism

## References

- [difference-between-binary-semaphore-and-mutex](https://stackoverflow.com/questions/62814)
- [lock-mutex-semaphore-whats-the-difference](https://stackoverflow.com/questions/2332765)
- [Mutex vs Semaphore](http://www.barrgroup.com/Embedded-Systems/How-To/RTOS-Mutex-Semaphore)
- [Concurrency-Examples](https://see.stanford.edu/materials/icsppcs107/23-Concurrency-Examples.pdf)
