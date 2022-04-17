---
title: Changing static final fields in Java for JUnit Unit Tests
author: admin
layout: post
permalink: /2015/12/static-final-java-junit-test/
comments: true
categories:
  - Java
  - Reflection
  - JUnit
tags:
  - java
  - unit test
  - junit
  - reflection
  - static
  - final
---
## Reflection is a helluva drug

`Reflection` allows you to tinker with Java in ways that are clearly against everything you've learnt from your Object Oriented Programming lessons.

Reflection is like being handed a master-key. I've used it to come up with solutions to problems that didn't have an easier or cleaner way in Java - cutting down cumbersome code writing.

Minor disclaimer:
**Don't do this! You might open Pandora's Box.**

If you still want to go ahead, read on.

Recently for a project we needed a way to test some code which depended on a class which had `public static final` constants that are picked up from a property file at runtime. The problem was that there was no way to unload the class and let Java reload the `static final` fields. I checked up about writing my own `classloader` but it seemed like overkill. All I wanted was to be able to change the values of the `static final` fields to run more tests, without having to worry about changing the code too much.

The solution came to me in the form of Reflection. Using Reflection, you can read values of fields that are `private` or even `final`, and *write* back into them! (Lesser demons scream in my head...)

So, without further ado, the unethical way to edit `static final` fields in Java that makes people burned by Reflection wince, is as follows:

### First, we get the `field` to tinker with:
 
    Field field = clazz.getDeclaredField( fieldName );

Somehow I wasn't able to use the `getField()` method for this, and had to use the `getDeclaredField()` method instead.

### Next we modify the `field` using reflection to allow editing `final` fields (Sort of like self-immolation): 
    Field modifiersField = Field.class.getDeclaredField( "modifiers" );
    boolean isModifierAccessible = modifiersField.isAccessible();
    modifiersField.setAccessible( true );
    modifiersField.setInt( field, field.getModifiers() & ~Modifier.FINAL );

### Now we allow the field to be edited by setting the `accessible` field to true:
    boolean isAccessible = field.isAccessible();
    field.setAccessible( true );

### Now do the deed. Set the value to the `null` object to affect the `static` member:
    field.set( null, value );

### Clean up by changing the `accessible` field back:
    field.setAccessible( isAccessible );
    modifiersField.setAccessible( isModifierAccessible );
Might not be very useful resetting the value, really. The harm is already done.

**Note:** Compilers optimize constants by replacing them inline, which will make changing the constant useless. For example, assume `changeField` is our method which changes the `static final` field `TEST` of Class `clazz`:

    public static final String TEST = "Hello";

    public static void main( String[] args )
    {
        System.out.println( TEST ); // prints "Hello"
        changeField( clazz, "TEST", "hi!" );
        System.out.println( TEST ); // prints "Hello"
    }

This is because during compilation, your compiled decided to replace `System.out.println( TEST );` with `System.out.println( "Hello" );`

You can overcome this behavior by setting the value of the `static final` field using a method such as:

    public static final String TEST = PropertyFileReader.getProperty("TEST");

This prevents the compiler from optimizing the code, allowing you to tinker with it using Reflection.

## Statiflex:
If you don't want to do it yourself, and instead tell people you "found a library on the Internet that does it", here's a link to my repository containing a jar that will let you do the same: [Statiflex](https://github.com/caffinc/statiflex/releases/tag/1.0 "Statiflex")

You can add it as a Maven dependency like this:

    <dependency>
        <groupId>com.caffinc</groupId>
        <artifactId>statiflex</artifactId>
        <version>1.0</version>
        <scope>system</scope>
        <systemPath>${project.basedir}/src/main/resources/jars/statiflex-1.0.jar</systemPath>
    </dependency>
`Statiflex` depends on `SLF4J` with `Log4j`:

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.12</version>
    </dependency>

### Usage:

    boolean success = Statiflex.flex(MyClass.class, "MY_STATIC_FINAL_FIELD", "NEW VALUE");

Feel free to tinker with my code and use it in your projects. Don't blame me if you end up with issues :) Happy hacking!