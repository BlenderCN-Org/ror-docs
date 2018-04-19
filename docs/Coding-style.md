---
title: Coding style
---

### The template


**Indentation:** 4 spaces. Tabs are forbidden.

NOTE: The codebase uses mostly tabs at the moment ... cleanup in progress.

**Header:**

```

/*                                                   //STYLE: License block
    This source file is part of Rigs of Rods
    Copyright 2005-2012 Pierre-Michel Ricordel
    Copyright 2007-2012 Thomas Fischer
    Copyright 2013-2016 Petr Ohlidal

    For more information, see http://www.rigsofrods.com/

    Rigs of Rods is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License version 3, as
    published by the Free Software Foundation.

    Rigs of Rods is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with Rigs of Rods.  If not, see <http://www.gnu.org/licenses/>.
*/

/**
    @file   CodingStyleTemplate.h
    @brief  Wiki coding style template
    @author CrazyTrucker
    @date   05/2014
*/
                                             //STYLE: blank line!
#pragma once                                         //STYLE: header guard (we don't use header guard macros anymore)
                                                     //STYLE: blank line!
#include "RoRPrerequisites.h"                        //STYLE: RoR includes.
#include "BeamData.h"                                //    Try minimizing the dependencies between headers
#include "Flexable.h"                                //    Take advantage of forward declarations
#include "MaterialFunctionMapper.h"
                                                     //STYLE: blank line!
#include <OgreVector3.h>                             //STYLE: External includes
#include <OgreQuaternion.h>                          //    Again, try minimizing the dependencies between headers
#include <OgreHardwareVertexBuffer.h>                //    Including individual OGRE headers is preferred to including Ogre.h
#include <OgreMesh.h>
                                                     //STYLE: blank line!

namespace CodingTemplate                             //STYLE: Namespace
{                                                    //    NOTE: No indentation in namespace!

/// Brief description (must be ended with dot).
/// Detailed description.
struct CodingStyleDemoStruct
{                                                   //STYLE: { on new line.
    CodingStyleDemoStruct():                        //STYLE: Initializing constructor.
        struct_member_variable(0),
        another_member_variable(0.f)
    {}
                                                    //STYLE: blank line!
    int   struct_member_variable; //!< Description  //STYLE: variables and documentation. Mind the alignment.
    float another_member_variable; //!< Description
};


/// Brief description (must be ended with dot).       //STYLE: Class, inheritance
/// Detailed description.                             //    **REMEMBER!** Public inheritance means **IS-A!**
class CodingStyleDemoClass: public BaseDemoClass      //    "class B: public A" means "B is also A" !!
{
                                                    //STYLE: blank line before member struct!
    /// Just for little cleaner code...
    struct MemberStructure                          //STYLE: member struct
    {
        SubStructure(int stuff)                     //STYLE: Parametrized constructor
            stuff(stuff)
        {}

        int stuff;
    };
                                                    //STYLE: blank line!
public:  
                                                    //STYLE: blank line!
    CodingStyleDemoClass():
        m_private_member_variable(0),
        m_another_private_variable(0.f),
        m_example_pointer(nullptr),                 //STYLE: use 'nullptr' keyword for NULL pointers
        m_example_calculator(nullptr)
    {}
                                                    //STYLE: blank line!
    enum PossibleOptions                            //STYLE: enum
    {
        OPTION_BEGIN                                //STYLE: special option
        OPTION_FOO //!< Description                 //STYLE: all options have equal prefix
        OPTION_BAR //!< Description
        OPTION_BAZ //!< Description
        OPTION_END                                  //STYLE: special option
        OPTION_INVALID = 0xFFFFFFFF                 //STYLE: special option and value
    };

    static const unsigned int DEPOT_SERVICE       = BITMASK(1); //STYLE: bit flags
    static const unsigned int DEPOT_MASS_SEND     = BITMASK(2); //    Use BITMASK() from RoRPrerequisites.h
    static const unsigned int DEPOT_DONT_CANCEL   = BITMASK(3); //    Do NOT use enums for bitflags
    static const unsigned int DEPOT_LOCATE_HANGAR = BITMASK(4); //      because it blocks using | operator
                                                    //STYLE: blank line!

                                                    //STYLE: blank line!
public:
                                                    //STYLE: blank line!
    char public_member;                             //STYLE: public member
                                                    //STYLE: blank line!
    int GetPrivateMemberVariable()                  //STYLE: function (getter)
    {
        return m_private_member_variable;           //STYLE: Don't use {{this->}} to access members
    }

    /// Returns highest number
    int FunctionWithManyArguments(                  //STYLE: Functions with too long argument list
        int first_argument,                         //    ... will be split to multiple lines.
        int second_argument,
        int third_argument,
        int fourth_argument,                        //    NOTE: Define your functions in .cpp files
        int fifth_argument,                         //    ... to decrease compile dependencies!
        int last_argument                           //    One-line getters (as above) are fine though.
    );

    int  GetMemberValue() { return m_private_member_variable; }  //STYLE: Getter function -> `Get` in name

    int  ComputeHighestNumberUsingBranches(int a, int b, int c); //STYLE: Not a getter -> no `Get` in name
    int  ComputeHighestNumberUsingConditionals(int a, int b, int c, int d, int e, int f);
    int  AddNumbers(int a, int b);
    bool IsPointerNotNull();                        //STYLE: A `bool` method is named as yes/no question.
                                                    //STYLE: blank line!
private:
                                                    //STYLE: privates are prefixed m_. Mind the alignment.
    int                m_private_member_variable;
    float              m_another_private_variable;
    PointedClass*      m_example_pointer;           //STYLE: pointer member ~ * at datatype
    ExampleCalculator* m_example_calculator;    

};

} // namespace CodingTemplate                       //STYLE: End of namespace.
```


