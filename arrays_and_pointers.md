# Arrays and Pointers
Pointers and Arrays

Are pointers and arrays the same ?
Pointers and arrays are not the same. When you declare a pointer, you declare a variable which can hold the address of any variable including the starting address of the array. When you declare the array, the total size of the array will be allocated.

Pointer	Array
Holds the address of data	Holds data
Data is accessed indirectly, so you first retrieve the contents of the pointer, load that as an address (call it "L"), then retrieve its contents.
If the pointer has a subscript [i] you instead retrieve the contents of the location 'i' units past "L"	Data is accessed directly, so for a[i] you simply retrieve the contents of the location i units past a.
Commonly used for dynamic data structures	Commonly used for holding a fixed
number of elements of the same type of
data
Commonly used with malloc(), free()	Implicitly allocated and deallocated
Typically points to anonymous data	Is a named variable in its own right
Can be mutated to point to other data ie. ptr++ or ptr-- is valid	Is not mutable i.e a++ is invalid

Why am I unable to increment the base address of an array ?
Elements of the array are accessed by adding necessary offsets to the base address of the array. Since access of any element involves the usage of the base address to calculate its actual address, the base address is crucial that it is not lost. If the base address is overwritten with any other value, then the array can not be accessed. Hence to prevent overwriting the base address, it is kept as readonly. Any attempt to change the base address results in compiler giving a warning.

You said that I can't increment the base address of an array. Here is how I could increment the base address. How can it be possible ?

