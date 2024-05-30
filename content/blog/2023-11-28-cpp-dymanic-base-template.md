+++
title = "The C++ Dynamic Base Boilerplate"
updated = 2023-11-30
[taxonomies]
tags = ["programming","c++"]
+++

After years of using C++, I changed my snippet for the very common case of a dynamic base
class that I use define _interfaces_, that is, to provide handles to an object as well as
access to methods on that objects while still hiding the class layout and method
implementations, using C++'s _virtual method dispatch_.

<!-- more -->

As you probably know, there is not a specific concept for these interfaces like in Java or
C#, the regular dynamic dispatch is done through inheritance.
Until recently, I had a snippet to define such an interface base class that looked just
like this:

```c++
class ISnippet
{
  public:
    virtual ~ISnippet() = default;
};
```

There is a virtual and defaulted destructor so that owning references to objects of
derived classes can be handed out and we can start adding some pure virtual functions.
A virtual destructor is recommended as soon as there are any virtual functions, and we
could make it pure virtual if there are no other methods to be defined on our interface so
as to still have the interface class abstract.

There is a problem though: A base class like this violates the _rule of five (and a
half)_, it implements a destructor but none of the copy or move operations. Practically,
two things are sub-optimal here:
1. Users could attempt to copy _out of_ references of the interface type _onto_ another
   instance. The copy constructor and the copy assignment operator are implicitly generated
   and public. They won't do anything at all (a bleak form of _object slicing_, actually), hence
   any invocation of them will not do what one would _expect_.
2. The presence of the user-defined destructor prevents the move operations from being
   automatically generated. This is no big deal as it does not affect the automatic member
   generation in derived classes and the class itself has nothing to be moved or copied.
   This can at most become an issue when the class at some point gets converted from an
   abstract interface class with no member fields to a base class with data members.
3. Tooling flags up this rule violation. For example, the clang-tidy check
   _cppcoreguidelines-special-member-functions_ will light up in developer's editors
   (interestingly, this has an option to allow _defaulted_ destructors to appear without
   the other special member functions).

Especially because it is not unthinkable (albeit unlikely) that someone sometime invokes a
copy operation through references of the interface type, and to avoid syncing linter
settings with everyone who will ever look at my code, I changed my snippet to this:

```c++
class ISnippet
{
  public:
   ISnippet() = default;
   virtual ~ISnippet() = default;

  protected:
   ISnippet(const ISnippet &) = default;
   ISnippet(ISnippet &&) noexcept = default;
   ISnippet &operator=(const ISnippet &) = default;
   ISnippet &operator=(ISnippet &&) noexcept = default;
};
```

[This stackoverflow question](https://stackoverflow.com/questions/49961811/must-a-c-interface-obey-the-rule-of-five)
is what made it clear for me. In short:
With this, attempts to move or copy through references will not compile, yet, derived
classes may still define move and copy operations for code that knows the specific types
(and hopefully makes sure any references that were handed out stay valid).

It fascinates me, that I would still change a snippet for such a common boilerplate purpose.

### See also
  - Scott Meyers, "Effective Modern C++", Item 17, "Understand special member function generation."
  - [The rule of three/five/zero](https://en.cppreference.com/w/cpp/language/rule_of_three)  on cppreference.com

</br>

_Edit: I forgot to add the explicit default constructor in the second listing, which is
not automatically generated in the presence the of user-defined copy and move construtors_
