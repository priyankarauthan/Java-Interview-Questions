# Java-Interview-Questions

## When a class implements two interfaces that have the same default method, Java does not know which method to use, and it results in a compilation error unless the class provides its own implementation.


# Java-Interview-Questions

## When a class implements two interfaces that have the same default method, Java does not know which method to use, and it results in a compilation error unless the class provides its own implementation.

Example Scenario

```java
interface A {
    default void show() {
        System.out.println("A's show method");
    }
}

interface B {
    default void show() {
        System.out.println("B's show method");
    }
}

class MyClass implements A, B {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.show();  // Compilation Error if not overridden
    }
}```
Compilation Error:
Duplicate default methods named show with the parameters () are inherited from the types A and B.

How to Resolve This Conflict? To resolve the ambiguity, we must override the conflicting method in MyClass and explicitly specify which interface's method to call using InterfaceName.super.methodName().

```class MyClass implements A, B {
    @Override
    public void show() {
        // Explicitly calling interface method
        A.super.show();  // Calls A's default method
        // B.super.show(); // If we want to call B's method instead
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.show();  // Prints: A's show method
    }
}
```
## @Transactional Annotation in Spring Boot

The @Transactional annotation in Spring Boot is used to manage database transactions. It ensures that a series of database operations are executed as a single unit of work—either all succeed or all fail (rollback in case of an error).

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void someMethod() {
    // Your code here
}
```

## Garbage Collection (GC) in Java
Garbage Collection (GC) in Java is an automatic memory management process that removes unused objects from heap memory to free up space and avoid memory leaks. It is handled by the JVM (Java Virtual Machine) and does not require explicit deallocation of objects.

## How Does Garbage Collection Work?
Objects are allocated in heap memory when created using new.
Garbage Collector identifies unreachable objects (objects with no active references).
GC removes those objects and reclaims memory.
Memory is compacted and optimized to reduce fragmentation.
When Does Garbage Collection Happen?
The JVM runs GC automatically when it detects low memory.
You can suggest GC using System.gc(), but it is not guaranteed to run immediately.
GC primarily occurs during idle CPU time to minimize performance impact.

