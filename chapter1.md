# Introduction to Pointers

####What is a pointer ?
A pointer is a data type which can store the address of any variable. A pointer is a variable suitable for keeping memory addresses  of other variables, the values you assign to a pointer are memory addresses of other variables (or other pointers).


####What is the "address of" operator ?
An "&" represents the address of operator. Any variable when preceded with an "&" returns the address of the variable.

####What is the "value at address" operator ?
A "*" represents the value at address operator. A pointer variable when preceded with a "*" returns the value stored at the address.

#### Why is sizeof(char *) printing it as 4 ? I expect it to be the length of the actual string ?
sizeof is the compile time operator which gives the size occupied by that variable. Since a pointer holds an address in memory, the data type of it is an unsigned integer, whose size is 4bytes. In general, all pointers are of the same size, irrespective of their types. On a 32bit system

```C
sizeof(char *)=4 bytes
sizeof(float *)=4 bytes
sizeof(double *)=4 bytes
sizeof(int **)=4 bytes
```

####What does de-referencing mean ?
A pointer stores an address in it, and obtaining the value at that address is known as dereferencing the pointer. This is usually done by prepending a “*” before the variable name. The below example prints the value stored in the variable x using pointer p which holds the address of x.
```C
int x=10;
int *p=&x;
printf(“Address =%x Value at address is %d\n”, p, *p);
```

####What is a void pointer ?
A void pointer is a special pointer type which can store the address of any variable type.

```C
void *vp;
int x;
float *y;
vp=&x; // vp stores the address of an integer
vp=&y; //vp stores the address of a pointer variable. 
```

####When you have a void pointer, which is generic enough to store the address of any variable type, why do I need different pointer types ?
Strictly speaking, we can get away by using a generic pointer and typecasting wherever we need to which makes the program look complex. Consider the below code where you traverse an array of integers using a pointer variable. The second example shows the same using the generic void pointer.

```C
main()
{
  int arr[5]={1,2,3,4,5};
  int *p;
  void *ptr;
  int i;

  p=arr;
  ptr=arr;
  for(i=0;i<5;i++)
  {
    printf("arr[%d] is %d\n",i,*(p+i));//using integer pointer
    printf("arr[%d] is %d\n",i,*((int *)ptr+i));//using void pointer
  }
}
```
What is the size of (*ptr) where ptr is a void pointer ?
You can not dereference a void pointer, because the compiler does not know the size/type of the variable you are pointing to.
The below program does not compile.
main()
{
  int x=4;
  void *p=&x;
  printf("x=%d",*p);
}

To make this work change the last statement to
        printf("x=%d",*(int *)p);
which faithfully prints x=4.

Why am I unable to do ptr++ for a void pointer ?
Performing arithmetic on a pointer requires its size of the pointed object to be known to the compiler. Since the pointer is pointing to a type whose size is unknown, the compiler cannot perform the required arithmetic. Note that some compilers may allow arithmetic on void pointers, by treating them as character pointers. However portable code should not to rely on such compiler specific features.

What is a NULL pointer ?
A NULL pointer is a pointer which is not pointing to any valid memory. A null pointer can be compared to null values in relational databases.
A NULL pointer does not point to any valid memory. Hence any attempts to dereference a null pointer will cause a runtime error. If this error is unhandled, the program terminates immediately causing a Segmentation fault. Two null pointers are guaranteed to be equal. Any NULL pointer will be equal to zero when compared with an integer type. The macro NULL macro is defined as (void *)0, and thus a null pointer will compare equal to the macro NULL.

 
What is the difference between a NULL pointer and a void pointer ?
A NULL pointer is a pointer variable which is not pointing to any valid memory address. A void pointer is a variable which can hold the address of any type of variable.
int *p ;
p=NULL;
In the above code, p is a pointer which can hold the address of an integer variable, but currently not holding any valid address of an integer.
void *vp;
//vp is a void pointer which can hold the address of any variable.
int x=20;
vp=&x;//Now vp is pointing to the address of x;
char a=’I’;
vp=&a;//Now vp is pointing to the address of a;
Note that you cannot print the value at the address held in vp as below

Why does the same code when split over multiple statements not work ?
int *p=malloc(128);
int *p;
*p=malloc(128);

The first line declares a pointer variable pointing to an integer address and points to a memory which is allocated via malloc. Note that the variable p is assigned the address of the memory chunk returned by malloc.
The second statement declares an integer pointer without allocating any memory. In the last statement the pointer is dereferenced and assigned the value of the return value of malloc. Return value of malloc is a pointer, which when assigned to *p might cause memory overwrite and might cause the program to misbehave and dump core. The correct way of splitting the first line is
int *p;
p=malloc(128);
Why am I not supposed to use non re-entrant functions in signal handlers?
The best practice is to write a signal handler that does nothing but set an external variable that the program checks regularly, and leave all serious work to the program. This is best because the handler can be called asynchronously, at unpredictable times—perhaps in the middle of a primitive function, or even between the beginning and the end of a C operator that requires multiple instructions. The data structures being manipulated might therefore be in an inconsistent state when the handler function is invoked. Even copying one int variable into another can take two instructions on most machines.

How do I write a function which is similar to the sizeof() operator for dynamically memory allocated pointer ?
Strictly speaking you can't write a portable code of sizeof() for dynamically allocated memory because the implementation of malloc is different by different compilers and any code which works with a variant of malloc might not on other variants of malloc. Hence there is no portable way of writing a generic function.

 
What is a constant pointer ?
A constant pointer is a variable which points to a specific address and the address stored in the variable cannot be changed. However, the value at the address can be changed. Note that the address present the constant pointer can still be changed via pointer gimmicks.
int a=10,b=20;
int *const cp=&a;
In the above code, cp is a constant pointer, which means once cp is assigned the address of array a, it cannot be changed by 
cp=&b;

