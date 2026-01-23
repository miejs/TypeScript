# Change Request: Fix `typeof null` Returns `'object'` Bug

## Description

This is a fix for one of JavaScript's most notorious bugs: `typeof null` returning `'object'` instead of `'null'`. This behavior has existed since the first version of JavaScript and is considered a mistake by JavaScript's creator, Brendan Eich.

### The Bug
In JavaScript:
```javascript
typeof null === 'object' // true (incorrect)
```

The expected behavior should be:
```javascript
typeof null === 'null' // true (correct)
```

### Why This Happens
In the original JavaScript implementation, values were stored with a type tag. Objects had a type tag of 0, and `null` was represented as the NULL pointer (0x00 in most platforms). This caused `typeof` to incorrectly identify `null` as an object.

## Implementation Plan

Since TypeScript is a superset of JavaScript that compiles to JavaScript, we cannot change the runtime behavior of JavaScript's `typeof` operator. However, we can implement TypeScript-specific improvements to help developers avoid this bug:

### Approach 1: Type System Enhancements
1. **Enhance type narrowing for `typeof` checks**
   - Location: `src/compiler/checker.ts`
   - Update the `narrowTypeByTypeof` function to better handle null checks
   - Add compiler warnings when developers use `typeof x === 'object'` without null checking

2. **Add strict null checks with typeof**
   - Location: `src/compiler/checker.ts`
   - When `strictNullChecks` is enabled, emit warnings for unsafe typeof comparisons
   - Suggest using `x !== null && typeof x === 'object'` pattern

### Approach 2: Compiler Warnings & Linting
1. **Create a new diagnostic message**
   - Location: `src/compiler/diagnosticMessages.json`
   - Add message: "typeof null returns 'object'. Consider explicit null check before typeof comparison."

2. **Implement detection logic**
   - Location: `src/compiler/checker.ts`
   - Detect patterns like `typeof x === 'object'` without prior null check
   - Emit warning with suggested fix

### Approach 3: Language Service Enhancements
1. **Add Quick Fix suggestions**
   - Location: `src/services/codefixes/`
   - When `typeof x === 'object'` is detected, offer quick fix to refactor to `x !== null && typeof x === 'object'`

2. **Update IntelliSense**
   - Location: `src/services/completions.ts`
   - Show helpful hints when typing `typeof` expressions

### Approach 4: Documentation & Type Definitions
1. **Update lib.d.ts type definitions**
   - Location: `src/lib/`
   - Add JSDoc comments documenting this quirk
   - Provide type guard examples

2. **Add to TypeScript Handbook**
   - Document this JavaScript quirk
   - Provide best practices for null/object type checking

## Files to Modify

1. `src/compiler/checker.ts` - Core type checking logic
2. `src/compiler/diagnosticMessages.json` - Warning messages
3. `src/services/codefixes/fixTypeofNullCheck.ts` - New quick fix provider
4. `src/lib/es5.d.ts` - Type definition enhancements
5. `tests/cases/compiler/typeofNull.ts` - New test cases

## Testing Strategy

1. Add unit tests for type narrowing with null and typeof
2. Add tests for the new diagnostic warnings
3. Add tests for quick fix code actions
4. Ensure backward compatibility with existing code

## Notes

- This change is additive and non-breaking
- All changes are compile-time only and don't affect runtime behavior
- The changes help developers write safer code while maintaining JavaScript compatibility
