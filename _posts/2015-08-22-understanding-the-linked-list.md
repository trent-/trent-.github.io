---
layout: post
title:  Understanding the linked list
date:   2015-09-15 10:51:00
---

A common data structure you should be all to used to is an Array. This defines a list of elements of some pre-determined size the run sequentially in memory.

| 0 | 1 | 2 | 3 |
|---|---|---|---|
| 1 | 2 | 5 | 3 |

The next most basic form of data structure to get around this fixed size limitation is a linked list. The general idea of a linked list is that you are able to dynamically add and remove elements as you see fit. This is achieved by defining a structure that contains the element information, as well as a link to the next item - where it would be NULL up until you wish to add a new element.

In the C programming language, this is achieved with a pointer. Every piece of data you define in C has a data type - pointers are defined in the same way with one minor difference. You denote the fact it is a pointer by adding an asterisk at the front of the variable. You also reference the memory address of a variable by prefixing it with the ampersand (&) symbol.

```c
int a = 2;//an int datatype
int *aPointer;//aPointer must store a memory address of the int datatype.
aPointer = &a;//&a gets the memory address of a
```

Further, if you want to get the value a pointer points to, you again need to prefix it with an asterisk (which is often referred to as deferencing).

```c
int a = 2;
int *aPointer;
aPointer = &a;

printf("Value of a is: %d\n", a);
printf("Value of dereferenced aPointer is: %d\n", *aPointer);
a = 5;
printf("Value of a is: %d\n", a);
printf("Value of dereferenced aPointer is: %d\n", *aPointer);
*aPointer = 20;
printf("Value of a is: %d\n", a);
printf("Value of dereferenced aPointer is: %d\n", *aPointer);
```

The above would produce the following output:

```
Value of a is: 2
Value of dereferenced aPointer is: 2
Value of a is: 5
Value of dereferenced aPointer is: 5
Value of a is: 20
Value of dereferenced aPointer is: 20
```

So what we effectively has is two variables pointing to the same memory address. This concept is important for when you want to pass values around in functions. In general, parameters become copies of their original. However, by passing a pointer to a variable, you can modify their values and ensure any modifications are reflected from the calling function.

So, from here, we can start to construct our linked list. You can create custom objects in C with a `struct`. The body of the struct would contain any properties you would like, as well as a link to the next node (which points to the same struct data type). For a basic example, I just want to have a linked list of integers, with the business concept of recording the number of attendees in a classroom.

The basic syntax for defining a struct is:

```c
stuct structName{
    propertyDataType propertyName;
    //repeat
}
```

However, in the body of your code, whenever you want to declare a variable of that struct type, you need to prefix with struct. So it is helpful to create a typedef on the struct `typedef struct structName structName`. This will allow us to simply specify the datatype of `structName` instead of `struct structName`. This can be further simplified by combining the two.

```c
typedef struct structName {
    propertyDataType propertyName;
    //repeat
} structName
```

So, with that in mind, I will create my struct named `attendeeList` as follows:

```c
typedef struct attendeeList {
	int attendees;
	struct attendeeList *nextNode;
} attendeeList;
```

With that set up, the basic operations you would most likely want on your data structure are:

* print list
* add element
* delete element
* get index

Since this particular list is single-directional, in the main body of your program, you will want a variable that points to the first element in the list. Then in each function of your list, you will iterate over each element.

The first function you will likely want is a print function. This enables you to see what is in the list.

```c
void printList(attendeeList *l){

    attendeeList *currentNode;

    currentNode = l;
    while (currentNode != NULL){
        printf("Num attendees: %d\n", currentNode->attendees);
        currentNode = currentNode->nextNode;
    }
    printf("\n");

}
```

Then, adding items to the list. The scope of function variables is such that they only last for the life of the particular block - that could be a function or a control block.
In C we can manually allocate space to a variable with the `malloc` (a part of the stdlib library) function. Then when we point a pointer to that memory address, it means we can still access the data after the life of the function.

So the basic process is that you want to allocate some memory to a new node, then navigate to the last element in the list, and point it at this new node.

```c
void addAttendees(attendeeList **l, int count){

    attendeeList *newNode, *currentNode;

    newNode = (attendeeList *) malloc(sizeof(newNode));
    newNode->attendees = count;

    if (*l == NULL){
        *l = newNode;
    } else {
        currentNode = *l;
        while(currentNode->nextNode != NULL){
            currentNode = currentNode->nextNode;
        }

        currentNode->nextNode = newNode;
    }

    printf("New node added with count: %d\n", count);

}
```

Then, do delete an element (in this example, the last item added) you want to navigate to the end of the list, whilst keeping track of both the currentNode and previousNode. Once you get to the end, moving the reference to currentNode, and then freeing that block of memory.

```c
void deleteAttendee(attendeeList **l){

    attendeeList *currentNode, *previousNode;

    currentNode = *l;

    if (l != NULL){

        while(currentNode->nextNode != NULL){
            previousNode = currentNode;
            currentNode = currentNode->nextNode;
        }

        previousNode->nextNode = NULL;
        free(currentNode);
    }
}
```

The final example of my list of tasks to add, is getting the index of a particular element. Since there is no sorting on this structure, we just have to check node by node until we get to the end of the list.

```c
int getAttendeeIndex(attendeeList *l, int count){

    int const NOT_FOUND = -1;

    attendeeList *currentNode;

    if (l != NULL) {
        currentNode = l;
        int index = 0;

        while(currentNode != NULL){
            printf("Checking %d\n", currentNode->attendees);

            if (currentNode->attendees == count){
                return index;
            } else {
                index++;
            }

            currentNode = currentNode->nextNode;


        }

    }

    return NOT_FOUND;
}
```

With all this, the program would look like this:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct attendeeList {
	int attendees;
	struct attendeeList *nextNode;
} attendeeList;

void printList(attendeeList *l){

    attendeeList *currentNode;

    currentNode = l;
    while (currentNode != NULL){
        printf("Num attendees: %d\n", currentNode->attendees);
        currentNode = currentNode->nextNode;
    }
    printf("\n");

}

void addAttendees(attendeeList **l, int count){

    attendeeList *newNode, *currentNode;

    newNode = (attendeeList *) malloc(sizeof(newNode));
    newNode->attendees = count;

    if (*l == NULL){
        *l = newNode;
    } else {
        currentNode = *l;
        while(currentNode->nextNode != NULL){
            currentNode = currentNode->nextNode;
        }

        currentNode->nextNode = newNode;
    }

    printf("New node added with count: %d\n", count);

}

void deleteAttendee(attendeeList **l){

    attendeeList *currentNode, *previousNode;

    currentNode = *l;

    if (l != NULL){

        while(currentNode->nextNode != NULL){
            previousNode = currentNode;
            currentNode = currentNode->nextNode;
        }

        previousNode->nextNode = NULL;
        free(currentNode);
    }
}

int getAttendeeIndex(attendeeList *l, int count){

    int const NOT_FOUND = -1;

    attendeeList *currentNode;

    if (l != NULL) {
        currentNode = l;
        int index = 0;

        while(currentNode != NULL){
            printf("Checking %d\n", currentNode->attendees);

            if (currentNode->attendees == count){
                return index;
            } else {
                index++;
            }

            currentNode = currentNode->nextNode;


        }

    }

    return NOT_FOUND;
}

int main(void){

    attendeeList *myList;

    myList = NULL;
    addAttendees(&myList, 55);
    addAttendees(&myList, 125);
    addAttendees(&myList, 72);
    addAttendees(&myList, 53);

    printList(myList);

    deleteAttendee(&myList);

    printList(myList);
    return 0;
}
```
