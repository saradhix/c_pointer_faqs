# Memory and Pointers
Pointers and Memory

What is shallow and deep copying ?
Two pointers which both refer to a single pointee are said to be "sharing". That two or more entities can cooperatively share a single memory structure is a key advantage of pointers in all computer languages. Pointer manipulation is just technique — sharing is often the real goal.

In particular, sharing can enable communication between two functions. One function passes a pointer to the value of interest to another function. Both functions can access the value of interest, but the value of interest itself is not copied. This communication is called "shallow" since instead of making and sending a (large) copy of the value of interest, a (small) pointer is sent and the value of interest is shared. The recipient needs to understand that they have a shallow copy, so they know not to change or delete it since it is shared. The alternative where a complete copy is made and sent is known as a "deep" copy. Deep copies are simpler in a way, since each function can change their copy without interfering with the other copy, but deep copies run slower because of all the copying.

When and array is passed to a function, “shallow copying” takes place where only a pointer to the first element of the array gets copied to the stack.
When a structure is passed to a function, “deep copying” takes place where all the bytes of the structure are copied to the stack.

In malloc, the number of bytes to allocate is specified as a parameter. However there is no such parameter to free for freeing memory. 
malloc function maintains the number of bytes allocated at the beginning of the block. While calling free, free knows how many bytes actually allocated from malloc and will free those many bytes.
I allocated some memory, but I want to use only a part of it. Why am I unable to free only a part of it ?
You should not free only a part of memory using free. Instead use realloc to grow or shrink the dynamic memory. 

Can a pointer point to a memory location which is allocated in another function.
Yes. Heap, unlike stack is global to all functions. Hence any memory allocated through malloc is accessible to all the functions as long as the pointer to the start of the memory is passed to the function.


int *create_array(void)
{
  int *ptr=malloc(10*sizeof(int));//assume malloc succeeds

  for(i=0;i<10;i++)
    ptr[i]=i;
  return arr;
}

main()
{
  int *x=create_array();
  for(i=0;i<10;i++)
    printf("Value of x[%d]=%d\n",i,x[i]);
}

In the above example, the create_array returns the address of dynamically allocated memory and passes the same to the variable x in the function call. main will print the array elements of arr which is created in create_array function.

What is stack and heap ?
The stack is the memory set aside as scratch space for a thread of execution. When a function is called, a block is reserved on the top of the stack for local variables and some bookkeeping data. When that function returns, the block becomes unused and can be used the next time a function is called. The stack is always reserved in a Last In First Out order; the most recently reserved block is always the next block to be freed. This makes it really simple to keep track of the stack; freeing a block from the stack is nothing more than adjusting one pointer.

The heap is memory set aside for dynamic allocation. Unlike the stack, there's no enforced pattern to the allocation and deallocation of blocks from the heap; you can allocate a block at any time and free it at any time. This makes it much more complex to keep track of which parts of the heap are allocated or free at any given time; there are many custom heap allocators available to tune heap performance for different usage patterns.