**Source:**

```
                                                     //STYLE: blank line!
#include "CodingTemplate.hpp"                        //STYLE: The associated header on first line.
                                                     //STYLE: blank line!
#include "AnotherHeader.hpp"                         //STYLE: RoR includes.
                                                     //STYLE: blank line!
#include "OgreMaterialManager.h"                     //STYLE: External includes

namespace CodingTemplate                             //STYLE: Namespace
{                                                    //    NOTE: No indentation in namespace!

int CodingStyleDemoClass::FunctionWithManyArguments( //STYLE: Functions with too long argument list
    int first_argument,                              //    ... will be split to multiple lines.
    int second_argument,
    int third_argument,
    int fourth_argument,
    int fifth_argument,
    int last_argument
)
{
    int a = this->GetHighestNumberUsingBranches(     // STYLE: using explicit this->
        first_argument,                              //    to distinguis member functions
        second_argument,                             //    and global functions
        third_argument
    );

    int b = this->GetHighestNumberUsingBranches(     // STYLE: Explicit this->
        fourth_argument,
        fifth_argument,
        last_argument
    );

    return (a > b) ? a : b;
}

int CodingStyleDemoClass:::ComputeHighestNumberUsingBranches(int a, int b, int c)
{
    if (a < b)                                       //STYLE: if() statements
    {                                                //    {} must always be present
        if (b < c)                                   //    {} must always be on new line
        {                                            //    NOTE: Always consider using conditionals (below)
            return c;
        }
        else
        {
            return b;
        }
    }
    else
    {
        if (a < c)
        {
            return c;
        }
        else
        {
            return a;
        }
    }
}

int CodingStyleDemoClass::ComputeHighestNumberUsingConditionals(int a, int b, int c, int d, int e, int f)
{
    int highest = (a > b) ? a : b;                   //STYLE: conditional assignments
    highest = (highest > c) ? highest : c;           //    Use them! :)
    highest = (highest > d) ? highest : d;
    highest = (highest > e) ? highest : e;
    return (highest > f) ? highest : f;
}

bool CodingStyleDemoClass::IsPointerNotNull()
{                                                    //STYLE: NULL pointer test
    return (m_example_calculator != nullptr)         //    Using {{if (pointer)}} conditions is forbidden
{                                                    //    ... only bools can be tested this way.

int CodingStyleDemoClass::AddNumbers(int a, int b)
{
    assert (m_example_calculator != nullptr);        //STYLE: NULL pointer debug test

    return m_example_calculator->Add(a, b);          //STYLE: Don't use {{this->}} to access members
}

} // namespace CodingTemplate                        //STYLE: End of namespace.
```


### Functions

