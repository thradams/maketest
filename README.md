## Make Tests

This program scans the source files passed on the command line and collects test functions that are inside ifdef TEST.

It generates an ouputfile tha calls all test functions.

```
maketest output.c file1.c file2.c ....
```
or it also can be used with VC++ projects.

```
maketestvc project.vcxproj output.c
```

Sample:

file1.c

```cpp
#ifdef TEST
void MyTestCase(void) {
}
#endif
```

file2.c
```cpp
#ifdef TEST
void MyTestCase2(void) {
}
#endif
```

```cpp
maketest output.c file1.c file2.c
````

generates output.c

```cpp
#ifdef TEST

//forward declarations

//file1.c
void MyTestCase(void);

//file2.c
void MyTestCase2(void);

void DoUnitTests(void)
{
    //file1.c
    MyTestCase();    
    
    //file2.c
    MyTestCase2();    
}
#else
; //removes warning C4206: nonstandard extension used: translation unit is empty
#endif
```

### How Do I use it?

I define my ASSERT at unittest.h

```cpp
#pragma once
#include <stdio.h>

#define GREEN "\x1b[32m"
#define RED   "\x1b[31;1m"
#define RESET "\x1b[0m"

#define ASSERT(B) printf("%*d : ",  4, __LINE__ ); \
                  if (!(B)) {\
                      printf(RED "ERROR!" RESET); \
                      printf(" '%s' \n", #B); \
                      exit(1); \
                  } \
                  else {\
                      printf(GREEN "    OK" RESET);\
                      printf(" '%s' \n", #B); \
                  }
```

... then I include inside ifdef TEST block

```cpp
/*
   Normal code goes here...
*/

#ifdef TEST
#include "unittest.h"
void MyTestCase2(void)
{
    ASSERT(1 == 1);
}
#endif
```

In my main() function:

```cpp
#ifdef TEST
void DoUnitTests(void);//lint !e2701
#endif

int main(int argc, char** argv)
{
#ifdef TEST
        DoUnitTests();
#endif
}
```

