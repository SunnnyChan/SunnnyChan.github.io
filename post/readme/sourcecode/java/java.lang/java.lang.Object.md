# java.lang.Object

```java
public class Object {}
```

* private static native void registerNatives();
```java
    static {
        registerNatives();
    }
```

* public native int hashCode();

* public boolean equals(Object obj) {}
```java
public boolean equals(Object obj) {
        return (this == obj);
}
```