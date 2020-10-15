# Guide to picking C++ argument type: Pass by value? ref? const ref? rvalue ref? pointer? smart pointer? optional?

Here's my guide for how to pick the C++ argument type to use.

## General thoughts

"Defend against accidental misuse, not intentional abuse." (Me)

The type tends to be dependent on the following things, in order:

1. The size of the type + whether constructing the type has any side effects (e.g. incrementing a ref count, opening a file, etc etc).

2. The ownership semantics for the concept the function represents.

3. Whether the function does mutate- or could can be made more efficient by mutating- the passed type (aka pass by value).

To simplify this guide I am going to focus only on cases where the argument is an in-argument. Out and in-out arguments are less interesting because syntactically there are not as many considerations to take into account: they can't be non-const and they can only be passed by reference or pointer.

## Here we go

Remember everything below is about in-arguments. Consider this example:

```c++
class A {
public:
  	A(?? B???);
};
```

What should ?? and ??? be? If `B` is a type that can held soley in registers (and its constructor doesn't have side effects – see (1) above) then it should be taken by value because this avoids the cost to dereference a pointer if it were passed any other way. That means all POD types (e.g. `int`, `unsigned`, `char`, etc, etc) should be passed by value. It also means some non-POD types like `std::optional<>,` `std::string_view`, `std::span<>` shoul be passed by value because these types are small – the size of two pointers, which can easily fit in two registers on a modern CPU.

If `B` cannot be held soley in registers then it should it should be passed by reference or pointer and now ownership must be considered:

1. If `A` needs to own the passed `B` or just needs to look at it for the duration of its body then the argument should be `const B&`.
   
2. If `A` should take ownership of `B` then the argument should be `B&&`.
   
3. (Special Case) If `A` will only be used as a stack-allocated RAII-style mechanism for owning `B` for the duration of some scoped block (aka a guard object) and `B` must be on the stack then the argument should be `B&`, **not** `const B&`, because this helps prevent a caller from passing a value that's going to expire. For Example:
   
```c++
   class A {
   public:
     	A(B& b) : m_b { b } {};
   private:
       B& m_b;
   };
   
   void f()
   {
       B b;
       {
           A setForScope { b };
           // do things
       }
       // do more things
   }
```



## Common patterns

### Constructors

These special functions tend to store passed arguments into instance variables. For all such arguments, take them by rvalue reference or value. Why not const lvalue reference? Using that is less efficient as the argument must always be copied (aka deeply copied) even when it could be more efficiently "moved" (aka shallow copied).

### Setters

If the setter turns around and sets an instance variable then take the argument by rvalue reference:

```c++
void A::setB(B&& b)
{
    m_b = std::move(b);
}

// This function is inefficient if callers almost always need to hold a copy too. Read below.
void A::setC(C&& c)
{
    if (c == m_c)
        return;
    m_c = std::move(c);
    notifyCDidChange();
}
```

