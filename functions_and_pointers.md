# Functions and Pointers
Pointers and Functions

What is a function pointer ?
A function pointer is a pointer variable which can hold the address of a function. When dereferenced, a function pointer invokes a function, passing it zero or more arguments just like a normal function.

Why do I need a pointer to a function ?
function pointers can be used to simplify code by providing a simple way to select a function to execute based on run-time values. Function pointers can be used as call back functions too. Function pointers allow the creation of generic code.
Give example of each here

How do I declare and initialize a pointer to a function ?
To declare a pointer to a function, you need to know the prototype of the function.

Suppose a function f takes an integer and a character and return a double, the prototype of the function would be

double f(int, char);

A pointer to function f having the above prototype can be defined as

doubt (*fptr)(int,char);

Note that the above statement declares a pointer variable which can point to any function having the same prototype as that of f. To make it point to f, it has to be initialised like this

fptr=f;

How do I declare a pointer to a function variable if I do not know the prototype of the function ?
Strictly speaking, you can't declare a pointer to a function if the prototype of the function is unknown. However, you can declare a void pointer, which can be typecasted to the suitable function pointer and called.

int add(int a, int b)
{
  return a+b;
}
main()
{
  void *ptr;
  int x;
  ptr=add; 

  x=(*((int (*)(int,int))ptr))(2,3);
  printf("X=%d\n",x);
}
In line 6 we just assign the address of add function to ptr which is a void pointer. Since it is a void pointer, it can not be dereferenced. First we type cast the void pointer to a pointer pointing to a function which takes two integers and returns an integer. We then dereference ptr and give arguments 2 and 3 which will call add function with the above parameters. 
How do I call a function using a pointer ? How do I supply arguments to the call ?
A function can be called via a pointer to the function by dereferencing.

int add(int a, int b)
{
return a+b;
}

main()
{
  int x;
  int (*fptr)(int,int); //declare a pointer to a function which accepts two integers and returns an int
  fptr=add;  //initialise the variable with the address of the function add

  x=(*fptr)(4,5);

  printf("Value of x is %d\n",x);
}
How do I declare an array of function pointers ?
The below line declares an array of 4 pointers to function whcih takes two ints and returns an  int.
int (*p[4]) (int x, int y)

Consider the following prototypes of the functions:
int add(int a, int b);
int sub(int a, int b);
int mul(int a, int b);
int div(int a, int b);

You can initialise the array of pointers in the same line as shown below:
int (*p[4]) (int x, int y)={add,sub,mul,div};

//calling sub with arguments 10,2

(*p[1])(10,2);
Can I define a generic pointer which can point to any function ?
Yes. You can do it by using void pointer. But unless you know the "signature" of the function you are pointing to, you cannot dereference the function pointer.

int add(int x,int y)
{
return x+y;
}
main()
{
        void *fp;
        int sum;
        fp=add;//this is fine..but how do you call ?
        sum=((int (*)(int,int))fp)(5,10); //by type casting the function pointer with the signature of add function.
        printf("Value of sum=%d\n",sum);
}

How do I call a function of specific prototype, using a void pointer ?
As shown in the previous question, we need to type caste the void pointer as a pointer to function returning the functions return type. Another example calls the builtin function strlen

#include <string.h>
main()
{
        void *p;
        int len;
        char buff[]="Pointers";
        p=strlen;
        len=((int (*)(char *))p)(buff);
        printf("Length=%d\n",len);
}
How do I declare a pointer to function returning a pointer to function ?
Such declarations get complicated. For example, the following is the prototype for the signal function
    void (*signal (int sig, void (*disp)(int)))(int);

The above declaration conveys that the signal function takes two arguments – first argument ‘sig’ is an integer. Second argument disp is a pointer to a function which takes an integer argument and returns void. The signal function returns a pointer to a function which takes an integer argument and returns void. The above declaration can be easily understood if it is split on two lines using typedefs
typedef void (*sighandler_t)(int);// a typedef for a pointer to a function which takes an int and returns void
sighandler_t signal(int signum, sighandler_t handler);
Now it is clear that signal returns a pointer to a function of type sighandler_t and takes two arguments – an integer and a pointer to a function of type sighandler_t.

How can I do C++ type of object oriented programming using function pointers ?
By embedding pointers to functions in a structure, one can imitate a very trivial C++ type of object orientation in C.
struct my_struct
{
    int data;
    struct my_struct* (*set_data) (struct my_struct *,int);
    int (*get_data) (struct my_struct *);
};

struct my_struct* my_struct_set_data(struct my_struct* m, int new_data)
{
    m->data = new_data;
    return m;
}
int my_struct_get_data(struct my_struct* m)
{
    return m->data;
}

struct my_struct* my_struct_create() {
    struct my_struct* result = malloc((sizeof(struct my_struct)));
    result->data = 0;
    result->set_data = my_struct_set_data;
    result->get_data = my_struct_get_data;
    return result;
}

int main(int argc, const char* argv[])
{
    int data=0;
    struct my_struct* thing = my_struct_create();
    thing->set_data(thing,1);
    data=thing->get_data(thing);
    printf("%d\n", data);
    free(thing);
    return 0;
}
How do I write a generic compare function to compare two integers to be used in qsort ?
This is the compare function to compare two integers which can be used in qsort. Note that the generic function should take two arguments of type const void *.
int compare(const void *p1,const void *p2)
{
        return (*(int *)p1 > *(int *)p2);
}

How is that (*fp)() and (**fp)() are equivalent ?  [ function ]
A pointer to a function can be dereferenced even without putting a *before the variable. Conversely, any number of * before a pointer to a function would dereference the function only once.

int sqr(int x)
{
        printf("Called with %d\n",x);
        return x*x;
}
main()
{
int (*fp)(int)=sqr;
fp(1);
(*fp)(2);
(**fp)(3);
(****fp)(4);
}


