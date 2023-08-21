# God Struct/Component

_The God Struct/Component_ (or _God Class/Object_) is an anti-pattern that refers to a struct or component that knows too much or does too much in a program. Some key characteristics:

* Has too many responsibilities - violates the [Single Responsibility Principle](../solid-principles.md#s-single-responsibility-principle)
* Contains most/all business logic for the application
* Has dependencies on many other structs/packages
* Other structs/packages rely heavily on it for information/functionality

#### Problems with God Struct/Packages

* Difficult to maintain - any change can affect code everywhere
* Hard to extend or reuse - tightly coupled to rest of system
* May bottleneck performance due to excessive processing
* Not testable in isolation due to many dependencies

***

## Examples

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

func (u *User) HashPassword(password string) {
  //...
}

func (u *User) EncodeJWT() {
  //...
}

func (u *User) SaveToDB() {
  //...
}

func (u *User) SendEmail(text string) {
  //...
}
```

In this example, the User struct contains methods for authentication, logging, hashing, encoding, database access, and sending emails.

This violates the [_Single Responsibility Principle_](../solid-principles.md#s-single-responsibility-principle) and makes the User a God Object - it knows too much and has too many unrelated responsibilities.

To fix this, we can refactor it into separate structs and components.

#### Good Example

```go
// auth.go 
type Auth struct {
  //...
}

// logger.go
type Logger struct {
  //...
} 

// hasher.go
type Hasher struct {
  //...
}
```

***

## Improvements

The God Struct/Package anti-pattern is usually a result of poorly designed code that places too many responsibilities into a single struct or package. It can be improved by:

* Following the [Single Responsibility Principle](../solid-principles.md#s-single-responsibility-principle) - break into multiple structs and components
* Applying the [Open/Closed Principle](../solid-principles.md#o-open-closed-principle) - reduce dependencies
* Using [dependency inversion](../solid-principles.md#d-dependency-inversion-principle)
* Creating [facades](../structural-patterns/facade.md) - simplify interfaces

\
