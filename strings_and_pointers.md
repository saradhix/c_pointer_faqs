# Strings and Pointers

What is the difference between a pointer to a string and a pointer to an array of characters ?
In C, there is no data type called "string". If an array of characters terminates with a character whose value is '\0'(ascii value 0), then that data is called as C string. Since strings are terminating with '\0', there is no necessity to pass the size of the array when passing the array to a function. Common string functions exploit this feature and eliminate the necessity of passing the size of the string as an extra parameter to all the string processing functions.

What is the difference between a NULL and NUL and ‘0’ ?
NUL is he ascii character with all its bits as zero. It is the NUL character, which is used to terminate C strings. The numerical value of NUL is zero.
NULL is the macro used to represent a "null pointer" i.e a pointer which points to nowhere. NULL is generally implemented as a macro #define NULL (void *)0. Printing the value of NULL as an integer would give 0. 

printf(“%d”,NULL); //prints 0

Dereferencing NULL is illegal and will cause a fatal error.

int *x=NULL;
*x=10;
This is fatal error. Program will crash usually with a segmentation violation.

The character ‘0’ is the character for digit 0 and has ascii value 48.
How do I traverse a string using pointers ?

Traversing a string is easy as we have the sentinel character which has the value of ‘\0’

char s[20]=”Love all, Hate None”;

for(i=0;s[i]!=’\0’;i++)
printf(“%c”,s[i]);

The above code will print each character from s one at a time for each call to printf.

How and when do I to use an array of pointers to strings ? Give some examples.
An array of pointers to strings is used when we need to iterate over variable length strings.
main()
{
        const char *f[]={"AA","Ab","Ac"};
        int i;
        for(i=0;i<3;i++)
                printf("%s\n",f[i]);
}

char *x="Hello"; x[2]='P'; Why does this not work ?
Placing "Hello" in your code causes the compiler to include that string in the compiled executable. The compiler stores the first in a section of memory called RODATA (read-only data). Any attempt to modify data in the RODATA section will lead to a segmentation fault.

What is a string literal ? How can I modify it ?
A string literal is the representation of a string value in a program. String literals are constant in C. Hence it is illegal to modify them.
char *x=”Hello”; Here x is initialised to the string literal “Hello”

*x=’P’;//This is illegal as x is a pointer to constant char

strcpy(x,”Bye Bye World”);//This too is illegal as x is a pointer to constant char.
While other constant variables can be modified using pointer tricks(dereferencing a pointer to the constant and assigning value to it), the value pointed by x cannot be changed by pointer tricks because the data pointed by x is stored in section called RODATA(Read Only Data).
How do I sort an array of strings with qsort, using strcmp as the comparison function ?
The below example sorts the array of strings
char *strings[] = { "dog","cat","bat", "rat"};
size_t count = sizeof(strings)/sizeof(*strings);
qsort( strings, count, sizeof(char *), strcmp );
What is the difference between strcpy() and memcpy() ?
The strcpy() function is designed to work exclusively with strings. It copies each byte of the source string to the destination string and stops when the terminating NUL character '\0' has been copied. On the other hand, the memcpy() function is designed to work with any type of data which can contain a '\0's anywhere in the data. Unlike in strings,a '\0' character can occur anywhere, the size of the data can not be determined by relying on the occurence of '\0'. Hence explicit size has to be passed to memcpy function.
It is advisable to use strcpy for copying strings and memcpy for copying data like structures.
Why '\0' is used as end of the string?
As '\0' is the only character that is neither printable nor a control character, it is used as end of string marker. The character is also known as NUL character or referred to null(in small letters) and is frequently confused with NULL. The ASCII value of NUL character is 0. Note that NUL character ‘\0’ is different from the ASCII character ‘0’. The ASCII value of character ‘0’ is 48.

What are constant strings ?
Constant strings may get placed either in code or data area and that depends on the implementation. That means, the string constants are available to use even after functions are exited. Let us see an example:
      char *try1()
      {
      char *temp = “string constant";
      	return temp;
      }
      char *try2()
      {
      char temp[] = “character string";
      	return temp;
      }
      char *try3()
      {
      char temp[] = {‘c’,’h’,’a’,’r’,’s’,’\0’};
      	return temp;
      }
      int main()
      {
      puts(try1());    // prints “string constant”
      	puts(try2());    // undefined behavior!
      puts(try3());    // undefined behavior!
}
What are shared strings ?
 Constants are also allocated space during compilation of the program. In this example, “string” is stored somewhere in the memory and its address is stored in ptr. This happens because both of the strings are constants and are stored in the RODATA section of memory which is readonly. This concept is known as shared strings. Compiler may store both the strings in a single location and assign the same string to both arr and ptr.  It is to be noted that the shared strings does not have to occur in full strings, part of the strings can also overlap.

