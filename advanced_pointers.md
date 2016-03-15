# Advanced Pointers
Advanced Pointers

Please summarise about the interchangeability of arrays and pointers
1. An array access a[i] is always "rewritten" or interpreted by the compiler as a pointer access *(a+i);
2. Pointers are always just pointers; they are never rewritten to arrays.  You can apply a subscript to a pointer; you typically do this when the pointer is a function argument, and you know that you will be passing an array in.
3. An array declaration in the specific context (only) of a function parameter can equally be written as a pointer. An array that is a function argument (i.e., in a call to the function) is always changed, by the compiler, to a pointer to the start of the array.
4. Therefore, you have the choice for defining a function parameter which is an array, either as an array or as a pointer. Whichever way you define it, you actually get a pointer inside the function.
5. In all other cases, definitions should match declarations. If you defined it as an array, your extern declaration should be an array. The same applies true for a pointer.

How is malloc implemented internally?
One of the implementations of malloc can be found at http://g.oswego.edu/dl/html/malloc.html and at http://www.ibm.com/developerworks/linux/library/l-memory.
Both of them are good to read and understand the intricacies of the malloc and free functions.
What is left right rule for understanding complicated pointer constructs?
Declarations in C are read boustrosphedonically, i.e alternating right-to-left with left-to-right. This is also known as "right-left" rule. The "right-left" rule is a completely regular rule for deciphering C declarations.  It can also be useful in creating them.

First, symbols.  Read

*	as "pointer to"	- always on the left side
[] 	as "array of"	- always on the right side
()	as "function returning"- always on the right side

as you encounter them in the declaration.

STEP 1
Find the identifier.  This is your starting point.  Then say to yourself,
"identifier is."  You've started your declaration.

STEP 2
Look at the symbols on the right of the identifier.  If, say, you find "()"
there, then you know that this is the declaration for a function.  So you
would then have "identifier is function returning".  Or if you found a 
"[]" there, you would say "identifier is array of".  Continue right until
you run out of symbols *OR* hit a *right* parenthesis ")".  (If you hit a 
left parenthesis, that's the beginning of a () symbol, even if there
is stuff in between the parentheses.  More on that below.)

STEP 3
Look at the symbols to the left of the identifier.  If it is not one of our
symbols above (say, something like "int"), just say it.  Otherwise, translate
it into English using that table above.  Keep going left until you run out of
symbols *OR* hit a *left* parenthesis "(".  

Now repeat steps 2 and 3 until you've formed your declaration.  Here are some
examples:
     int *p[];
1) Find identifier.          int *p[];
                                                    ^
   Say "p is"

2) Move right until out of symbols or left parenthesis hit.
                             int *p[];
                                      ^^
   Say "p is array of"

3) Can't move right anymore (out of symbols), so move left and find:
                             int *p[];
                                   ^
   "p is array of pointer to"

4) Keep going left and find:
                             int *p[];
                             ^^^
   "p is array of pointer to int". 
   (or "p is an array where each element is of type pointer to int")

Another example:

   int *(*func())();

1) Find the identifier.      int *(*func())();
                                                      ^^^^
   "func is"

2) Move right.               int *(*func())();
                                                           ^^
   "func is function returning"

3) Can't move right anymore because of the right parenthesis, so move left.
                             int *(*func())();
                                      ^
   "func is function returning pointer to"

4) Can't move left anymore because of the left parenthesis, so keep going
   right.                    int *(*func())();
                                                        ^^
   "func is function returning pointer to function returning"

5) Can't move right anymore because we're out of symbols, so go left.
                             int *(*func())();
                                   ^
   "func is function returning pointer to function returning pointer to"

6) And finally, keep going left, because there's nothing left on the right.
                             int *(*func())();
                             ^^^
   "func is function returning pointer to function returning pointer to int".

As you can see, this rule can be quite useful.  You can also use it to
sanity check yourself while you are creating declarations, and to give
you a hint about where to put the next symbol and whether parentheses
are required.

