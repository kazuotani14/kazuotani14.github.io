### Reference links

* [Variadic templates](http://eli.thegreenplace.net/2014/variadic-templates-in-c/)
    * Recursion, except for templated functions!
* [callbacks](https://stackoverflow.com/questions/2298242/callback-functions-in-c)
* Size of object
    - <https://stackoverflow.com/questions/937773/how-do-you-determine-the-size-of-an-object-in-c>
    - 1 byte if empty
    - Size of non-static data members if not empty
    - Add size of pointer for virtual table
        - 32 bit: 4 bytes (8 bits in 1 byte)
        - 64 bit: 8 bytes
    - Derived classes include size of base class
    - Compiler/platform-specific alignment (e.g. chars, bools)
    - Non-virtual member functions add no space (since they're shared by all objects and stored in one place)
* Class: “user-defined type”
* For class that does dynamic allocation internally, 3 things that need to be defined: constructor, destructor, assignment operator 

* [Optimization strategies and techniques](http://www.tantalon.com/pete/cppopt/main.htm)
  * "optimization procrastination" - only do it when you have to, and profile first
  * const everything
  * pass by Reference
  * return value optimization (return constructor arguments instead of an object)
    * might be in c++ standard/compilers already (don't have to do it manually)
  * inlining for small functions
  * default constructors


### CMake

* [Optional arguments for macros](https://gist.github.com/dberzano/8194ddfc9b8fbd5529ae)

---

> We have tried to demonstrate by these examples that it is almost always incorrect to begin the decomposition of a system into modules on the basis of a flowchart. We propose instead that one begins with a list of difficult design decisions or design decisions which are likely to change. Each module is then designed to hide such a decision from the others. Since, in most cases, design decisions transcend time of execution, modules will not correspond to steps in the processing…
