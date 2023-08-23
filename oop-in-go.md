# OOP in Go

{% hint style="warning" %}
The OOP (object-oriented programming) characteristic of inheritance is not supported by Go. Instead of concentrating on inheritance hierarchies, try focusing on interfaces and straightforward composition.
{% endhint %}

Go uses **composition and interfaces** to reuse code more efficiently than it would with complex OOP abstraction.

* **There are no classes in Go.** It substitutes structs and interfaces instead. Similar to classes, structs can organize data and behavior together, but without the formal concept of inheritance.
* **Subclassing is not a concept in Go.** Structs are not permitted to inherit from other structs. Go uses composition to allow for code reuse.
* **Polymorphic behavior is provided by interfaces,** which can define method signatures that concrete types can implement. However, interface subclassing does not exist.
* **Go structs don't have access modifiers like private or protected.** Encapsulation is best achieved through composition.

{% hint style="info" %}
Capitalized names export across package boundaries but lowercase names imply internal-only usage in Go, where casing standards signal intended encapsulation.
{% endhint %}
