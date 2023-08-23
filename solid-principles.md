---
description: An overview of the SOLID principles
---

# SOLID Principles

<figure><img src=".gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
Design patterns and the SOLID principles work in harmony, with SOLID providing advice on excellent design.

**However, SOLID isn't actually a design pattern; rather, it's a set of guiding principles.**
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=TMuno5RZNeE" %}
In this talk Uncle Bob provides an introduction and overview of the **SOLID** principles of software design.
{% endembed %}

***

## S: Single Responsibility Principle

_The Single Responsibility Principle_ states that each struct and component in a program should have **only one reason to change**. This means that, within the overall system design, each struct and component should have a single, distinct purpose and set of duties.

* **Helps make code more modular and reusable** - Structs and components with clear roles can be reused more readily.
* **Promotes separation of concerns** - Different structs and components handle different tasks/concerns.
* **Reduces side effects of change** - Modifying one struct or component doesn't impact the overall system.
* **Improves testability** - Simpler structs and components with narrowly defined responsibilities are easier to test.

For example, having a component that manages email sending, logging, and authentication **is a violation of **_**the Single Responsibility Principle**_. To enable changes to one without affecting the others, these responsibilities for authentication, logging, and emailing **should be divided into different structs and components**.

{% hint style="danger" %}
**ANTI-PATTERN HINT:** Be wary of creating [God Structs or God Components](anti-patterns/god-struct-component.md)!
{% endhint %}

#### Bad Example

```go
// user.go

type User struct {
  //...
}

func (u *User) Login() {
  //... 
} 

func (u *User) LogAccess() {
  //...
}

func (u *User) SendEmail(text string) {
  //...
}
```

By managing authentication (`Login`), logging (`LogAccess`), and email sending (`SendEmail`), **this User struct transgresses **_**the Single Responsibility Principle**_**.**

#### Good Example

```go
// auth.go

type Auth struct {
  //...
}

func (a *Auth) Login(email, password string) {
  //...
}

// logger.go 

type Logger struct {
  //...
}

func (l *Logger) LogAccess(email string) {
  //...  
}

// email.go

type Email struct {
  //...
}

func (e *Email) Send(to, text string) {
  //...
}
```

Here the responsibilities are divided into `Auth`, `Logger`, and `Email` structs and components, each focused on a single purpose. **This follows **_**the Single Responsibility Principle**_**.**

***

## O: Open/Closed Principle

_The Open/Closed Principle_ states that structs, components (packages), and functions should be **open for extension but closed for modification**.

* **Open to extension** - New behavior can be added by creating new code, not by changing existing code.
* **Closed to modification** - Existing code should not be modified to add new functionality.

#### Some ways to follow the Open/Closed Principle

* **Define and depend on interfaces** - Concrete implementations can change without modification.
* **Use composite structs** - Embed existing structs and extend behavior.
* **Employ polymorphism via interfaces** - Override interface methods to provide new behavior.
* **Parameterize packages** - Accept behaviors/algorithms as params rather than embedding.

Following _the Open/Closed Principle_ results in more maintainable and extensible code. New requirements can be addressed by adding new code in new packages, files, and structs, rather than changing existing code. This reduces bugs and ripple effects.

{% hint style="info" %}
Take a look at [_the Specification pattern_](enterprise-patterns/specification.md)_._ This pattern encapsulates business rules or criteria into reusable query structs.
{% endhint %}

The interfaces, composition, and polymorphism feature directly support writing code that adheres to the Open/Closed Principle.

#### Bad Example

```go
type Zoo struct {
  animals []Animal
}

func (z *Zoo) MakeNoise() {
  // loop through animals and call noise
  // method based on animal type
}

func (a *Animal) MakeNoise() {
  // animal noise implementation
}

// To add a new animal:
// 1. Modify Zoo.MakeNoise to handle new animal
// 2. Modify Animal struct to add a new field
```

**This violates open/closed**. To add a new `animal` we have to modify the existing code.

#### Good Example

```go
type NoiseMaker interface {
   MakeNoise() 
}

type Zoo struct {
  animals []NoiseMaker 
}

func (z *Zoo) MakeNoise() {
  // loop through NoiseMakers
  for _, a := range z.animals {
    a.MakeNoise()
  }
}


type Lion struct {}

func (l *Lion) MakeNoise() {
  // lion noise 
}

// To add a new animal:
// 1. Create a new struct with the MakeNoise method
// 2. No existing code needs modification
```

Here the `Zoo` and `Animal` structs are closed to modification but open for extension via new structs implementing `NoiseMaker`.

***

## L: Liskov Substitution Principle

{% hint style="warning" %}
_**The Liskov Substitution Principle**_** is less directly applicable in Go** since Go does not have an inheritance in the traditional OOP sense.
{% endhint %}

_The Liskov Substitution Principle_ states that **structs should be replaceable by their subtypes without altering the correctness of the program**. This means:

* If S is a subtype of T, then objects of type T should be replaceable with objects of type S.
* The subtype must meet the contract defined by the base type.

While Go does not have inheritance, **the general idea behind **_**the Liskov Substitution Principle**_** can still apply**. It states that structs implementing interfaces should be interchangeable without introducing errors or unexpected behavior. These are some ways this principle may apply:

