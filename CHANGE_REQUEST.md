# Change Request: Pancake Implementation

## Description

Implement a pancake feature for the TypeScript repository. This will add functionality to create, flip, and serve delicious pancakes in TypeScript!

The pancake implementation should provide:
- A `Pancake` class with properties like size, toppings, and cooking state
- Methods to flip pancakes
- Methods to check if a pancake is cooked
- Support for different pancake types (buttermilk, blueberry, chocolate chip, etc.)

## Implementation Plan

### 1. Create Core Pancake Module
Create a new file `pancake.ts` in the root directory that will contain:

**Pancake Interface/Type Definitions:**
- Define a `PancakeType` enum for different pancake varieties
- Define a `PancakeState` enum for cooking states (RAW, COOKING, FLIPPED, DONE, BURNT)
- Define an interface for pancake toppings

**Pancake Class:**
```typescript
class Pancake {
  - Properties: type, size, toppings, state, flips, cookingTime
  - Constructor to initialize a new pancake
  - flip() method to flip the pancake
  - cook(duration) method to advance cooking state
  - addTopping(topping) method
  - isDone() method to check if ready to serve
  - isBurnt() method to check if overcooked
  - describe() method to get a string description
}
```

### 2. Create Pancake Factory
Add a `PancakeFactory` class that can:
- Create pancakes of different types
- Apply default toppings based on pancake type
- Validate pancake configurations

### 3. Add Utility Functions
Include helper functions:
- `makePancakeStack(count, type)` - create multiple pancakes
- `servePancakes(stack)` - format pancakes for serving
- `calculateCookingTime(size)` - determine optimal cooking duration

### 4. Export Public API
Ensure all public classes, interfaces, and functions are properly exported for use in other TypeScript projects.

### 5. Add TypeScript Features
Demonstrate TypeScript features:
- Strict typing throughout
- Generic types where applicable
- Union types for flexible parameters
- Type guards for state checking
- Optional parameters with defaults

## Files to Create
- `pancake.ts` - Main pancake implementation file

## Testing Considerations
While not implementing tests in this change request, the code should be designed to be testable with:
- Unit tests for Pancake class methods
- Integration tests for PancakeFactory
- Edge cases for overcooking/burning

## Documentation
The code will include JSDoc comments for all public APIs to ensure proper IntelliSense support.
