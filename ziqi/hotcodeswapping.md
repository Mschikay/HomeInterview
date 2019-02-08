# importlib.reload(module) in Python

Reload a previously imported module. The argument must be a module object, so it must have been successfully imported before. This is useful if you have edited the module source file using an external editor and want to try out the new version without leaving the Python interpreter. The return value is the module object (which can be different if re-importing causes a different object to be placed in sys.modules).

#### When reload() is executed:

Python module’s code is recompiled and the module-level code re-executed, defining a new set of objects which are bound to names in the module’s dictionary by reusing the loader which originally loaded the module. The init function of extension modules is not called a second time.

As with all other objects in Python the old objects are only reclaimed after their reference counts drop to zero.


The names in the module namespace are updated to point to any new or changed objects.

#### Other references to the old objects (such as names external to the module) are not rebound to refer to the new objects and must be updated in each namespace where they occur if that is desired.
也就是说，在模块外引用这个模块里被更新的对象，并且有自己的命名空间的变量，不会因为reload指向新的被更新的对象。需要在它的命名空间里更新才可以。（所以旧引用的那片空间还在？）

#### There are a number of other caveats:

When a module is reloaded, its dictionary (containing the module’s global variables) is retained. Redefinitions of names will override the old definitions, so this is generally not a problem. 
#### If the new version of a module does not define a name that was defined by the old version, the old definition remains. This feature can be used to the module’s advantage if it maintains a global table or cache of objects — with a try statement it can test for the table’s presence and skip its initialization if desired:
```python
try:
    cache
except NameError:
    cache = {}
```

#### It is generally not very useful to reload built-in or dynamically loaded modules. Reloading sys, __main__, builtins and other key modules is not recommended. 

#### In many cases extension modules are not designed to be initialized more than once, and may fail in arbitrary ways when reloaded.

#### If a module imports objects from another module using from … import …, calling reload() for the other module does not redefine the objects imported from it — 
one way around this is to re-execute the from statement, another is to use import and qualified names (module.name) instead.

If a module instantiates instances of a class, reloading the module that defines the class does not affect the method definitions of the instances — they continue to use the old class definition. The same is true for derived classes.

New in version 3.4.

Changed in version 3.7: ModuleNotFoundError is raised when the module being reloaded lacks a ModuleSpec.

