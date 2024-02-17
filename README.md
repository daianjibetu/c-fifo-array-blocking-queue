# C FIFO Array Blocking Queue Integer

To use this blocking queue, define `C_FEK_ARRAY_BLOCKING_QUEUE_INTEGER_IMPLEMENTATION` before including blocking_queue.h in one of your source files.

To use this blocking queue, you MUST link your binary with pthread.

> Note: When using this blocking queue, make sure you are linking to pthreads. If using gcc, just use `-lpthread`. Keep in mind that `libc` provides dummy implementation for most of pthread functions by default, so if you forget linking to pthreads, your program might compile and link just fine. The queue, however, will not work for "mysterious" reasons.

Note that fair_lock.h is a pre-requisite for this implementation, so you also need to include fair_lock.h in one of your source files
and define `C_FEK_FAIR_LOCK_IMPLEMENTATION` before including it.

This blocking queue is thread-safe.

This blocking queue has the following properties/features:

- It has a fixed capacity, provided by the caller.
- It allows the caller to add elements to the queue via both a blocking call and a non-blocking call.
- It allows the caller to get elements from the queue via both a blocking call and a non-blocking call.
- If multiple callers are blocked adding/getting an element to/from the queue, they are served in FIFO order.

The last point avoids the problem of starvation.

Note that since the queue is designed to serve callers in FIFO order, this might have a significant impact in performance.

If FIFO is not needed in your implementation, consider using a queue with no such guarantee.

Define `C_FEK_ARRAY_BLOCKING_QUEUE_INTEGER_NO_CRT` if you don't want the C Runtime Library included. If this is defined, you must provide
implementations for the following functions:

```c
void* malloc(size_t size);
void free(void* block);
```

For more information about the API, check the comments in the function signatures.

An usage example:

```c
#define C_FEK_ARRAY_BLOCKING_QUEUE_INTEGER_IMPLEMENTATION
#define C_FEK_FAIR_LOCK_IMPLEMENTATION
#include "blocking_queue.h"
#include <assert.h>

int main() {
	Blocking_Queue bq;
	int aux;

	blocking_queue_init(&bq, 4);
	
	// Example of non-blocking calls
	blocking_queue_add(&bq, 1);
	blocking_queue_poll(&bq, &aux);
	assert(aux == 1);

	// Example of potentially blocking calls
	blocking_queue_put(&bq, 2);
	blocking_queue_take(&bq, &aux);
	assert(aux == 2);

	blocking_queue_destroy(&bq);
	return 0;
}
```
