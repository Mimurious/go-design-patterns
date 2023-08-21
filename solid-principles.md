---
description: A quick summary on the SOLID principles
---

# SOLID Principles

<figure><img src=".gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
_The SOLID principles_ and design patterns complement each other, with SOLID offering guidance on good design.

**However, SOLID is considered a set of principles rather than a design pattern itself.**
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=TMuno5RZNeE" %}
In this talk Uncle Bob provides an introduction and overview of the **SOLID** principles of software design.
{% endembed %}

***

## S: Single Responsibility Principle

_The Single Responsibility Principle_ states that each struct and component in a program should have **only one reason to change**. This means every struct and component should have a single clear purpose and set of responsibilities within the overall system design.

* Helps make code more modular and reusable - structs and components with focused responsibilities can more easily be reused.
* Promotes separation of concerns - different structs and components handle different tasks/concerns.
* Reduces side effects of change - modifying one struct or component doesn't impact the overall system.
* Improves testability - simpler structs and components with narrowly defined responsibilities are easier to test.

For example, a component, either a separate package or type that handles authentication, logging and email sending violates _the Single Responsibility Principle_. These responsibilities should be split into separate structs and components for authentication, logging, and emailing, allowing change to one without impacting the others.

{% hint style="danger" %}
**ANTI-PATTERN HINT:** Be wary of creating [God Structs or God Components](anti-patterns/god-struct-component.md)
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

This User struct violates _The Single Responsibility Principle_ by handling authentication (Login), logging (LogAccess), and sending emails (SendEmail).

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

Here the responsibilities are separated into Auth, Logger, and Email structs and components, each focused on a single purpose. This follows the Single Responsibility Principle.

***

## O: Open/Closed Principle

_The Open/Closed Principle_ states that structs, components (packages), and functions should be **open for extension but closed for modification**.

* Open to extension - New behavior can be added by creating new code, not by changing existing code.
* Closed to modification - Existing code should not be modified to add new functionality.

Adhering to this principle provides stability and ensures that changes do not introduce bugs.

#### Some ways to follow the Open/Closed Principle

* Define and depend on interfaces - Concrete implementations can change without modification.
* Use composite structs - Embed existing structs and extend behavior.
* Employ polymorphism via interfaces - Override interface methods to provide new behavior.
* Parameterize packages - Accept behaviors/algorithms as params rather than embedding.

Following the Open/Closed Principle results in more maintainable and extensible code. New requirements can be addressed by adding new code in new packages, files, and structs, rather than changing existing code. This reduces bugs and ripple effects.

{% hint style="info" %}
Take a look at [_The Specification pattern_](enterprise-patterns/specification.md)_._ This pattern encapsulates business rules or criteria into reusable query structs.
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

This violates open/closed. To add a new animal we have to modify the existing code.

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

Here the Zoo and Animal structs are closed to modification but open for extension via new structs implementing NoiseMaker.

***

## L: Liskov Substitution Principle

> Subclasses should be substitutable for their base classes.

## I: Interface Segregation Principle

> Many client-specific interfaces are better than one general-purpose interface.

## D: Dependency Inversion Principle

> Depend on abstractions rather than concretions.

***

{% embed url="https://www.youtube.com/watch?v=AKdvlr-RzEA" %}
SOLID in Go in 8 Minutes by Steve Hook
{% endembed %}

## See Also

* [SOLID Principle Golang playlist by Tennexas: Tech with Satyajit Das on YouTube](https://www.youtube.com/playlist?list=PLBZT\_qt7qHRRm8OaFG38JRaL\_b8MmEwmx)
