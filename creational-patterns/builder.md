# Builder

_**The Builder pattern**_** allows constructing complex objects step-by-step**, separating the construction process from the object's representation.

In Go, it involves:

* **A "builder" interface** with methods to build each piece of the object.
* **Concrete builder implementations** that construct the different object representations.
* **A "director" struct** that calls the builder methods in the necessary order.
* **The "product"** is the complex object being built.

#### Example

For example, building a house involves many steps - laying the foundation, framing the walls, adding roofing, etc. The process is complex but results in a house at the end.

_The Builder pattern_ models this by separating:

* The house-building process (the builder)
* The actual house parts constructed (product)

```go
type Builder interface {
  BuildPartA()
  BuildPartB() 
}

type ConcreteBuilder struct {
  // builds product
}

func (b *ConcreteBuilder) BuildPartA() {
  // build part A
}

func (b *ConcreteBuilder) BuildPartB() {
  // build part B
}

type Director struct {
  builder Builder
}

func (d *Director) Construct() {
  d.builder.BuildPartA()
  d.builder.BuildPartB() 
}

// Product is the complex object  
type Product struct {
  // ...
}
```

The `Director` constructs `Product` using the `Builder` interface. This separates the complex construction from the product representation.

{% hint style="success" %}
**FLUENT METHOD:** You can have builder methods return a pointer to the concrete builder struct (e.g. `return b* ConcreteBuilder`). This allows chaining the build steps like:

```go
builder.BuildPartA().BuildPartB()
```
{% endhint %}

***

## Builder Facets

_Builder facets_ are an extension of _the Builder pattern_ that uses **multiple small builder interfaces to build subsets of an object.**

For example, to build a house you could define separate builder interfaces for:

```go
type FoundationBuilder interface {
  BuildFoundation()
}

type FramingBuilder interface {
  BuildFraming()  
}

type RoofingBuilder interface {
  BuildRoofing()
}
```

Then a main builder composes these facet interfaces:

```go
type HouseBuilder struct {
  foundation FoundationBuilder
  framing FramingBuilder
  roofing RoofingBuilder
}

func (b *HouseBuilder) Build() {
  b.foundation.BuildFoundation()
  b.framing.BuildFraming()
  b.roofing.BuildRoofing()
}
```

`HouseBuilder` coordinates the facet builders to construct the house.

**Benefits of builder facets:**

* Each facet interface is focused on one specific concern.
* Facets are decoupled from each other.
* New facets can be added easily without affecting others.
* Facets can be mixed and matched or reused.

***

## Builder Parameter

To force users to use a builder instead of directly creating objects, hide the object implementation, and only expose the builder publicly.

* Define a builder struct with fluent methods to incrementally construct the object.
* Validate data and enforce invariants in the builder methods.
* Define a function that accepts a function of type.
* Inside the main function, call the passed-in function to initialize the builder and build the object.
* The caller must use the builder to construct the object instead of creating it directly.

This approach enforces the use of the builder and validations, keeping the object encapsulated.

#### Example

The goal is to force construction houses using `HouseBuilder`, not by directly creating `House` objects.

To do this:

* We define a `HouseBuilder` interface with methods like `BuildFoundation()`, `BuildWalls()` etc.
* We implement a `ConcreteBuilder` struct that builds a `House` object incrementally.
* The `House` object itself is hidden from external users.
* We expose a `BuildHouse()` function that accepts a builder function as a parameter.

```go
func BuildHouse(build func(b *ConcreteBuilder)) {
  builder := new(ConcreteBuilder)
  build(builder) // Call builder function
  house := builder.GetHouse()
  // Use house  
}
```

***

## Functional Builder

The key idea is to represent the building steps as pure functions that take in the object being built and return a modified object.

#### Example

```go
type House struct {
  // Fields
}

func BuildFoundation(h House) House {
  // Create foundation, return updated house 
}

func BuildWalls(h House) House {
  // Add walls, return updated house
}

func BuildRoof(h House) House {
  // Add roof, return finished house
}
```

```go
house := House{} // start with empty house

house = BuildFoundation(house) // build foundation
house = BuildWalls(house) // add walls 
house = BuildRoof(house) // add roof
```

**Benefits of functional builder:**

* Each build step is a pure function operating on the House object
* Steps are highly reusable and composable
* No separate builder object needed
* Can validate or alter build steps
* More functional, immutable style

**Tradeoffs:**

* More allocations from copying objects each step
* Less fluent API style compared to traditional Builder

### Hybrid Approach to Functional Builder

This approach combines elements of functional programming with a mutable builder object. We'll call it the _functional-procedural hybrid builder_. It involves:

* A mutable builder object like `HouseBuilder` that stores build actions
* Pure functions for each build step like `AddDoor()`, `AddWindow()`
* Encapsulating state mutation inside the builder object
* Composing build steps by calling builder methods

#### Example

```go
type HouseBuilder struct {
  house House
  actions [](House) House 
}

func (b *HouseBuilder) AddDoor() {
  // Append new action
  b.actions = append(b.actions, AddDoorAction) 
}

func AddDoorAction(house House) House {
  // Copy and modify house
  // ...
  return newHouse  
}

func (b HouseBuilder) Build() {
  for _, a := range b.actions {
     b.house = a(b.house) // Mutate internal state
  }

  return b.house // Return final product
}
```

So the key points are:

* External pure functions for build steps
* Mutable builder tracking actions and state
* Combining function composition with imperative loops

This balances flexibility with a performance by mutating internally while exposing pure functions.

***

## See Also

{% embed url="https://www.youtube.com/watch?v=uZKU0J456fw" %}
Builder Design Pattern in Go by We Build Developers on YouTube
{% endembed %}

* [Refactoring.Guru - Builder in Go / Design Patterns Conceptual Example](https://refactoring.guru/design-patterns/builder/go/example)