Some declarations look much more complicated than they are due to array
sizes and argument lists in prototype form.  If you see "[3]", that's
read as "array (size 3) of...".  If you see "(char *,int)" that's read
as "function expecting (char *,int) and returning...".  Here's a fun
one:

                 int (*(*fun_one)(char *,double))[9][20];

I won't go through each of the steps to decipher this one.

     "fun_one is pointer to function expecting (char *,double) and 
      returning pointer to array (size 9) of array (size 20) of int."

As you can see, it's not as complicated if you get rid of the array sizes
and argument lists:

     int (*(*fun_one)())[][];

You can decipher it that way, and then put in the array sizes and argument
lists later.

Some final words:

It is quite possible to make illegal declarations using this rule,
so some knowledge of what's legal in C is necessary.  For instance,
if the above had been:

     int *((*fun_one)())[][];

it would have been "fun_one is pointer to function returning array of array of
pointer to int".  Since a function cannot return an array, but only a  pointer to an array, that declaration is illegal.

Illegal combinations include:

[]() - cannot have an array of functions
()() - cannot have a function that returns a function
()[] - cannot have a function that returns an array

In all the above cases, you would need a set of parens to bind a * symbol on the left between these () and [] right-side symbols in order for the declaration to be legal.
You can use www.http://cdecl.org to convert C declarations to English and vice versa.

Give some examples of complicated pointer constructs.
int i;                  an int
int *p;                 an int pointer (ptr to an int)
int a[];                an array of ints
int f();                a function returning an int
int **pp;               a pointer to an int pointer (ptr to a ptr to an int)
int (*pa)[];            a pointer to an array of ints
int (*pf)();            a pointer to a function returning an int
int *ap[];              an array of int pointers (array of ptrs to ints)
int aa[][];             an array of arrays of ints
int af[]();             an array of functions returning an int (ILLEGAL)
int *fp();              a function returning an int pointer
int fa()[];             a function returning an array of ints (ILLEGAL)
int ff()();             a function returning a function returning an int
                                (ILLEGAL)
int ***ppp;             a pointer to a pointer to an int pointer
int (**ppa)[];          a pointer to a pointer to an array of ints
int (**ppf)();          a pointer to a pointer to a function returning an int
int *(*pap)[];          a pointer to an array of int pointers
int (*paa)[][];         a pointer to an array of arrays of ints
int (*paf)[]();         a pointer to a an array of functions returning an int
                                (ILLEGAL)
int *(*pfp)();          a pointer to a function returning an int pointer
int (*pfa)()[];         a pointer to a function returning an array of ints
                                (ILLEGAL)
int (*pff)()();         a pointer to a function returning a function
                                returning an int (ILLEGAL)
int **app[];            an array of pointers to int pointers
int (*apa[])[];         an array of pointers to arrays of ints
int (*apf[])();         an array of pointers to functions returning an int
int *aap[][];           an array of arrays of int pointers
int aaa[][][];          an array of arrays of arrays of ints
int aaf[][]();          an array of arrays of functions returning an int
                                (ILLEGAL)
int *afp[]();           an array of functions returning int pointers (ILLEGAL)
int afa[]()[];          an array of functions returning an array of ints
                                (ILLEGAL)
int aff[]()();          an array of functions returning functions
                                returning an int (ILLEGAL)
int **fpp();            a function returning a pointer to an int pointer
int (*fpa())[];         a function returning a pointer to an array of ints
int (*fpf())();         a function returning a pointer to a function
                                returning an int
int *fap()[];           a function returning an array of int pointers (ILLEGAL)
int faa()[][];          a function returning an array of arrays of ints
                                (ILLEGAL)
int faf()[]();          a function returning an array of functions
                                returning an int (ILLEGAL)
int *ffp()();           a function returning a function
                                returning an int pointer (ILLEGAL)

