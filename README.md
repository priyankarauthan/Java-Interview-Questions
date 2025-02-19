# Java-Interview-Questions

## When a class implements two interfaces that have the same default method, Java does not know which method to use, and it results in a compilation error unless the class provides its own implementation.


Java
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
}
Compilation Error:
Duplicate default methods named show with the parameters () are inherited from the types A and B.

How to Resolve This Conflict? To resolve the ambiguity, we must override the conflicting method in MyClass and explicitly specify which interface's method to call using InterfaceName.super.methodName().

Java
class MyClass implements A, B {
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