However, the cp can be changed indirectly, by dereferencing a pointer pointed to cp as shown below.

int **pcp=&cp;
*pcp=&b;

What is pointer to a constant ?
A pointer to a constant is a variable which points to an address whose contents can't be changed. However, the pointer can be made to point to a different address. Note that the value at the address present in the pointer to a constant can still be changed via pointer gimmicks.


Does type casting a pointer change the data the pointer points to ?
 No. This is a common misconception even among experienced C programmers. Type casting does not change the value at the address of the pointer. It is only a direction to the compiler to treat the memory as if it is another type of variable it is type casted to

char ch='A';
printf("ASCII value of ch is %d",(int)ch);
printf("Character present in ch is %c\n",ch);

Note that the value of ch still remained 'A' even though it is typecasted.

How do I produce a segmentation fault ?
There are many ways to produce segmentation fault. It is easy to produce segmentation fault in C as C does not have inbuilt type checks or array bounds check.

By dereferencing null
#include <stdio.h>
main()
{
printf("Value at NULL is %d\n",*(int *)NULL);
}

By dereferencing some other memory
main()
{
  printf("Dereferencing some address %d\n",*(int *)4);
}

By memory overwrite at location which does not belong to you
main()
{
  int *ptr=(int *)4;
  *ptr=100;
}
By freeing unallocated memory
main()
{
        int *p=(int *)4;
        free(p);
}

By freeing memory not allocated from malloc
main()
{
        int x[10];
        free(x);
}

How do I know the number of elements in a static array ?
The right expression to know the number of elements in a static array is 
sizeof(arr)/sizeof(arr[0]);
where arr is an array of Type T and arr[0] would be of Type T.
For example
int  arr[20];
int count;
count= sizeof(arr)/sizeof(arr[0]);
printf(“%d”,count);

The above code will print 20.

Note that the same code does not work in a function, if the array is passed as an argument to a function because of pointer decay rule.
You can define a macro which can operate on an array of any data type to return the size of the array
#define SIZE(x) (sizeof(x)/sizeof(*x))
main()
{
  int a[3];
  char b[20];
  struct
  {
    int x;
    char y;
  }s[100];

  printf("Elements in a=%d b=%d 
    s=%d\n",SIZE(arr),SIZE(b),SIZE(s));
}

What does incrementing and decrementing a pointer mean ?
When a pointer is incremented, it points to the next location of the element of that data type. 
Incrementing a pointer to char would mathematically increase the value of the address stored by 1 byte. Incrementing a pointer to an int would mathematically increase the value of the address stored by 4 bytes.

In general, the expression ptr++; is mathematically equivalent to OBJTYPE *ptr;
....
ptr=ptr+sizeof(OBJTYPE);

Similarly ptr-- is mathematically equivalent to 
ptr=ptr-sizeof(OBJTYPE);
Here OBJTYPE can be any data type including structures and unions.

void * is generic pointer type for any pointer type(like char *,int * etc.) . What is the generic pointer types for pointer-pointer, pointer-pointer-pointer (like char **…) etc?  Is it void **… or is such generic pointer type necessary at all?
The type void * can hold addresses of all pointers. Hence it is not necessary to have void ** data types in your programs.


How do I print byte by byte value of an integer using a pointer to it ?
The idea is to traverse each byte by treating an integer as a 4 byte character array.  
main()
{
  int i=0x12345678;
  int *p=&i;
  int n=0;
  for(n=0;n<sizeof(int);n++)
  {
    printf("Byte [%d]=%x\n",n,*((char *)(p)+n));
  }
}
In the above example, the integer pointer p is type casted as a character pointer and then offset n is added to the address, so the address expression points to the nth byte of the integer. The address is referenced as a character to extract the 1 byte value present at the address. The above code would print
Byte [0]=78
Byte [1]=56
Byte [2]=34
Byte [3]=12

How do I increment a char pointer, which points to an array of ints ? x++ does not work. 
x++ would increment the pointer by 1 byte, because x is a pointer to char and size of char is 1. To make x point to the next integer you need to increment x by 4 bytes. This can be achieved in two ways.
First way: Calling x++, sizeof(int) times.
Second way: Typecasting x to integer pointer and then incrementing it only once.
        ((int *)p)++;


First way:

main()
{
  int a[4]={1,2,3,4};
  char *p=(char *)a;
  printf("p points to first element %d\n",*((int *)p));
  for(i=0;i<sizeof(int);i++)
    p++;
  printf("Now p points to second element%d\n",*((int *)p));
}
Second way:
main()
{
  int a[4]={1,2,3,4};
  char *p=(char *)a;
  printf("p points to first element %d\n",*((int *)p));
  ((int *)p)++;
  printf("Now p points to second element%d\n",*((int *)p));
}
How do I write a function which increments an integer pointer ?
You should write a function which takes a pointer to a pointer to an int and perform the increment.
void inc_ptr(int **p)
{
(*p)++;
}
Infact, it is easier to write a macro which does the increment of a generic pointer.
#define INCR(x) (x++)
What is Segmentation Fault ?
A segmentation fault (often shortened to segfault), bus error or access violation is generally an attempt to access memory that the CPU cannot physically address. It occurs when the hardware notifies an operating system about a memory access violation. The OS kernel then sends a signal to the process which caused the exception. By default, the process receiving the signal dumps core and terminates. The default signal handler can also be overridden to customize how the signal is handled.