Can I write a program which dereferences NULL and still does not cause segmentation violation ?
This is an advanced trick and be warned that such implementations should never be present in production quality code. The below example is for enhancing the knowledge.
On a 32bit multiprocessing OS like Linux, each process has its own 2^32 possible addresses, not all of them need to be valid (correspond to real memory). In particular, by default, the NULL or 0 pointer does not correspond to valid memory, which is why accessing it leads to a crash.

Because each application has its own address space, however, it is free to do with it as it wants. For instance, you're welcome to declare that NULL should be a valid address in your application. We refer to this as "mapping" the NULL page, because you're declaring that that area of memory should map to some piece of physical memory.

On Linux (and other UNIX) systems, the function call used for mapping regions of memory is mmap(2). mmap is defined as:

void *mmap(void *addr, size_t length, int prot, int flags,
           int fd, off_t offset);
Let's go through those arguments in order (All of this information comes from the man page):

addr :This is the address where the application wants to map memory. If MAP_FIXED is not specified in flags, mmap may select a different address if the selected one is not available or inappropriate for some reason.
length :The length of the region the application wants to map. Memory can only be mapped in increments of a "page", which is 4k (4096 bytes) on x86 processors.
prot :Short for "protection", this argument must be a combination of one or more of the values PROT_READ, PROT_WRITE, PROT_EXEC, or PROT_NONE, indicating whether the application should be able to read, write, execute, or none of the above, the mapped memory.
flags :Controls various options about the mapping. There are a number of flags that can go here. Some interesting ones are MAP_PRIVATE, which indicates the mapping should not be shared with any other process, MAP_ANONYMOUS, which indicates that the fd argument is irrelevant, and MAP_FIXED, which indicates that we want memory located exactly at addr.
fd : The primary use of mmap is not just as a memory allocator, but in order to map files on disk to appear in a process's address space, in which case fd refers to an open file descriptor to map. Since we just want a random chunk of memory, we're going pass MAP_ANONYMOUS in flags, which indicates that we don't want to map a file, and fd is irrelevant.
offset :This argument would be used with fd to indicate which portion of a file we wanted to map.
mmap returns the address of the new mapping, or MAP_FAILED if something went wrong.
If we just want to be able to read and write the NULL pointer, we'll want to set addr to 0 and length to 4096, in order to map the first page of memory. We'll need PROT_READ and PROT_WRITE to be able to read and write, and all three of the flags I mentioned. fd and offset are irrelevant; we'll set them to -1 and 0 respectively.

Putting it all together, we get the following short C program, which successfully reads and writes through a NULL pointer without crashing!

