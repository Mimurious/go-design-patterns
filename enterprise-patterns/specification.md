# Specification

The Specification pattern encapsulates business rules or criteria into reusable query structs. It provides a way to separate logical conditions from the structs they operate on.

* Specifications are structs that encapsulate a business rule or check. They have a method like `IsSatisfied(candidate T) bool` to evaluate candidate structs.
* Specifications can be combined using boolean logic - like `AndSpec`, `OrSpec`, `NotSpec` structs.
* A generic `Repository` struct query method accepts Specification interfaces and returns structs satisfying them.
* The `Repository` isn't coupled to any specific Specification, new Specs can be added without changing it.

#### Benefits

* Business rules/criteria are modularized into Specification structs.
* App logic is separated from query logic.
* Adding new query criteria doesn't affect the app code.
* Specs are reusable and combinable.
* Improves maintainability and extensibility.

Specifications would encapsulate business logic in structs with `IsSatisfied` methods. The repository accepts interface parameters to remain decoupled.

{% hint style="info" %}
The Specification pattern allows us to adhere to [the Open/Closed Principle](../solid-principles.md#o-open-closed-principle) - code is open for extension but closed for modification.
{% endhint %}

#### Example

```go
// Specification interfaces
type Spec interface {
  IsSatisfied(product Product) bool 
}

// Concrete specifications
type ColorSpec struct {
  color string
}

func (cs ColorSpec) IsSatisfied(p Product) bool {
  return p.Color == cs.color 
}

type SizeSpec struct {
  size int
}

func (ss SizeSpec) IsSatisfied(p Product) bool {
  return p.Size == ss.size
}

// Repository 
type ProductRepository struct {}

func (r ProductRepository) Query(spec Spec) []Product {
  // Query logic using spec
}
```
