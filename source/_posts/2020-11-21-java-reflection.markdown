---
layout: post
title: "Java Reflection"
date: 2020-11-21 16:25:03 +0800
comments: true
categories: Java
---

## Java Reflection - Private Fields and Methods

> Note: This only works when running the code as a standalone Java application, like you do with unit tests and regular applications.
> if you try to do this inside a Java Applet, you will need to fiddle around with `SecurityManager`. 


### Accessing Private Fields
To access a private field you will need to call `Class.getDeclaredFiled(String name)` method

The methods `Class.getField(String name)` only return public fields.
#### Example
```java
import java.lang.reflect.Field;

class PrivateObject {
    private String privateString = null;
    public PrivateObject(String privateString) {
        this.privateString = privateString;
    }
}
public class test {
    public static void main(String[] args) throws NoSuchFieldException {
        PrivateObject privateObject = new PrivateObject("The Private Value");
        Field privateStringField = privateObject.getClass().getDeclaredField("privateString");
        privateStringField.setAccessible(true);
        try {
            String oldFieldValue = (String) privateStringField.get(privateObject);
            System.out.println("fieldValue: " + oldFieldValue);
            String newFieldValue = oldFieldValue.replace("The", "That");
            privateStringField.set(privateObject, newFieldValue);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
```

**PS**:
	by calling `Field.setAccessible(true)` just turn off the access checks for this particular Field instance, for reflection only. Now you can access it even if it is **private/protected** or **package scope**. even if `the caller is not part of those scopes` But you still can't access the field using normal code which would be disallowed by compiler.

### Accessing Private Methods

To access a private method you will need to call the ` Class.getDeclaredMethod(String name, Class[] parameterTypes)`

The methods `Class.getMethod(String name, Class[] parameterTypes)`  only return public methods

#### Example
```java
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

class PrivateObject {
    private String privateString = null;
    public PrivateObject(String privateString) {
        this.privateString = privateString;
    }

    private String getPrivateString() {
        return this.privateString;
    }
}
public class test {
    public static void main(String[] args) throws NoSuchMethodException {
        PrivateObject privateObject = new PrivateObject("The Private Value");

        try {
            Method privateStringMethod = PrivateObject.class.getDeclaredMethod("getPrivateString", null);
            privateStringMethod.setAccessible(true);
            String returnValue = (String) privateStringMethod.invoke(privateObject, null);

            System.out.println("returnValue = " + returnValue);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
    }
}
```