(Note that most modern systems actually specifically disallow mapping the NULL page, out of security concerns. To run the following example on a recent Linux machine at home, you'll need to run # echo 0 > /proc/sys/vm/mmap_min_addr as root, first.)

#include <sys/mman.h>
#include <stdio.h>

int main()
{
int *ptr = NULL;
if (mmap(0, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_FIXED, -1, 0)== MAP_FAILED)
{
perror("Unable to mmap(NULL)");
fprintf(stderr, "Is /proc/sys/vm/mmap_min_addr non-zero?\n");
return 1;
}
printf("Dereferencing my NULL pointer yields: %d\n", *ptr);
*ptr = 17;
printf("Now it's: %d\n", *ptr);
return 0;
}

Does an array always get converted to a pointer? What is the difference between arr and &arr? How does one declare a pointer to an entire array?
Not always.

In C, the array and pointer arithmetic is such that a pointer can be used to access an array or to simulate an array. What this means is whenever an array appears in an expression, the compiler automatically generates a pointer to the array's first element (i.e, &a[0]).

There are three exceptions to this rule


1. When the array is the operand of the sizeof() operator.
2. When using the & operator.
3. When the array is a string literal initializer for a character array.


Also, on a side note, the rule by which arrays decay into pointers is not applied recursively!. An array of arrays (i.e. a two-dimensional array in C) decays into a pointer to an array, not a pointer to a pointer.

If you are passing a two-dimensional array to a function:

int myarray[NO_OF_ROWS][NO_OF_COLUMNS];
myfunc(myarray);


then, the function's declaration must match:


void myfunc(int myarray[][NO_OF_COLUMNS])
or
void myfunc(int (*myarray)[NO_OF_COLUMNS])


Since the called function does not allocate space for the array, it does not need to know the overall size, so the number of rows, NO_OF_ROWS, can be omitted. The width of the array is still important, so the column dimension
NO_OF_COLUMNS must be present.

An array is never passed to a function, but a pointer to the first element of the array is passed to the function. Arrays are automatically allocated memory. They can't be relocated or resized later. Pointers must be assigned to allocated memory (by using (say) malloc), but pointers can be reassigned and made to point to other memory chunks.

Whats the difference between func(arr) and func(&arr)?
In C, &arr yields a pointer to the entire array. On the other hand, a simple reference to arr returns a pointer to the first element of the array arr. Pointers to arrays (as in &arr) when subscripted or incremented, step over entire arrays, and are useful only when operating on arrays of arrays. Declaring a pointer to an entire array can be done like int (*arr)[N];, where N is the size of the array.

Also, note that sizeof() will not report the size of an array when the array is a parameter to a function, simply because the compiler pretends that the array parameter was declared as a pointer and sizeof reports the size of the pointer.
Is the cast to malloc() required at all?
Before ANSI C introduced the void * generic pointer, these casts were required because older compilers used to return a char pointer.

int *myarray;
myarray = (int *)malloc(no_of_elements * sizeof(int));
But, under ANSI Standard C, these casts are no longer necessary as a void pointer can be assigned to any pointer. These casts are still required with C++, however.

What does malloc() , calloc(), realloc(), free() do? What are the common problems with malloc()? Is there a way to find out how much memory a pointer was allocated?
malloc() is used to allocate memory. Its a memory manager.

calloc(m, n) is also used to allocate memory, just like malloc(). But in addition, it also zero fills the allocated memory area. The zero fill is all-bits-zero. calloc(m.n) is essentially equivalent to

p = malloc(m * n);
memset(p, 0, m * n);
The malloc() function allocates raw memory given a size in bytes. On the other hand, calloc() clears the requested memory to zeros before return a pointer to it. (It can also compute the request size given the size of the base data structure and the number of them desired.)

The most common source of problems with malloc() are

1. Writing more data to a malloc'ed region than it was allocated to hold.
2. malloc(strlen(string)) instead of (strlen(string) + 1).
3. Using pointers to memory that has been freed.
4. Freeing pointers twice.
5. Freeing pointers not obtained from malloc.
6. Trying to realloc a null pointer.

How does free() work?
Any memory allocated using malloc() realloc() must be freed using free(). In general, for every call to malloc(), there should be a corresponding call to free(). When you call free(), the memory pointed to by the passed pointer is freed. However, the value of the pointer in the caller remains unchanged. Its a good practice to set the pointer to NULL after freeing it to prevent accidental usage. The malloc()/free() implementation keeps track of the size of each block as it is allocated, so it is not required to remind it of the size when freeing it using free(). You shouldn't use dynamically-allocated memory after you free it.

 
Is there a way to know how big a allocated block is?
Unfortunately there is no standard or portable way to know how big an allocated block is using the pointer to the block. And probably you need not know how much was allocated as the memory allocator keeps track of how much is allocated, which is implementation defined. It means compiler writers are at liberty to use their own algorithms to do the memory book keeping. If you have to keep track of the memory allocated, you can write a generic malloc wrapper which keeps track of memory allocated.
Is this a valid expression?

pointer = realloc(0, sizeof(int));
Yes, it is. It is equivalent to this statement
pointer=malloc(sizeof(int))
What's the difference between const char *p, char * const p and const char * const p?

const char *p    -   This is a pointer to a constant char. One cannot change the value pointed at by p, but can change the pointer p itself.

*p = 'A';// This is illegal.
p  = "Hello";// This is legal.

Note that even char const *p is the same!

const * char p   -   This is a constant pointer to (non-const) char. One cannot change the pointer p, but can change the value pointed at by p.

*p = 'A' ; //This is legal.
p  = "Hello";//This is illegal.
 const char * const p  -   This is a constant pointer to constant char! One cannot change the value pointed to by p nor the pointer.

*p = 'A'; //This is illegal.
p  = "Hello"; //This is also illegal.
To interpret these declarations, let us first consider the general form of declaration:

[qualifier] [storage-class] type [*[*]..] [qualifier] ident ;
                                or
 [storage-class] [qualifier] type [*[*]..] [qualifier] ident ;
where,


qualifier can be one of these - volatile, const

storage-class can be one of these – auto, extern, static register

type can be one of these -
void,  char,  short,  int, long, float, double, signed, unsigned,
enum-specifier,  typedef-name,  struct-or-union-specifier

Both the forms are equivalent. Keywords in the brackets are optional. The simplest tip here is to notice the relative position of the `const' keyword with respect to the asterisk (*).

Note the following points:

If the `const' keyword is to the left of the asterisk, and is the only such keyword in the declaration, then object pointed by the pointer is constant, however, the pointer itself is variable. For example:
const char * pcc;
char const * pcc;
If the `const' keyword is to the right of the asterisk, and is the only such keyword in the declaration, then the object pointed by the pointer is variable, but the pointer is constant; i.e., the pointer, once initialized, will always point to the same object through out it's scope. For example:
char * const cpc;
If the ‘const’ keyword is on both sides of the asterisk, the both the pointer and the pointed object are constant. For example:
const char * const cpcc;
char const * const cpcc2;
One can also follow the "nearness" principle; i.e.,

If the `const' keyword is nearest to the `type', then the object is constant. For example:
char const * pcc;
If the `const' keyword is nearest to the identifier, then the pointer is constant. For example:
char * const cpc;
If the `const' keyword is nearest, both to the identifier and the type, then both the pointer and the object are constant. For example:
const char * const cpcc;
char const * const cpcc2;
However, the first method seems more reliable.

What is a void pointer? Why can't we perform arithmetic on a void * pointer?

The void data type is used when no other data type is appropriate. A void pointer is a pointer that may point to any kind of object at all. It is used when a pointer must be specified but its type is unknown.

The compiler doesn't know the size of the pointed-to objects incase of a void * pointer. Before performing arithmetic, convert the pointer either to char * or to the pointer type you're trying to manipulate

What do segmentation fault, access violation, core dump and Bus error mean?

The segmentation fault, core dump, bus error kind of errors usually mean that the program tried to access memory it shouldn't have.

Probable causes are overflow of local arrays; improper use of null pointers; corruption of the malloc() data structures; mismatched function arguments (specially variable argument functions like sprintf(), fprintf(), scanf(), printf()).

For example, the following code is a sure shot way of inviting a segmentation fault in your program:
sprintf(buffer,  "%s %d", "Hello");
What is the difference between a bus error and a segmentation fault?

A bus error is a fatal failure in the execution of a machine language instruction resulting from the processor detecting an anomalous condition on its bus.

Such conditions include:

Invalid address alignment (accessing a multi-byte number at an odd address).
Accessing a memory location outside its address space.
Accessing a physical address that does not correspond to any device.
Out-of-bounds array references.
References through uninitialized or mangled pointers.


A bus error triggers a processor-level exception, which Unix translates into a "SIGBUS" signal,which if not caught, will terminate the current process. It looks like a SIGSEGV, but the difference between the two is that SIGSEGV indicates an invalid access to valid memory, while SIGBUS indicates an access to an invalid address.

Bus errors mean different thing on different machines. On systems such as Sparcs a bus error occurs when you access memory that is not positioned correctly.

The following example will cause a BUS error

int main(void)
{
  char *c;
  long int *i;
  c = (char *) malloc(sizeof(char));
  c++;
  i = (long int *)c;
  printf("%ld", *i);
  return 0;
}

On SPARC machines long ints have to be at addresses that are multiples of four (because they are four bytes long), while chars do not (they are only one byte long so they can be put anywhere). The example code uses the char to create an invalid address, and assigns the long int to the invalid address. This causes a bus error when the long int is dereferenced.

A segfault occurs when a process tries to access memory that it is not allowed to, such as the memory at address 0 (where NULL usually points). It is easy to get a segfault, such as the following example, which dereferences NULL.
int main(void)
{
  char *p;
  p = NULL;
  putchar(*p);
  return 0;
}
What is a memory leak?

Its a scenario where the program has lost a reference to an area in the memory. Its a programming term describing the loss of memory. This happens when the program allocates some memory but fails to return it to the system

What are brk() and sbrk() used for? How are they different from malloc()?

brk() and sbrk() are the only calls of memory management in UNIX. For one value of the address, beyond the last logical data page of the process, the MMU generates a segmentation violation interrupt and UNIX kills the process. This address is known as the break address of a process. Addition of a logical page to the data space implies raising of the break address (by a multiple of a page size). Removal of an entry from the page translation table automatically lowers the break address.

brk()and sbrk() are systems calls for this process

char *brk(char *new_break);
char *sbrk(displacement)

Both calls return the old break address to the process. In brk(), the new break address desired needs to be specified as the parameter. In sbrk(), the displacement (+ve or -ve) is the difference between the new and the old break address. sbrk() is very similar to malloc() when it allocates memory (+ve displacement).

malloc() is really a memory manager and not a memory allocator since, brk/sbrk only can do memory allocations under UNIX. malloc() keeps track of occupied and free peices of memory. Each malloc request is expected to give consecutive bytes and hence malloc selects the smallest free pieces that satisfy a request. When free is called, any consecutive free pieces are coalesced into a large free piece. This is done to avoid fragmentation.

realloc() can be used only with a preallocated/malloced/realloced memory. realloc() will automatically allocate new memory and transfer maximum possible contents if the new space is not available. Hence the returned value of realloc must always be stored back into the old pointer itself.

What is a dangling pointer? What are reference counters with respect to pointers?
A pointer which points to an object that no longer exists. Its a pointer referring to an area of memory that has been deallocated. Dereferencing such a pointer usually produces garbage.

Using reference counters which keep track of how many pointers are pointing to this memory location can prevent such issues. The reference counts are incremented when a new pointer starts to point to the memory location and decremented when they no longer need to point to that memory. When the reference count reaches zero, the memory can be safely freed. Also, once freed, as a good programming practice, the corresponding pointer must be set to NULL.

What is the difference between char *a and char a[] ?

There is a lot of difference!
char a[] = "string";
char *a = "string";
The declaration char a[] asks for space for 7 characters and see that its known by the name "a". In contrast, the declaration char *a, asks for a place that holds a pointer, to be known by the name "a". This pointer "a" can point anywhere. In this case its pointing to an anonymous array of 7 characters, which does have any name in particular. Its just present in memory with a pointer keeping track of its location.

char A[] = "STRING";
The below table shows the memory map of the variable A
S	T	R	I	N	G	\0
A[0]	A[1]	A[2]	A[3]	A[4]	A[5]	A[6]
4000	4001	4002	4003	4004	4005	4006

char *a = "string";
The below tables show the memory map of the pointer a.
1000
5000-5003[ 4 bytes of memory allocated for the pointer which contains the value 1000 which is the address of the anonymous array containing “string”

s	t	r	i	n	g	\0
1000	1001	1002	1003	1004	1005	1006

It is crucial to know that a[3] generates different code depending on whether a is an array or a pointer. When the compiler sees the expression a[3] and if a is an array, it starts at the location "a", goes three elements past it, and returns the character there. When it sees the expression a[3] and if a is a pointer, it starts at the location "a", gets the pointer value there, adds 3 to the pointer value, and gets the character pointed to by that value.
If a is an array, a[3] is three places past a. If a is a pointer, then a[3] is three places past the memory location pointed to by a. In the example above, both a[3] and a[3] return the same character, but the way they do it is different!

Doing something like this would be illegal.
char *p = "hello, world!";
p[0] = 'H';


I see some programs which use an integer to store the pointer. I am confused.
In obsolete programs, you may often see code fragments where a pointer is stored in the int type. However, sometimes it is done through lack of attention rather than on purpose. Let's consider an example with confusion caused by using the int type and a pointer to the int type:
int GlobalInt = 1;
void GetValue(int **x)
{
    *x = &GlobalInt;
}
void SetValue(int *x)
{
    GlobalInt = *x;
}
 ...
int XX;
GetValue((int **)&XX);
SetValue((int *)XX);
In this sample, the XX variable is used as a buffer to store the pointer. This code will work correctly on those 32-bit systems where the size of the pointer coincides with the int type's size. In a 64-bit system, this code is incorrect and the call 
GetValue((int **)&XX); will cause corruption of the 4 bytes of memory next to the XX 

This code was being written either by a novice or in a hurry. The explicit type conversions signal that the compiler was resisting the programmer till the last hinting to him that the pointer and the int type are different entities. But the programmer chose to ignore the warnings. Correction of this error is elementary and lies in choosing an appropriate type for the XX variable. The explicit type conversion becomes no longer necessary:
int *XX; GetValue(&XX); SetValue(XX);

Does C support call by value or call by reference ?
In the strictest sense, C always uses pass by value. You can simulate pass by reference yourself, by defining functions which accept pointers and then using the & operator when calling, and the compiler will essentially simulate it for you when you pass an array to a function (by passing a pointer instead). An important thing to note is that when you pass arrays to functions, they decay to the pointer to the first element of the array. Thus arrays are passed to functions as pointers to the first element of the array. These pointers are passed by value(and not by reference). An example of swap function where the addresses of two variables are passed and the values are swapped.
There are more printfs to track the values and addresses of the variables to understand whats happening before, during and after the function call.
main()
{
int a=5,b=10;
printf("Before swap\n");
printf("Address of a=%x b=%x\n",&a,&b);
printf("Value of a=%d b=%d\n",a,b);
swap(&a,&b);
printf("After swap\n");
printf("Address of a=%x b=%x\n",&a,&b);
printf("Value of a=%d b=%d\n",a,b);
}

void swap(int *pa, int *pb)
{
int temp;
printf("Inside swap before swap\n");
printf("Address of pa=%x pb=%x\n",&pa,&pb);
printf("Value at pa=%d pb=%d\n",*pa,*pb);
temp=*pa;
*pa=*pb;
*pb=temp;
printf("Inside swap after swap\n");
printf("Address of pa=%x pb=%x\n",&pa,&pb);
printf("Value at pa=%d pb=%d\n",*pa,*pb);
}
Output:
Before swap
Address of a=f05836c8 b=f05836cc
Value of a=5 b=10
Inside swap before swap
Address of pa=f0583698 pb=f0583690
Value at pa=5 pb=10
Inside swap after swap
Address of pa=f0583698 pb=f0583690
Value at pa=10 pb=5
After swap
Address of a=f05836c8 b=f05836cc
Value of a=10 b=5

In the above example, note the arguments &a and &b are pointers which are passed by values. This is evident from the output which shows different addresses for a and pa. The variable pa and pb are pointers which are local to the swap function and they have their own addresses which are different from the addresses of a and b.


