# Structures and Pointers
How are structures represented in memory ?
Structures, like arrays are stored in contiguous locations in the memory. In the following declaration
struct  employee
{
    int id;
    char name[32];
    int age;
    float salary;
} employee1;

Assuming the address of employee1 begins at location 4000, the id which is integer occupies memory of 4000 to 4003. The 32byte name occupies memory locations 4004 to 4035. The age occupies memory locations 4036 to 4039. The salary occupies memory locations 4040 to 4043.
The size of the array is 44 bytes and it spans from 4000 to 4043.

You can peek at the individual bytes of the structure by initialising a pointer to char and traversing the structure as shown below

char *ptr=(char *)&employee1;
int i;
for(i=0;i<sizeof(employee1);i++)
{
printf(“Byte %d of employee1 is %d\n”,ptr[i]);
}


How are structures passed to functions ?
Structures are first class members in C. Structures are treated as primary data types (like int, char, float) and not derived data types(array). Hence when a structure is passed to a function, the whole "structure" gets passed to the function(unlike where only the address gets passed in case of array). Thus any change in the values of fields in the structure won't be reflected to the caller after the function returns. 
Passing huge structures to functions is an overhead as it involves copying of the whole structure data byte by byte to the called function.  Hence it is advisable to pass a pointer to a constant struct to a function instead of passing the complete structure. Some clever compilers do this behind the scenes and generate optimised code.
main()
{
    struct header 
    {
        char bytes[44];
        int type;
    }head;
...
    print_header(head);//48 bytes(size of structure) are copied on to the stack
}
void print_header(struct header h)
{
    printf(“Header=%s type=%d\n”,h.header,h.type);
}
The above code can easily be optimised by using pointer to a constant structure as shown below.
struct header 
{
    char bytes[44];
    int type;
} head;
...
print_header(&head);
//4 bytes(address of the struct is copied on to the stack
}
void print_header(struct header *h)
{
printf(“Header=%s type=%d\n”,h->header,h->type);
}

How do I access a member of a structure using a pointer to it ?
To access a structure member through a pointer, you need to use -> operator. Note how the days, hours, minutes variables are accessed using -> on utc variable which is a pointer to struct tm.


#include<time.h>
main()
{
	struct timeval tv;
	struct timezone tz;
	struct tm   *utc;
	time_t      now;
	struct tm *tm;
	gettimeofday(&tv, &tz);
	tm=localtime(&tv.tv_sec);
	now = time((time_t *)0);
	utc = localtime(&now);
  printf("%4d:%02d:%02d:%02d:%02d:%02d:%06d\n",1900+utc->tm_year,++utc->tm_mon,utc->tm_mday,tm->tm_hour, tm->tm_min,tm->tm_sec, tv.tv_usec);

}


Why *ptr.x does not work ?
The parentheses are necessary in (*ptx).x because the precedence of the structure member operator . is higher than *. The expression *ptr.x means *(ptr.x) which is illegal because x is not a pointer.

What is this strange type of memory allocation of a structure with zero/one sized array at the end ?
struct w
{
    int len;
    char word[1];
};
This is a common “trick” where the word can be of varying size(unlike the structure declaration suggests). An implementation could be 
struct tuple *storeword(char *newword)
{
struct tuple *ret=malloc(sizeof(struct w)-1+strlen(newword)+1);
ret->len=strlen(newword);
strcpy(ret->word,newword);
}

I want to copy specific number of bytes from source to destination without using any library functions. How do I do it ?
You can use a small struct hack to simulate a memcpy(). Since structures are first class variables, we can assign two structures of same type, whatever the size of the structure may be. By type casting a character buffer to a pointer to a structure, we can "assign" a character buffer to another character buffer as shown below.

void copy_bytes(char *src,char *dest)
{
        struct s
        {
                char data[48];
        };

        *((struct  s *)dest)=*((struct s *)src);
}

What is this strange and unobvious clever structure type casting ?
#include <sys/types.h>          
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr,  socklen_t addrlen);

...
struct sockaddr_in serv_addr,cli_addr;
........
retval=bind(listenfd,(struct sockaddr*)&serv_addr,sizeof(serv_addr));


The signature of the bind shows that the second argument is of type struct sockaddr *, but we are passing struct sockaddr_in * typecasted to struct_sockaddr *. Is this correct ?

Some information on bind. bind() assigns the address specified to by addr to the socket referred to by the file descriptor sockfd. addrlen specifies the size, in bytes, of the address structure pointed to by addr.  Traditionally, this operation is called "assigning a name to a socket". The bind system call allows a portable way of a server program listening on a socket to bind to a specific server entity based on the type of the socket. For a TCP socket, of family AF_INET, the bind allows the server program to bind to the well known port of the server. For a unix socket, of family AF_UNIX, the bind allows the server program to a well known path of the server. The sockaddr structure is a generic structure having the following members

struct sockaddr {
    u_short sa_family;            /* address family;  AF_xxx */
    char    sa_data[14];        /* protocol specific address */
}; 
The only purpose of this structure is to cast the structure pointer passed in addr in order to avoid compiler warnings.
The protocol specific structure definitions do exist, 

The Unix domain socket address structure sockaddr_un is defined in <sys/un.h>.
struct sockaddr_un{
    short sun_family;                /*AF_UNIX*/
    char sun_PATH[108];        /*path name */
};


struct sockaddr_in {
    short sin_family;                   /* AF_INET */
    u_short sin_port;                       /* 16-bit port number */
    struct in_addr sin_addr;
    char sin_zero[8];                  /* unused */
}; 

In reality, the behaviour of bind when binding a unix socket is different from the behaviour of bind when binding a tcp socket. However the bind system call provides a portable way of using it for various types of address families.
This is achieved by clever casting of the structures.

Observe that the size of the first member in each structure represents the address family of the type of socket and is same in all the three structures - short.

The code in the bind could be something like this


int bind(int sockfd, struct sockfd, const struct sockaddr *addr, socklen_t addrlen)
{
struct sockaddr_un *un;
struct sockaddr_in *in;

short family;
int port;	    //to store the tcp port number of the server to which the program has to be bound
struct in_addr in; //to store the ipaddress of the server to which the program has to be bound

char *unix_path;  //to store the unix path in case of unix domain sockets
family=addr->sa_family;  //This will extract the first 2 bytes of whatever struct pointer you pass. Since both the structs have the first field as 2 bytes, this will fetch the address family of the socket.

//Now do a switch

switch(family)
{
case AF_INET: /*Code for binding to a tcp port using the rest of data in the structure*/

in=(struct sockaddr_in *)addr;   //cast it back to the original strucutre pointer

port=in->sin_port;//extract the port to be bound
in_addr=in->sin_addr;//extract the server address to be bound
....
break;

case AF_UNIX:/*Code for binding to a specific unix path based on the data in the structure*/
un=(struct sockaddr *)addr;   //cast it back to the original strucutre pointer

path=un->sun_path;  //point path to the unix path
break;

}  //end of switch
...
...

}//end of bind

By providing an abstract interface for binding a program to a server using structure typecasting, there are some benefits. No need to remember the system call for binding to server for each family of socket. New functionality for a new family of socket can easily be extended in the bind function. Migrating a program from one socket family to another will need minimal changes.

By clever use of typecasting of structures, portable and extensible interfaces can be created. This is the power of pointers to structures.
 


