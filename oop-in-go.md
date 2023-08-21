# OOP in Go

{% hint style="warning" %}
Go does not support object-oriented programming (OOP) features like inheritance. Try focusing on interfaces and simple composition rather than inheritance hierarchies.
{% endhint %}

Go takes a lightweight approach to code reuse through interfaces and composition rather than heavy OOP abstraction.

* Go does not have classes. Instead, it uses structs and interfaces. Structs can group data and behavior together similar to classes but without the formal notion of inheritance.
* There is no concept of subclassing in Go. Structs cannot inherit from other structs. Go relies on composition to reuse code.
* Interfaces can define method signatures that concrete types can implement, providing polymorphic behavior. But there is no subclassing of interfaces.
* No access modifiers like private or protected in Go structs. Composition is preferred for encapsulation.

{% hint style="info" %}
In Go, casing conventions indicate intended encapsulation - capitalized names export across package boundaries while lowercase names suggest internal-only usage.
{% endhint %}
