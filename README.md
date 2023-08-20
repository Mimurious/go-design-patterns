---
description: >-
  This gitbook provides an in-depth look at implementing common software design
  patterns in Go.
---

# Go Design Patterns

<figure><img src=".gitbook/assets/pngegg.png" alt="" width="375"><figcaption></figcaption></figure>

Go takes a lightweight approach to code reuse through interfaces and composition rather than heavy OOP abstraction.

* Go does not have classes. Instead it uses structs and interfaces. Structs can group data and behavior together similar to classes, but without formal notion of inheritance.
* There is no concept of subclassing in Go. Structs cannot inherit from other structs. Go relies on composition to reuse code.
* Interfaces can define method signatures that concrete types can implement, providing polymorphic behavior. But there is no subclassing of interfaces.
* No access modifiers like private or protected in Go structs. Composition is preferred for encapsulation.

{% hint style="info" %}
In Go, casing conventions indicate intended encapsulation - capitalized names export across package boundaries while lowercase names suggest internal-only usage.
{% endhint %}