* Function names use [CamelCase](http://en.wikipedia.org/wiki/CamelCase) without underscores.
* Opening curly bracket **{** for a function starts on the next line.
* Methods (member functions) should be referenced by explicit "this-&gt;" to distinguish them from global functions.
* Methods have some naming conventions:
    * GetSomething() -- Getter function, probably inline. Only fetches member value or does simple arithmetic. Doesn't perform complex computations.
    * Is/Was/HasSomething()  -- Getter function returning bool. Only fetches member value or does simple arithmetic. Doesn't perform complex computations.
    * SetSomething()  -- Setter function. Only assigns value to member variable or does simple arithmetic. Doesn't perform complex computations.

```
void ThisIsAFunction()
{
}
```

### Variables

* Variable names are all lowercase, and use "_" as separator.
* Global variables must not be used<br>
* Member variables should be prefixed "m_" and not be referenced using "this-&gt;".
* Boolean variable names should be yes/no questions, i.e. "is_skeletonview_active" or "was_position_changed".
* Pointers and references should have their reference symbol next to the type name (modern style!).
* Variables that are declared below one another should have their type, name or reference operator, and assignment operator aligned by spaces.<br>
* Declare variables upon first usage. <br>
* Always initialize variables with a default value.&nbsp;Do not use uninitialized variables.
* Declare iterators in their loop.

```
int     number         = 10;
Vehicle *u_first_wagon = v->next;
int     value          = v->value;

uint32 _global_variable = 3750;

protected:
    char protected_array[10];

for (int i = 0;;);
```

* Give the variables expedient names, this increases the readability of the code

```
bool is_maglev_train = true;
if (!is_maglev_train) DoSomething();
```

### Enumerations

* Enumerations represent a set of legal values for a variable.
* Enum name is CamelCase, enum members are UPPERCASE_WITH_UNDERSCORES
* Enum values may only be integral numbers. For bit-masks, use constants
* If the enum has *_INVALID field, it's value must be 0xFFFFFFFF
* Other "invalid" values have consecutively lower values than *_INVALID
* Variables storing enum value should not be declared as plain int. Use enum name.
* Comments of members should start with "///&lt;" to enable doxygen documentation.
* Do not typedef enums. Writing "typedef enum {A, B, C} MyEnum" is forbidden. It's a C-language trick which has no meaning in C++ and confuses IDE tools.

```
enum DiagDirection
{
	DIAGDIR_NE       = 0, ///< North east
	DIAGDIR_SE       = 1, ///< South east
	DIAGDIR_SW       = 2, ///< South west
	DIAGDIR_NW       = 3, ///< North west
	INVALID_DIAGDIR  = 0xFFFFFFFF,
	BROKEN_DIAGDIR   = 0xFFFFFFFE,
};

DiagDirection enterdir = DIAGDIR_NE;
```

### Static constants
* All constant values.
* Bit-masks (unsigned int)
```
static const int MAXIMUM_STATIONS = 42;
static const unsigned int BITFLAG_NOT_ACTIVE = BITMASK(2); // Macro defined in BitMask.h
```

### Classes and structs

* Classes names use CamelCase.
* Classes should have "public", "protected", and "private" sections.
* Order of elements in class is: types (incl. member structs/classes), constructors/destructors, methods, static methods, members.
* Deviations from above order are allowed when the code dictates it (e.g. a static const is needed for a typedef)  
* The method implementation should indicate if it is virtual or similar by using a comment.
* Do not typedef structs. Writing "typedef struct {A, B, C} MyStruct" is forbidden. It's a C-language trick which has no meaning in C++ and confuses IDE tools.

```
class ThisIsAClass
{
public:
    typedef Titem_   *ItemPtr;

    ThisIsAClass();
    ~ThisIsAClass();

    virtual void Method();

private:
    static const int MS_MAX_SIZE = 500;
    int              m_size;
    ItemPtr          *m_items;  
};

/*virtual*/ void Class::Method()
{
    m_size *= 2;
}
```

### Other important rules

* Put a space before and after binary operators: a + b, a == b, a &amp; b, a &lt;&lt;= b, etc.. Exceptions are ".", "-&gt;" and "[]" (no spaces)
* Put brackets where it improves readability: *(b++) instead of *b++.  
* Use const where possible.

* Don't treat non-flags as flags: use "<tt>if (char_pointer&nbsp;!= NULL &amp;&amp; *char_pointer&nbsp;!= '\0')</tt>", not "<tt>if (char_pointer &amp;&amp; *char_pointer)</tt>".<br>
* No trailing whitespace. The svn server will not allow tabs or space on the end of lines.
* Only use tabs to indent from the start of the line.
* Use /* */ for single line comments.
* Use // at the end of a command line to indicate comments.

## Optimization
In RoR codebase, it's OK to optimize. That's a guideline!

### The #1 optimization technique
... is simplicity, as in "straightforward code". Seriously. Straightforward code
usually doesn't leave any space for optimization, and if it does, it's obvious what can be improved.

### Inline functions

There are 2 ways to use inline functions:

* **Explicit (with 'inline' keyword):** Always supported, require some extra care from the programmer. They must be prefixed with 'inline' keyword and function body must be in header file. The compiler may decide not to inline them.

* **Fully automated:** Compilier decides which functions to inline, and when. Supported by modern compilers as part of [inter-procedural optimization](http://en.wikipedia.org/wiki/Interprocedural_optimization) feature. This feature is disabled by default (because it slows down building a lot) and needs explicit compiler flags to be enabled (\GL and \LTCG in MSVC, --flto in GCC and Clang)

In RoR, the former approach (explicit inline functions) is preffered to help developers - the
"ReleaseWithDebugInfo" target can build a lot faster with "inter-procedural optimizations" disabled.

## Documentation

We use [Doxygen](http://doxygen.org/) to automatically generate documentation from the source code. It scans the source files for ''recognizable'' comments.

The doxygen user manual can be found there: http://www.doxygen.nl/manual.html