main()
{
        char *ptr1 = "string";
        char *ptr2 = "string";
        printf("Address in ptr1 is %x, address in ptr2 is %x\n",ptr1,ptr2);
}
How are strings passed to functions ?
C Strings are passed to functions just like arrays. When we say we pass the array to a function, we usually mean passing the address of the array to the function. Since C strings are terminated by \0 character, it is not mandatory to specify the size of the C string.
I have a function which returns a string, but after the function returns to the caller, the returned string is garbage. Why ?
Most possibly, the function was returning a local address like this
char *get_string()
{
char s[]=”Hello”;
return s;
}
In the above code, s is a local variable and the scope and life of s is as long as the control remains in the function. Once the control returns to the caller, the address of s will not be valid. Strictly speaking, the address points to the same memory location, the data at the memory location pointed by s might be overwritten.
The problem can be solved in two ways – one by declaring s as static
static char s[]=”Hello”;
In this case, s will be allocated in the BSS section of the program instead of in the stack. The life of the variable will be the life of the program and s can be accessed by the caller function. The second method is to allocate memory dynamically using malloc.
Why doesn’t this code work ?
char *s;
strcpy(s,”Hello World”);

This code might cause a segmentation violation because we are trying to copy data to an uninitialized pointer which might point to other areas of the program, including but not limited to read only areas and other data structures. Such an assignment might cause segmentation violation or corruption of critical data structures used by the program. As a good programming practice, it is advisable to do two things to prevent such accidents – Initialise all pointer variables to NULL right at the place of declaring the variables. Secondly, initialise pointer variables to valid address, either by pointing them to addresses in static memory or dynamic memory. The below example illustrates the points.
char *p1=NULL, *p2=NULL;
char s[]="Hello World";

p1=s; //p1 points to the statically allocated memory of the variables.
p2=malloc(20); //p2 points to the dynamically allocated memory returned from malloc, assuming malloc to succeed

How do I truncate a string ?
In C, strings are terminated by \0 character. The effective length of the string is measured from beginning till the first occurrence of the character \0. The below code shows how to truncate a string.

main()
{
char str[]="This is a long string which will get truncated soon...";
printf("Str=%s len=%d\n",str,strlen(str));
str[14]=0;//Truncate at 14th character counting from 0
printf("Str=%s len=%d\n",str,strlen(str));
}
Output:
Str=This is a long string which will get truncated soon... len=54
Str=This is a long len=14

Note that the data beyond the character \0 continues to exists in the above example(we just changed/overwritten only the 14th character, but is ignored by all string handling functions(like strlen, strcpy, strcmp) as evident from the above example.
How do I separate this line into two statements – declaration and initialisation ?
char a[]=”Hello”;
Doing that is little cumbersome and awkward in C. First we need to allocate character array sufficiently big to hold the content “Hello”. Since we do not know the length of the text which is stored in the variable, it is better to err on the safe side and assume a reasonable length. Then you need to copy the data using the strcpy function or sprintf function.The equivalent statements are

#define MAX_LEN 60
char a[MAX_LEN];
strcpy(s,”Hello”);
To be safe, use strncpy(s,”Hello”,MAX_LEN-2); and terminate it with the \0 character.
s[MAX_LEN-1]=0;

Note that array indices for arrays always start from 0 and will reach MAX_LEN-1. Since C strings are terminated by \0, the usable size of a C string is MAX_LEN-2.
Why does strncpy not place a ‘\0’ terminator in the destination string ?
Originally, strncpy was designed to handle a fixed length(but not necessarily \0 terminated) “string” strncpy is a cumbersome to use in the normal string contexts. You must always add the terminating ‘\0’ to the destination string after the call. The workaround to this problem is to use strcat instead of strncpy. If the destination string is an empty string, strncat does what strncpy does (and adds a \0 terminator too at the end of the destination.

char greeting[200]=”Hello ”;
char world[10]=”World”;

strncpy(greeting, world); //does not terminate the destination with \0

char *dest=greeting+strlen(greeting);    //dest points to the \0 terminator of greeting
strncat(dest,source,strlen(world));
This code copies upto n characters and always appends a \0.
Why do I have the n-versions of string functions like strncpy, strncat and strnlen ?
The n-versions of string functions (strncpy, strncat, strnlen, etc) enforce better string and array handling practices but if improperly used can still lead to buffer overflows. There are points to be aware of:
The n-functions will fill any remaining space in a target buffer with null bytes, reducing application performance; and
If a source string is larger than the target buffer, the string will be concatenated, but no null byte will terminate the target buffer.