* Subtypes embedding parent structs should maintain consistent contracts.
* Structs implementing interfaces should faithfully satisfy the interface contracts.
* Be careful when overriding methods via embedded fields to avoid unexpected behavior.
* Favor using lightweight interfaces over complex type hierarchies.

Following _the Liskov Substitution Principle_ can still help to **prevent brittle relationships and create resilient interfaces**. The focus shifts from strict hierarchies to judgments about interface compatibility and contracts.

#### Bad Example

```go
type Vehicle struct {
  wheels int 
}

func (v *Vehicle) NumWheels() int {
  return v.wheels
}

type Motorcycle struct {
  Vehicle // Embedded 
}

func (m *Motorcycle) NumWheels() int {
  // Bug! Returns 2 instead of the value from Vehicle
}
```

This violates _the Liskov Substitution Principle_ - `Motorcycle` overrides `NumWheels()` incorrectly.

#### Good Example

```go
type Vehicle interface {
  NumWheels() int
}

type Car struct {
  wheels int
}

func (c *Car) NumWheels() int {
  return c.wheels 
}

type Bike struct {
  wheels int
}

func (b *Bike) NumWheels() int {
  return b.wheels
}
```

Here `Vehicle` is an interface. `Car` and `Bike` implement the interface reliably according to their specific wheel number. The subtypes meet the contracts defined by `Vehicle`.

## I: Interface Segregation Principle

_The Interface Segregation Principle_ states that **interfaces should be small and focused rather than large and generic**. This means:

* Interfaces should be specific to a precise functionality rather than broad.
* Structs should not be forced to implement interfaces they don't use.
* Many smaller interfaces are preferable to one "do-it-all" interface.

Some ways to follow _the Interface Segregation Principle_:

* **Split large interfaces into smaller ones** based on functionality.
* **Structs should only implement interfaces that are relevant** to their purpose.
* **Keep interfaces minimal** - Don't define more methods than necessary.
* **Refactor interfaces** that require methods not used by all implementations.

{% hint style="info" %}
Take a look at [_the Decorator pattern_](structural-patterns/decorator.md)_._ This pattern demonstrates _the Interface Segregation Principle_ by using small, focused interfaces which avoids bulky interfaces forcing many unneeded methods.
{% endhint %}

_The Interface Segregation Principle_ makes interfaces and structs leaner because **they only need to implement behavior that is absolutely necessary for them**. This encourages decoupled code that is targeted at particular capabilities.

#### Bad Example

```go
type Shape interface {
  Area() float64
  Volume() float64 // not applicable to all shapes
}

type Circle struct {}

func (c Circle) Area() float64 {
  // implement circle area 
}

func (c Circle) Volume() float64 {
  // circle doesn't have volume
}
```

`Circle` is forced to implement `Volume()` even though it doesn't apply.

#### Good Example

```go
type AreaCalculator interface {
  Area() float64
}

type VolumeCalculator interface {
  Volume() float64
}

type Circle struct {}

func (c Circle) Area() float64 {
  // implement circle area
}

type Cube struct {} 

func (cu Cube) Area() float64 {
  // implement cube area 
}

func (cu Cube) Volume() float64 {
  // implement cube volume
}
```

`AreaCalculator` and `VolumeCalculator` are separate interfaces. `Circle` and `Cube` implement only what's applicable.

## D: Dependency Inversion Principle

_The Dependency Inversion Principle_ states that **higher-level modules should not depend directly on lower-level modules** - both should depend on abstractions. This means:

* High-level components should be unaware of low-level implementation details.
* Dependencies should be abstracted through interfaces, function arguments, etc.
* Concrete types should be passed in or created at a low level.

Some ways to follow _the Dependency Inversion Principle_:

* **Define interfaces for dependencies** rather than using concrete types directly.
* **Accept interface parameters** rather than concrete types in functions/structs.
* **Initialize dependencies by passing them in** rather than instantiating them inside higher-level code.
* **Have low-level components return interfaces** rather than concrete types.

Following _the Dependency Inversion Principle_ results in loosely coupled code that is more flexible, testable, and extendable over time as requirements change.

In Go, the language facilities for interfaces and function composition directly support writing code that adheres to _the Dependency Inversion Principle_.

#### Bad Example

```go
type Auth struct {
  db Database // concrete dependence 
}

func (a *Auth) Login() {
  // Auth directly depends on concrete Database
  a.db.Query("SELECT * FROM users")
}

type Database struct {
  //...
}
```

`Auth` is tightly coupled to the concrete `Database` type.

#### Good Example

```go
type DB interface {
  Query(string)
}

type Auth struct {
  db DB // interface dependence
}

func (a *Auth) Login() {
  // Auth depends on abstract DB  
  a.db.Query("SELECT * FROM users") 
}

type Database struct {
  //...
}

func (d *Database) Query(query string) {
  //...
}
```

Here `Auth` depends on the abstract `DB` interface rather than the concrete `Database`. This decouples the code and follows _The Dependency Inversion Principle_.

***

## See Also

{% embed url="https://www.youtube.com/watch?v=AKdvlr-RzEA" %}
SOLID in Go in 8 Minutes by Steve Hook
{% endembed %}

* [SOLID Principle Golang playlist by Tennexas: Tech with Satyajit Das on YouTube](https://www.youtube.com/playlist?list=PLBZT\_qt7qHRRm8OaFG38JRaL\_b8MmEwmx)