Each thread gets a stack, while there's typically only one heap for the application (although it isn't uncommon to have multiple heaps for different types of allocation).
The OS allocates the stack for each system-level thread when the thread is created. Typically the OS is called by the language runtime to allocate the heap for the application.
The stack is attached to a thread, so when the thread exits the stack is reclaimed. The heap is typically allocated at application startup by the runtime, and is reclaimed when the application (technically process) exits.
The size of the stack is set when a thread is created. The size of the heap is set on application startup, but can grow as space is needed (the allocator requests more memory from the operating system).
The stack is faster because the access pattern makes it trivial to allocate and deallocate memory from it (a pointer/integer is simply incremented or decremented), while the heap has much more complex bookkeeping involved in an allocation or free. Also, each byte in the stack tends to be reused very frequently which means it tends to be mapped to the processor's cache, making it very fast.
What is a dangling pointer ?
Freeing memory explicitly or by destroying the stack frame on return does not alter associated pointers. The pointer still points to the same location in memory even though the reference has since been deleted and may now be used for other purposes. Such pointer, which at one time pointing to a valid data, becomes a dangling pointer.
main()
{
   char *dp = NULL;
   /* ... */
   {
       char c;
       dp = &c;
   } /* c falls out of scope */
     /* dp is now a dangling pointer */
}
Another example of dangling pointer
main()
{
char *p=malloc(1024);
…
free(p);/*p is a dangling pointer now*/
…
*p=’x’;/*Doing this is illegal and might cause undefined behaviour*/

An all too common misstep is returning addresses of a stack-allocated local variable: once a called function returns, the space for these variables gets deallocated and technically they have "garbage values".
int *func(void)
{
    int num = 1234;
    /* ... */
    return &num;
}

Attempts to read from the pointer may still return the correct value (1234) for a while after calling func, but any functions called thereafter will overwrite the stack storage allocated for num with other values and the pointer would no longer work correctly. If a pointer to num must be returned, num must have scope beyond the function--it might be declared as static.

What is realloc ? 
The signature of realloc is 
void *realloc(void *ptr, size_t size )
which behaves differently based on the input arguments
if ptr is NULL then it is same as malloc(size) is called.
realloc changes the area of memory pointed by ptr to size bytes.
If the space can be continuously allocated after ptr, the memory size is extended and the same ptr is returned. If the space is not available continuously after ptr it is checked if the size of memory can be allocated somewhere else. If so, the data pointed by ptr is copied byte by byte to the new location and the new address is returned. If size bytes are not available NULL is returned.
realloc  always succeeds in shrinking a block.
if size is 0 and ptr is not NULL then it acts like free(ptr) (and always returns NULL).

Why am I able to passs any argument to free ?
Free does not validate if the address to be freed is really allocated by malloc/calloc. Free does not return the memory to the OS for its use. It just marks a specific block as available so that future memory requests can be served off from the marked chunk if possible. 

What are the common causes of segmentation fault ?
Segmentation fault might be caused due to one or more of the below conditions.
Dereferencing a pointer that doesn't contain a valid value
Dereferencing a null pointer (often because the null pointer was returned from a system routine, and used without checking)
Accessing something without the correct permission—for example, attempting to store a value into a read-only text segment would cause this error
Running out of stack or heap space (virtual memory is huge but not infinite)
Why is my malloc is dumping core ?
Malloc maintains the table of allocated memory(and freed memory) in its internal data structures. Corrupting these data structures may cause malloc to dump core. Freeing allocated memory twice might cause malloc to dump core. Freeing unallocated memory might cause the problem. One specific cause, which is little difficult to root cause is when calls to malloc interleave(causing malloc to be called simultaneously before the first malloc returned) in a multithreaded environment. Even in case of non multithreaded environment, if a signal handler tries to allocate memory and another signal preempts the execution of malloc, it can create a similar situation and dump core. Hence it is a good practice to use only re-entrant functions in signal handlers, if at all they have to be used.
Why is my free dumping core ?
Free can dump core for a variety of reasons
Freeing memory from stack
Freeing null or invalid memory
Freeing non-starting memory even though allocated from the malloc call.
Memory overwrites on the block header

How do I write wrappers for malloc and free functions?
Glib C specific solution: If your compilation environment is glibc with gcc, the preferred way is to use malloc hooks. Not only it lets you specify custom malloc and free, but will also identify the caller by the return address on the stack. An example using malloc hooks is shown below

/* Prototypes for __malloc_hook, __free_hook */
     #include <malloc.h>
     
     /* Prototypes for our hooks.  */
     static void my_init_hook (void);
     static void *my_malloc_hook (size_t, const void *);
     static void my_free_hook (void*, const void *);
     
     /* Override initializing hook from the C library. */
     void (*__malloc_initialize_hook) (void) = my_init_hook;
     
     static void my_init_hook (void)
     {
       old_malloc_hook = __malloc_hook;
       old_free_hook = __free_hook;
       __malloc_hook = my_malloc_hook;
       __free_hook = my_free_hook;
     }
     
     static void *my_malloc_hook (size_t size, const void *caller)
     {
       void *result;
       /* Restore all old hooks */
       __malloc_hook = old_malloc_hook;
       __free_hook = old_free_hook;
       /* Call recursively */
       result = malloc (size);
       /* Save underlying hooks */
       old_malloc_hook = __malloc_hook;
       old_free_hook = __free_hook;
       /* printf might call malloc, so protect it too. */
       printf ("malloc (%u) returns %p\n", (unsigned int) size, result);
       /* Restore our own hooks */
       __malloc_hook = my_malloc_hook;
       __free_hook = my_free_hook;
       return result;
     }
     
     static void my_free_hook (void *ptr, const void *caller)
     {
       /* Restore all old hooks */
       __malloc_hook = old_malloc_hook;
       __free_hook = old_free_hook;
       /* Call recursively */
       free (ptr);
       /* Save underlying hooks */
       old_malloc_hook = __malloc_hook;
       old_free_hook = __free_hook;
       /* printf might call free, so protect it too. */
       printf ("freed pointer %p\n", ptr);
       /* Restore our own hooks */
       __malloc_hook = my_malloc_hook;
       __free_hook = my_free_hook;
     }
     
     main ()
     {
       ...
     }
     
POSIX specific solution:
Another solution is to define malloc and free as wrappers to the original allocation routines in your executable, which will "override" the version from libc. Inside the wrapper you can call into the original malloc implementation, which you can look up using dlsym with RTLD_NEXT handle. Your application or library that defines wrapper functions needs to link with -ldl.

#define _GNU_SOURCE
#include <dlfcn.h>
#include <stdio.h>

void* malloc(size_t sz)
{
    void *(*libc_malloc)(size_t) = dlsym(RTLD_NEXT, "malloc");
    printf("malloc\n");
    return libc_malloc(sz);
}

void free(void *p)
{
    void (*libc_free)(void*) = dlsym(RTLD_NEXT, "free");
    printf("free\n");
    libc_free(p);
}

int main()
{
    free(malloc(10));
    return 0;
}

Linux specific. You can override functions from dynamic libraries non-invasively by specifying them in the LD_PRELOAD environment variable.

$LD_PRELOAD=mymalloc.so ./a.out
Assuming a.out is the executable binary. mymalloc.so is the shared library containing the malloc and free wrappers.
What would malloc(0) do ?
If the size of the space requested is zero, the behavior of malloc is implementation defined: either a null pointer is returned, or the behavior is as if the size were some nonzero value, except that the returned pointer shall not be used to access an object. 

What is the life and scope of memory allocated with malloc ?
The lifetime of an allocated object extends from the allocation until the deallocation. The scope of the memory is the same as the scope of the variable which points to the memory. 
What happens if the program exits before freeing memory allocated with malloc ? 
Freeing unused memory is a good idea, but it's not mandatory. When your program exits, any memory which it has allocated but not freed should be automatically released.

How do I increase the size of an array which is dynamically allocated ? 
Realloc can be used for this. Given a region of malloced memory , its size can be changed using the below code:

int *dynarray;
dynarray = malloc(10 * sizeof(int));  //malloc 10 ints

dynarray = realloc(dynarray, 20 * sizeof(int));   //increase the memory to hold 20 ints

What are the precautions to be taken while using realloc ?
Sometimes when realloc cannot find enough space at all, it returns a null pointer, and leaves the previous region allocated. Therefore, instead of immediately assigning the new pointer to the old variable, use a temporary pointer:

#include <stdio.h>
#include <stdlib.h>

int *newarray = (int *)realloc((void *)dynarray, 20 * sizeof(int));
if(newarray != NULL)
	dynarray = newarray;
else
 {
	fprintf(stderr, "Can't reallocate memory\n");
	/* dynarray remains allocated */
}
When reallocating memory, be careful if there are any other pointers lying around which point into the original memory: if realloc must locate the new region somewhere else, those other pointers must also be adjusted. Here is an example:

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char *p, *p2, *newp;
int tmpoffset;

p = malloc(10);
strcpy(p, "Hello,");		/* p is a string */
p2 = strchr(p, ',');		/* p2 points into that string */

tmpoffset = p2 - p;
newp = realloc(p, 20);
if(newp != NULL) 
{
	p = newp;		/* p may have moved */
	p2 = p + tmpoffset;	/* relocate p2 as well */
	strcpy(p2, ", world!");
}

printf("%s\n", p);
It is safe to recompute pointers based on offsets, as in the code fragment above. The alternative--relocating pointers based on the difference, newp - p, between the base pointer's value before and after the realloc--is not guaranteed to work, because pointer subtraction is only defined when performed on pointers into the same object.

What is the difference between malloc and calloc other than the number of arguments and zero initialisation ?
calloc(m, n) is essentially equivalent to
p = malloc(m * n);
memset(p, 0, m * n);
There is no important difference between the two other than the number of arguments and the zero fill.
What is the difference between memcpy and memmove ? 
With memcpy, the destination cannot overlap the source at all. With memmove it can. This means that memmove might be very slightly slower than memcpy, as it cannot make the same assumptions.

For example, memcpy might always copy addresses from low to high. If the destination overlaps after the source, this means some addresses will be overwritten before copied. memmove would detect this and copy in the other direction - from high to low - in this case. However, checking this and switching to another (possibly less efficient) algorithm takes time.


char str[] = "ABCDEFGHIJKLMNOP";
memcpy(&str[5],&str[1],10); //causes memory overwrite
printf("Str=%s\n",str);
prints 
Str=ABCDEBCDEBCDEBCP

The above code when replaced with memmove like this gives
memmove(&str[5],&str[1],10); //fine
prints
Str=ABCDEBCDEFGHIJKP

It is safe to use memmove even when the source and destination do not overlap. Better safe than sorry.

How do I allocate memory on the stack ?
To allocate memory dynamically from the stack, use the alloca() system call:

#include <alloca.h>
void *alloca(size_t size)
Important point to note is that you should neve use memory allocated with alloca() in the parameters to a function call, because, the allocated memory will then exist in the middle of stack space reserved for the function parameters. For example, the following is wrong

ret=fun(x,alloca(16));
On many systems alloca() behaved poorly, or the behaviour was undefined. On systems with small and fixed size stack, using alloca() is an easy way to overflow the stack and kill your program.

If your program has to be portable, you should avoid using alloca().

However on Linux, alloca() performs exceptionally well, and on many architectures, alloca() does as little as increment the stack pointer and outpeforms malloc in terms of efficiency. For small allocations in Linux specific code, alloca() is very efficient and has significance performance gains compared to malloc.

Note that unlike memory alloced from malloc which has to be freed, memory allocated from alloca need not be freed, because it gets freed once the function where a call to alloca is present returns, very similar to the automatic variables.


