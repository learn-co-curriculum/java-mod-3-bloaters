# Bloaters

## Learning Goals

- Learning Goal 1

## Introduction

Bloaters refer to code that have become too large to work with. They aren't
usually apparent right away but they come up when trying to add features or
refactor code.

## Bloater Sub-Patterns

### Long/complex method

A method should only be responsible for a narrow set of functionality. A good
way to monitor whether that's the case is the number of unit tests you must
write for a single method. If there are 10s and 10s of tests for a single
method, it's more than likely that that method is trying to implement too much
functionality by itself.

### Large class

A single class should only be responsible for functionality that is relevant to
the entity it's trying to model. We ran into this in a previous section when we
modeled a hotel room and had to decide whether to put the functionality to send
a welcome email in the hotel room class or not.

We decided to implement the Observer pattern because the hotel room should not
have to worry about all the other systems in the hotel that may be interested in
things that happen to the room. These other systems should have ways to find out
what happened without the room needing to tell them directly and explicitly. Had
we not done that, the `HotelRoom` class would have started to grow very quickly
as the rest of the system grew in size and complexity.

### Primitive obsession

Primitives are useful data types, but their ability to represent complex data
concepts is very limited. For example, they can't handle calculations.

Consider a method that receives 3 ints and double where the ints represent a
person's `birthYear`, `birthMonthIndex` and `birthDay`, and the double
represents the person's `ageInDecimalYears`.

How would you go about passing in the `ageInDecimalYears`? You'd have to
calculate it at the moment that you're trying to call the method that needs it:

```java
double ageInDecimalYears = calculateAge(birthYear, birthMonthIndex, birthDay);
doSomethingWithPerson(birthYear, birthMonthIndex, birthDay, ageInDecimalyears);
```

The probme is that because all these primitives aren't captured together in a
single data structure, you will have to calculate the person's age every time
you need to do something with it (a person's age changes with time, so I can't
even rely on calculating this value once and using it over and over again), and
anyone trying to understand this code will have to understand the relationship
between these raw values.

Instead, you should have a small `Person` class that models the person entity
and is responsible for calculating the person's age:

```java
public class Person {
    int birthYear;
    int birthMonthIndex;
    int birthDay;

    public double getAge() {
        return calculateAge(birthYear, birthMonthIndex, birthDay);
    }

    // accessor methods
}
```

Now the logic to calculate a person's age is the responsibility of the person,
and all the properties that you use to understand what a person is in your
system are tracked together in this class.

### Data clumps

Data clumps are basically an extension of primitive obsessions in that they
identify primitives that always travel through a system together, indicating
that they have some association to each other that isn't properly captured by
the way we modeled our system.

The problem with data clumps, in addition to the same issue as with primitive
obsessions, is that they do not allow business rules to be associated with these
data elements. In our example of a fields to represent a person, we would have
no way to restrict the person's `birthYear` to something that happens in the
past, or even to a positive integer for that matter

### Long parameter list

This is the natural consequence of having poorly modeled data, whether with too
many primitives, data clumps or even a large number of complex data types (i.e.
classes). Having long parameter lists is usually a sign that either a method is
responsible for too many things, or that the data passed into that method should
be modeled differently.