main()
{
  int arr[10]={1,2,3,4,5,6,7,8,9,10};
  increment_base_address(arr);
}
void increment_base address(int a[])
{
  printf("Base address of passed array is %x\n");
  a++;
  printf("Base address of passed array after incrementing is 
  %x\n");
}

Note that you have incremented a pointer instead of the base address of an array. This is the fundamental difference between a pointer and the base address of an array. When the array is passed as an argument to a function, it gets decayed to a pointer. And there is no rule which prevents you from incrementing the address in a pointer.

Are arrays and constant pointers the same ?
No. Though in general, both might look equal and interchangeable, the internals of arrays and constant pointers are different. For an array, the base address cannot be changed. However even for a constant pointer, the address held by it can be changed by assigning the value via a pointer to the pointer.

Can I assume that an array is a constant pointer ?
An array name can be treated as a constant pointer, in the sense that it cannot be changed. However, the access mechanisms while accessing objects for both types is completely different.

Once I allocate some memory to a pointer, can I assume that both the pointer and a similar array are equivalent ?
No, still a pointer and array are not the same. They are similar in the aspect of code. i.e the same code which can access an array can be used to access a pointer. However, the internal access mechanisms differ.

So when is a pointer and array the same ?
A pointer and array are never the same. However when an array is passed as a parameter to a function, only the pointer to the array is actually passed as an argument.
Why are these two function prototypes are same
funct(int a[]) and funct(int *a) ?
When an array is passed as a parameter to a function, only the pointer to array is passed as an argument. Hence both of the above prototypes are equivalent to the compiler. The only advantage of having two types of prototypes which are equivalent is to hint the developer if the input to the function is an array or if the function expects an integer pointer. Consider the following functions

void swap(int *a, int *b)
{
.....
}

int search(int arr[],int element)
{
...
}

The prototype of the first function gives an indication that the swap function takes two pointer variables(and not probably arrays) whereas the second function search takes an array as its first argument.
Note that even if the syntax of both the function is changed like this
void swap(int a[], int b[]);
int search(int *arr,int element);
Both the functions continue to work in the way they were intended to. However, the reader has some difficulty in understanding the actual behaviour of the function.
What is the pointer decay rule ?
An array of type T decays into a pointer to its first element. The resulting pointer is a pointer to T.
The pointer decay rule has 3 exceptions.

1. When the array is the operand of a sizeof operator.
2. When the array is the operand of & operator.
3. When the array is a string literal initialiser for a character array.

Note that the array decay rule cannot be applied recursively. For example, a an array of type T decays into a pointer to type T. However an array of array of type T does not decay into a pointer to a pointer to type T. An array of array of type T decays into a pointer to an array of type T.

How do I pass a 2-dimensional array to a function ?
There are 5 alternative ways to pass a 2-dimensional array to a function.
Consider this main function which passes the data in the 2-dimensional array mat to the following functions – func1, func2, func3, func4, func5.
main()
{
	short mat[3][3],i,j;

	for(i = 0 ; i < 3 ; i++)
		for(j = 0 ; j < 3 ; j++)
		{
			mat[i][j] = i*10 + j;
		}

	printf(" Initialized data to: ");
	for(i = 0 ; i < 3 ; i++)
	{
		printf("\n");
		for(j = 0 ; j < 3 ; j++)
		{
			printf("%5.2d", mat[i][j]);
		}
	}
	printf("\n");

	func1(mat);
	func2(mat);
	func3(mat);
	func4(mat);
	func5(mat);
}

 /*
Just an array without specifying the first dimension. 
There is no need to specify the first dimension.
 */

int func1(short mat[][3])   
{
        register short i, j;

        printf(" Declare as matrix, explicitly specify second dimension: ");
        for(i = 0 ; i < 3 ; i++)
                {
                printf("\n");
                for(j = 0 ; j < 3 ; j++)
                {
	                printf("%5.2d", mat[i][j]);
                }
        }
        printf("\n");

        return;
}

 /*
Passing the pointer to array, the second dimension has to be explicitly specified
 */

int func2(short (*mat)[3])
{
register short i, j;

      printf(" Declare as pointer to column, explicitly specify 2nd dim: ");
      for(i = 0 ; i < 3 ; i++)
      {
printf("\n");
            for(j = 0 ; j < 3 ; j++)
            {
	      	printf("%5.2d", mat[i][j]);
            }
      }
printf("\n");
return;
}

 /*
Using a single pointer, the array is "flattened"
 With this method you can create general-purpose routines.
 The dimensions doesn't appear in any declaration, so you
 can add them to the formal argument list.
 The manual array indexing will probably slow down execution.
 */

int func3(short *mat)	
{
register short i, j;
printf(" Declare as single-pointer, manual offset computation: ");
for(i = 0 ; i < 3 ; i++)
{
printf("\n");
for(j = 0 ; j < 3 ; j++)
{
printf("%5.2d", *(mat + 3*i + j));
}
}
printf("\n");
return;
}

 /*
Using a pointer to pointer, using an auxiliary array of pointers
With this method you can create general-purpose routines,
 if you allocate "index" at run-time.

 Add the dimensions to the formal argument list.
 */

int func4(short **mat)
{
short i, j, *index[3];

for (i = 0 ; i < 3 ; i++)
index[i] = (short *)mat + 3*i;

printf(" Declare as double-pointer, use auxiliary pointer array: ");
      for(i = 0 ; i < 3 ; i++)
      {
printf("\n");
for(j = 0 ; j < 3 ; j++)
            {
printf("%5.2d", index[i][j]);
}
}
printf("\n");
return;
}

 /*
 Method #5 (single pointer, using an auxiliary array of pointers)
 */

int func5(short *mat[3])
{
short i, j, *index[3];
for (i = 0 ; i < 3 ; i++)
index[i] = (short *)mat + 3*i;

printf(" Declare as single-pointer, use auxiliary pointer array: ");
for(i = 0 ; i < 3 ; i++)
{
printf("\n");
for(j = 0 ; j < 3 ; j++)
{
printf("%5.2d", index[i][j]);
}
}
printf("\n");
return;
}

What does *p++ do ?
int myarray[4]= {1,2,3,0};
int *p = myarray;
int out = 0;
while (out = *p++)
{
printf("%d ", out);
}

The above example prints out 1 2 3. Code like *p++ is a common sight in C so it is important to know what it does. The int pointer p starts out pointing to the first address of myarray, &myarray[0]. On each pass through the loop the p pointer address is incremented, moves up one index in the array, but the previous p unincremented address (index) is dereferenced and assigned to the out variable. This happens until it hits the fourth element in the array, 0 at which point the while loop stops. 

In terms of operator precedence the postfix operator (++) binds tighter than the dereference operator (*). If we were reading this wrong we might think that we are incrementing the value pointed to by our p int pointer. But what is actually happening is four separate operations and a clash between operator precedence and order of operations.

A fully expanded version of *p++ in code might look like this. The p pointer is assigned to x, then p is incremented. But x is still pointing at the original unincremented p address.
int myarray[4]= {1,2,3,0};
int *p = myarray;
int *x = p;
p = p + 1;
printf("*p = %d, *x = %d\n", *p, *x);

Why is sizeof array give different result when I split the code into two functions and pass the array as an argument ?
As per the pointer decay rule, when an array is passed to the function, only a pointer to the first element of the array is passed. Thus the function would print the size of pointer instead of printing the size of the array.

Consider this program

main()
{
int arr[10];
printf("Size of array in main is %d\n",sizeof(arr));
my_size(arr);
}

int my_size(int a[])
{
printf("Size of array in my_size is %d\n",sizeof(a));
return 0;
}

The above code prints

Size of array in main is 40
Size of array in my_size is 4

I had the definition int a[6] in one source file, and in another I declared extern int *a. Why didn't it work?
Here say file 1 declares it as an array and file 2 declares it as a pointer. The problem here is that a pointer and an array are not the same. Note that there are situations when an array "becomes" a pointer( as in pointer decay rule) but they will never be the same. Consider this analogy. Suppose there were two files file1 and file2. 

File 1:
int x;
File 2:
extern float x;
This is a mismatch of variables. Thus it does not work. Similarly int a[6] and extern int *a are different datatypes and would lead to mismatch.
How can I create a dynamically sized array ?
Gcc supports an extension called variable sized array. In functions, the array size can be specified as a variable parameter as shown below.

int copy_and_print_array(int a[], int size)
{
        int x[size];
        int i;
        for(i=0;i<size;i++)
        {
                x[i]=a[i];
        }
        //Now print it via the new array
        for(i=0;i<size;i++)
        {
                printf("x[%d]=%d\n",i,x[i]);
        }
}
main()
{
        int arr[4]={1,2,3,4};
        copy_and_print_array(arr,4);

}
As already explained, this extension is specific to gcc and if you want to write portable code, do NOT use this feature. In such case, you have to rely on malloc to create dynamically sized memory. One important thing is to free the memory before the function returns as shown below.
int copy_and_print_array(int a[], int size)
{
        int *x=malloc(size*sizeof(int));
        int i;
        for(i=0;i<size;i++)
        {
                x[i]=a[i];
        }
        //Now print it via the new array
        for(i=0;i<size;i++)
        {
                printf("x[%d]=%d\n",i,x[i]);
        }
        free(x);
}

Why can't I pass a char ** to a function which expects a const char **? 
You can use a pointer-to-T (for any type T) where a pointer-to-const-T is expected. However, the rule (an explicit exception) which permits slight mismatches in qualified pointer types is not applied recursively, but only at the top level. (const char ** is pointer-to-pointer-to-const-char, and the exception therefore does not apply.)
The reason that you cannot assign a char ** value to a const char ** pointer is somewhat obscure. Given that the const qualifier exists at all, the compiler would like to help you keep your promises not to modify const values. That's why you can assign a char * to aconst char *, but not the other way around: it's clearly safe to ``add'' const-ness to a simple pointer, but it would be dangerous to take it away. However, suppose you performed the following more complicated series of assignments:
const char c = 'x';		/* 1 */
char *p1;			/* 2 */
const char **p2 = &p1;	/* 3 */
*p2 = &c;			/* 4 */
*p1 = 'X';			/* 5 */
In line 3, we assign a char ** to a const char **. (The compiler should complain.) In line 4, we assign a const char * to a const char *; this is clearly legal. In line 5, we modify what a char * points to--this is supposed to be legal. However, p1 ends up pointing toc, which is const. This came about in line 4, because *p2 was really p1. This was set up in line 3, which is an assignment of a form that is disallowed, and this is exactly why line 3 is disallowed.
Assigning a char ** to a const char ** (as in line 3, and in the original question) is not immediately dangerous. But it sets up a situation in which p2's promise--that the ultimately-pointed-to value won't be modified--cannot be kept.
In C, if you must assign or pass pointers which have qualifier mismatches at other than the first level of indirection, you must use explicit casts (e.g. (const char **) in this case), although as always, the need for such a cast may indicate a deeper problem which the cast doesn't really fix.

Why a double pointer can't be used as a 2D array?
This is a good example, although the compiler may not complain,  it is wrong to declare:  "int **mat" and then use "mat" as a 2D array.  These are two very different data-types and using them you access different locations in memory. This  mistake aborts the program with a "memory access violation" error.

This mistake is common because it is easy to forget that the pointer decay rule must not be applied recursively (more than once) to the  same array, so a 2D array is NOT equivalent to a double pointer.
 A "pointer to pointer of T" can't serve as a "2D array of T".   The 2D array is "equivalent" to a "pointer to row of T", and this  is very different from "pointer to pointer of T".

 When a double pointer that points to the first element of an array,  is used with subscript notation "ptr[0][0]", it is fully dereferenced  two times. After two full dereferencings the resulting  object will have an address equal to whatever value was found INSIDE  the first element of the array. Since the first element contains  our data, we would have wild memory accesses.

 We could take care of the extra dereferencing by having an intermediary  "pointer to T":

    type   mat[m][n], *ptr1, **ptr2;
    ptr2 = &ptr1;
    ptr1 = (type *)mat;

 but that wouldn't work either, the information on the array "width" (n),  is lost, and we would get right only the first row, then we will have  again wild memory accesses.

 A possible way to make a double pointer work with a 2D array notation  is having an auxiliary array of pointers, each of them points to a  row of the original matrix.

    type   mat[m][n], *aux[m], **ptr2;

    ptr2 = (type **)aux;
    for (i = 0 ; i < m ; i++)
        aux[i] = (type *)mat + i * n;

 Of course the auxiliary array could be dynamic.

 An example program:

\#include <stdio.h>
\#include <stdlib.h>

main()
{
long	mat[5][5], **ptr;

	mat[0][0] = 3;
	ptr = (long **)mat;

	printf("  mat          %p \n",  mat);
	printf("  ptr          %p \n",  ptr);
	printf("  mat[0][0]    %d \n",  mat[0][0]);
	printf(" &mat[0][0]    %p \n", &mat[0][0]);
	printf(" &ptr[0][0]    %p \n", &ptr[0][0]);
return;	
}

mat          0x7fffc6baa6c0
ptr          0x7fffc6baa6c0
mat[0][0]    3
&mat[0][0]    0x7fffc6baa6c0
&ptr[0][0]    0x3
 We can see that "mat[0][0]" and "ptr[0][0]" are different objects  (they have different addresses), although  "mat"  and  "ptr" have  the same value.


 
Why am I not able to copy an array using assignment operator but able to copy a structure using assignment operator ? 
You can not copy an array using assignment because they are derived types and hence can not be copied. In C parlance, an array is a second class datatype(or a derived datatype) where as structures are given first class status(or primary datatypes like int, char etc). Variables of first class can be assigned using the assignment operator.

However there is a simple pointer trick with which you can copy by clever typecasting and using structures.
main()
{
int arr[]={1,2,3,4,5};
int arr2[]={0,0,0,0,0};
int i;
struct n
{
char x[sizeof(arr)];
};
printf("Sizeofstruct is %d\n",sizeof(struct n));
*((struct n*)arr2)=*((struct n*)arr);
for(i=0;i<5;i++)
{
printf("a[%d]=%d\n",i,arr2[i]);
}
}
What is the difference between arr and &arr for an integer array ?
For an integer array, arr and &arr gives the same address but both of them are pointers to two different data types. arr refers to the address of the first element of the array and equivalent to &arr[0] and is a pointer to an integer. &arr is refers to the the address of an array of N integers where N is the number of elements in the array and is a pointer to an array of N integers.  
The following example shows the difference between arr and &arr with respect to an integer array.
main()
{
int arr[4]={1,2,3,4};
int *pi;
int (*pa)[4];
pi=arr;
pa=&arr;
printf("arr=%x\n",arr);
printf("&arr=%x\n",&arr);

printf("pi=%x pa=%x\n",pi,pa);
pi++;
pa++;
printf("pi=%x pa=%x\n",pi,pa);
}
The following is the output of the above program
arr=2696f130
&arr=2696f130
pi=2696f130 pa=2696f130
pi=2696f134 pa=2696f140
Observe that incrementing pi has incremented the address by 4 bytes as it is a pointer to array, whereas incrementing pa has incremented the address by 16 bytes as it is a pointer to an array of 4 ints and 4 ints occupy 16 bytes. In a two dimensional array which is declared as
int array[ROWS][COLUMNS];
a reference to array has type “pointer to array of COLUMNS ints whereas &array has type “pointer to array of ROWS arrays of COLUMNS ints”.
For example, in the following declaration
int dbl_array[4][3];
dbl_array has type pointer to array of 3 ints. &dbl_array has type pointer to an array of 4 arrays of 3 ints.

How and when do I declare a pointer to an array ?
Generally you don’t want to declare a pointer to an array. When people speak casually of pointer to an array, they usually mean a pointer to its first element.
Instead of a pointer to an array, you can consider using a pointer to one of the array’s elements. Arrays of type T decay into pointers to type T which is convenient; You can access the individual members of the array by incrementing such a pointer.
However, in case of pointer to an array, when the pointer is incremented, they step over the entire arrays and they are useful only when operating on arrays of arrays.
You declare a pointer to an array when you want to pass a 2D array to a function. According to the pointer decay convention, a 2D array when passed to a function decays to pointer to array. The below example clarifies the usage.
Given the declarations

int a1[3] = {0, 1, 2};
int a2[2][3] = {{3, 4, 5}, {6, 7, 8}};
int *ip;		/* pointer to int */
int (*ap)[3];		/* pointer to array [3] of int */

you could use the simple pointer-to-int, ip, to access the one-dimensional array a1:

	ip = a1;
	printf("%d ", *ip);
	ip++;
	printf("%d\n", *ip);

This fragment would print

	0 1

An attempt to use a pointer-to-array, ap, on a1:

	ap = &a1;
	printf("%d\n", **ap);
	ap++;				/* WRONG */
	printf("%d\n", **ap);		/* undefined */

would print 0 on the first line and something undefined on the second (and might crash). The pointer-to-array would only be at all useful in accessing an array of arrays, such as a2:

	ap = a2;
	printf("%d %d\n", (*ap)[0], (*ap)[1]);
	ap++;		/* steps over entire (sub)array */
	printf("%d %d\n", (*ap)[0], (*ap)[1]);

This last fragment would print

	3 4
	6 7
What is flattening of arrays ? [arrays ]
Arrays are implemented as a contiguous memory block. This information can be used to manipulate the values stored in the array and allows rapid access to a particular array location.
int arr[10][10];
int *ptr = (int *)arr ; 
ptr[11] = 10;
// this is equivalent to arr[1][0] = 10; assign a 2D array
// and manipulate now as a single dimensional array.
The technique of exploiting the contiguous nature of arrays is known as ‘flattening of arrays’.
To calculate the linear offset of arr[i][j] to use arr as a flattened array, use the formula
flattened_offset=i*ROWSIZE+j;
Now arr[flattened_offset] is equal to arr[i][j];

What are ragged arrays ?
main()
{
char **list;
int i;
list=malloc(3*sizeof(char *));
list[0] = "United States of America";
list[1] = "India";
list[2] = "United Kingdom";
for(i=0; i< 3 ;i++)
printf(" %d ",strlen(list[i]));
}
prints 24 5 14 which are the string lengths of list[0], list[1], list[2] respectively.
This type of implementation is known as ragged array, and is useful in places where the strings of variable size are used. Popular method is to have dynamic memory allocation to be done on the every dimension. The command line arguments for a C program are passed only as ragged arrays.

How are array and pointer parameters are changed by the compiler ?
The pointer decay rule isn't recursive. An array of arrays is rewritten as a "pointer to arrays" not as a "pointer to pointer".
Argument is	Matches Formal Param
array of pointer char *c[15];	char **c; pointer to pointer
pointer to array char (*c)[64];	char (*c)[64]; doesn't change
pointer to pointer char **c;	char **c; doesn't change

The reason you see char ** argv is that argv is an array of pointers (i.e., char *argv[]). This decays into a pointer to the element, namely a pointer to a pointer. If the argv parameter were actually declared as an array of arrays (say, char argv[10][15]) it would decay to char (* argv)[15] (i.e., a pointer to an array of
characters) not char ** argv.
Is arr[-1] illegal ?
The answer is both “yes” and “no” depending on the context and the type of arr. Pointer arithmetic is defined or valid only as long as the pointer points within the same allocated block of memory or to the imaginary “terminating” element one past it. For all other cases, the behavior is undefined, even if the pointer is not dereferenced.  For the given declarations
int array[5];
int *ptr=&array[-1];      //Might work but illegal according to standards
printf(“array[0]=%d”,*(++ptr));

How are arrays passed to functions ?
In C, arrays are passed to functions via a pointer to the first element of the array. The pointer to the first element in the array(which is generally 4 bytes on 32 bit systems) is copied on to the stack irrespective of the length of the array. Since the called function does not know the size of the array( as it receives a pointer to the first element), it is common to see the length of the array passed to the function as a second parameter. The additional length argument is optional when passing C strings because they are terminated by \0.

Why I am unable to create anonymous integer arrays like this ?
int *arr={1,2,3,4};  //this is invalid
char  *s=”Hello”;  //this is valid
Anonymous integer arrays can not be created because, once defined like that, it is not possible to get the sizeof the array(or the number of elements in the array). Hence it is not possible. However, anonymous strings can be created because, strings in C are terminated by ‘\0 ‘ character and it is trivial to find the length of such a C string.

