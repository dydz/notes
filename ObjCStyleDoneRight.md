# Objective-C Style Done Right

This is my style. This guide has two parts:

1. The philosophy
2. The cookbook (aka the reference): If-this-then-do-that.

My style is heavily influenced by:

1. <https://webkit.org/code-style-guidelines/>
2. <https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html>
3. [K&R](https://en.wikipedia.org/wiki/The_C_Programming_Language)

## The Philosophy

### Whitespace

As I type code I think about:

1. How it relates to the code above and (if already written) below it.
2. Whether the code should stand out or not.

Whitespace is one of the ways I use to address these things. I use whitespace before and after code to group related lines into paragraphs. That does **not** always mean I put an empty line after an if/while/for/etc-statement. It also means sometimes there is only **1** paragraph in a function or struct.

Unless the language mandates it to produce correct code gen, I use whitespace instead of braces.

### Pick the right control and data structures

The right way to do software engineering is to pick the right structures for the code being written. There are control structures (e.g. if, while, do, for, switch) and data structures (e.g. arrays, unions, etc). Finding the right structure is a skill, but here are 2 different general strategies I use when I can't identify it from the get-go:

Top down:

1. Free flow write code and get a working solution.
2. Review each line of code, each assigment, each control flow statement, etc etc and ask:
   a. Am I repeating myself? If so, see (b).
   b. Is there an another language structure that would be better suited for what is being done? If so, use it.

Bottom up:

1. Solve the problem using the absolute mimimum number of machine instructions and memory footprint (I find this exercise to be a fun game). This tends to produce a very clever solution that would require comments, but I don't want to add comments if the code can be made intutive by using other langauge structures. So, I...
2. Work backwards from (1) replacing low level primitives with higher level ones until the solution no longer needs comments.

Once I have the right structures in place then it's time to consult the cookbook!

## The Cookbook
Note that portions of this section were taken in whole or in part from <<https://webkit.org/code-style-guidelines/>>.

### Indentation

1. Use spaces, not tabs. Tabs can be used where they have semantic meaning, like in string and character literals:

   ```objc
   static const char kTabCharacter = '	';
   ```

2. Use 4 spaces for indentation.

Right:

```objc
static BOOL myFunction()
{
    return YES;
}
```

Wrong:

```objc
static BOOL myFunction()
{
                    return YES;
}
```

### Spacing

#### Unary operators

1. Do not place spaces around unary operators.

Right:

```objc
i++;
++i;
```

Wrong:

```objc
i ++;
++ i;
```

#### Binary and ternary operators

1. Do place spaces around binary and ternary operators.

Right:

```objc
z = x * x + y * y;
f(a, b);
c = a | b;
return condition ? 1 : 0;
return condition ?: nil;
```

Wrong:

```objc
z=x*x+y*y;
f(a,b);
c = a|b;
return condition ? 1:0;
return condition ?:nil;
```

#### Comma and semicolon

1. Do not place spaces before comma and semicolon.

Right:

```objc
for (int i = 0; i < 10; ++i)
    doSomething();

f(a, b);
```

Wrong:

```objc
for (int i = 0 ; i < 10 ; ++i)
    doSomething();

f(a , b) ;
```

#### Control statements

1. Do place spaces between control statements and their parentheses.

Right

```objc
if (condition)
    doSomething();
```

Wrong:

```objc
if(condition)
    doSomething();
```

#### Functions

1. Do not place spaces between a function and its parentheses, or between a parenthesis and its content.

Right

```objc
f(a, b);
```

Wrong:

```objc
f (a, b);
f( a, b );
```

#### Blocks

1. Do not place spaces between the start of a block and its arguments, or the start of a block and its opening brace.
2. Do place a space between argument lists and the opening brace of the block.

Right:

```objc
checkState = ^{
  ...
};

checkState = ^(int, int) {
  ...
};
```

Wrong:

```objc
checkState = ^ {
  ...
};

checkState = ^ (int, int) {
  ...
};
```

### Line Breaking

1. Each statement should be on its own line because:

   ​	a. This makes it easier to see the control flow of the program.

   ​	b. Because of (a) it is easier to set breakpoints on each statement.

   ​	c. It helps make sequences (i.e. things that use the comma operator e.g.`x = (a = b, a * a)`)  stand out more by making statements stand out less, which is good because sequences tend to be harder to read and reason about.

Right:

```obj
x++;
y++;
if (condition)
    f();
```

Wrong:

```objc
x++; y++;
if (condition) f();
```

2. An `else` statement should go on the same line as a preceding close brace if one is present, else it should line up with the `if` statement.

Right:

```objc
if (condition) {
    ...
} else {
    ...
}

if (condition)
    doSomething();
else
    doSomethingElse();

if (condition)
    doSomething();
else {
    ...
}
```

Wrong:

```objc
if (condition) {
    ...
}
else {
    ...
}

if (condition) doSomething(); else doSomethingElse();

if (condition) doSomething(); else {
    ...
}
```

3. An `else if` statement should be written as an `if` statement when the prior `if` concludes with a `return` statement.

Right:

```objc
if (condition) {
    ...
    return someValue;
}
if (condition) {
    ...
}
```

Wrong:

```objc
if (condition) {
    ...
    return someValue;
} else if (condition) {
    ...
}
```

### Braces

#### Functions

Place each brace on its own line.

Right:

```objc
int main()
{
    ...
}
```

Wrong:

```objc
int main() {
    ...
}
```

#### Structs, control clauses and everything else

Place the open brace on the line preceding the code block; place the close brace on its own line.

Right:

```objc
struct MyStruct {
    ...
};

for (int i = 0; i < 10; ++i) {
    ...
}
```

Wrong:

```objc
struct MyStruct
{
    ...
};

for (int i = 0; i < 10; ++i)
{
    ...
}
```

#### One-line control clauses

Should not use braces unless comments are included or a single statement spans multiple lines.

Right:

```objc
if (condition)
    doIt();

if (condition) {
    // Some comment
    doIt();
}

if (condition) {
    myFunction(reallyLongParam1, reallyLongParam2, ...
        reallyLongParam5);
}
```

Wrong:

```objc
if (condition) {
    doIt();
}

if (condition)
    // Some comment
    doIt();

if (condition)
    myFunction(reallyLongParam1, reallyLongParam2, ...
        reallyLongParam5);
```

#### Control clauses without a body

Right:

```objc
for ( ; current; current = current->next) { }
```

Wrong:

```objc
for ( ; current; current = current->next);
```

### Null, false and zero

1. In Objective-C++, the null pointer value for a C++ type should be written as `nullptr`. For a C type, it should be written as `NULL`. For an Objective-C object it should be written as `nil`.

2. Objective-C `BOOL` values should be written as `YES` and `NO`.
3. In Objective-C, instance variables are initialized to zero automatically. Don’t add explicit initializations to nil or NO in an init method.
4. Tests for true/false, null/non-null, and zero/non-zero should all be done without equality comparisons:

Right:

```objc
if (condition)
    doIt();

if (!ptr)
    return;

if (!count)
    return;
```

Wrong:

```objc
if (condition == YES)
    doIt();

if (ptr == NULL)
    return;

if (count == 0)
    return;
```

### Floating point literals

Unless required in order to force floating point math, do not append `.0`, `.f` and `.0f` to floating point literals.

Right:
```objc
const double duration = 60;

- (void)setDiameter:(float)diameter
{
    _radius = diameter / 2;
}

[self setDiameter:10];

const int framesPerSecond = 12;
double frameDuration = 1.0 / framesPerSecond;
```

Wrong:
```objc
const double duration = 60.0;

- (void)setDiameter:(float)diameter
{
    _radius = diameter / 2.f;
}

[self setDiameter:10.f];

const int framesPerSecond = 12;
double frameDuration = 1 / framesPerSecond; // integer division
```

### Properties, instance variables, setters and getters

1. Use properties instead of manually declaring setters and getter and defining instance variables.

Right:

```objc
@interface Document
@property (nonatomic) Frame *frame;
@property (nonatomic) NSURL *url;
@property (nonatomic, readonly) NSString *title;
@end
```

Wrong:

```objc
@interface Document {
    Frame *frame;
}
- (NSURL *)url;
- (void)setURL:(NSURL *)url;
- (NSString *)title;
@end
```

2. Declare private and implementation-specific variables in an anonymous category in the implementation file or instead of declaring in the class definition.

Right:

```objc
// Document.h
@interface Document
@property (nonatomic) NSURL *url;
@end

// Document.m
@interface Document ()
@property (nonatomic) Frame *frame;
@end
```

Wrong:

```objc
// Document.h
@interface Document {
@private
    Frame *frame;
}
@end
```

### Blocks

#### Using to scope

Use auto_type for the type of the left-hand-side expression.

Right:
```objc
__auto_type checkState = ^(NSString *aString) {
  ...
  return YES;
};
```

Wrong:
```objc
BOOL (^checkState)(NSString *) = ^BOOL(NSString *aString) {
  ...
  return YES;
};
```



#### Passing as an argument

Right:

```objc
typedef CheckStateFunctionType = BOOL (^checkState)(NSString *)
```

### Naming

#### Functions only for testing purposes

Sometimes to write a test a part of the implementation needs to be exposed (i.e. encapsulation must be broken). Using these functions outside of testing can make it more difficult in the future to change the implementation of a class because callers may intentionally or unintentionally become dependent on such knowledge. The right way to add a testing-only function is to:

1. Append "ForTesting" to the end of its name.
   This makes it easy for other developers, code reviewers and even automated tools to spot (by name alone) usage of it outside of testing.

2. Declare it an _Internal.h or _Private.h file or some other testing-only header.

   This way it is not exposed in any public API headers for embedding clients to become dependent on.

Right:

```objc
- (NSData *)bufferByNameForTesting:(NSString *)name;
- (void)setSlowScrollingEnabledForTesting:(BOOL)enabled;
```

Wrong:

```objc
- (NSData *)bufferByName:(NSString *)name;
- (NSData *)bufferForTestingByName:(NSString *)name;
- (void)_setSlowScrollingEnabled:(BOOL)enabled;
```
