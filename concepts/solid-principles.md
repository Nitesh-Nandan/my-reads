# SOLID Principles

## Overview

SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable.

## The Five Principles

### 1. Single Responsibility Principle (SRP)
**A class should have only one reason to change.**

- Each class should have a single, well-defined responsibility
- If a class has multiple responsibilities, it becomes harder to maintain
- Example: A `User` class should only handle user data, not user authentication

### 2. Open/Closed Principle (OCP)
**Software entities should be open for extension but closed for modification.**

- New functionality should be added by extending existing code
- Existing code should not be modified to add new features
- Use inheritance, interfaces, or composition to achieve this

### 3. Liskov Substitution Principle (LSP)
**Derived classes must be substitutable for their base classes.**

- Objects of a superclass should be replaceable with objects of a subclass
- The subclass should not break the expected behavior of the superclass
- Example: A `Rectangle` subclass should work wherever a `Shape` superclass is expected

### 4. Interface Segregation Principle (ISP)
**Clients should not be forced to depend on interfaces they do not use.**

- Keep interfaces small and focused
- Avoid large, monolithic interfaces
- It's better to have many specific interfaces than one general-purpose interface

### 5. Dependency Inversion Principle (DIP)
**High-level modules should not depend on low-level modules. Both should depend on abstractions.**

- Depend on abstractions, not concrete implementations
- Use dependency injection to provide dependencies
- This promotes loose coupling and easier testing

## Benefits

- **Maintainability**: Easier to modify and extend code
- **Testability**: Simpler to write unit tests
- **Flexibility**: Code is more adaptable to change
- **Reusability**: Components can be reused in different contexts

## Example

```typescript
// Bad: Violates SRP
class User {
  save() { /* save to database */ }
  sendEmail() { /* send email */ }
  validate() { /* validate data */ }
}

// Good: Follows SRP
class User {
  validate() { /* validate data */ }
}

class UserRepository {
  save(user: User) { /* save to database */ }
}

class EmailService {
  sendEmail(user: User) { /* send email */ }
}
```

## Resources

- [Clean Code by Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350884)
- [SOLID Principles on Wikipedia](https://en.wikipedia.org/wiki/SOLID)
- [Refactoring Guru - SOLID](https://refactoring.guru/design-patterns/solid-principles)
