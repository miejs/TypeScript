# Change Request: Fix Missing Error Location for TS2878

## A) Description of the Bug

**Ticket:** PAD-9  
**Error Code:** TS2878  
**Error Message:** "This import path is unsafe to rewrite because it resolves to another project..."

### Problem
The TypeScript compiler is generating error TS2878 when import paths resolve to another project, but the error is not being reported with specific file locations in the command line output. While the error appears correctly in the editor (with red squiggles in the correct locations within multiple files in the `test/` directory), developers running `tsc` from the command line cannot see where the problematic import statements are located.

This creates a poor developer experience because:
- Developers cannot quickly identify which files contain the unsafe import paths
- CI/CD pipelines don't provide actionable error information
- Debugging requires opening an editor to see the squiggles instead of relying on compiler output

### Expected Behavior
The error message should include:
- The file path where the unsafe import is located
- The line and column number of the import statement
- The specific import path that is causing the issue

### Current Behavior
The error is displayed without location information in command line output, making it difficult to identify which import statements need to be fixed.

---

## B) Implementation Plan

### 1. Locate Error Generation Code
- Search for where error TS2878 is generated in the TypeScript compiler source code
- Look for the diagnostic message definition and its usage
- Common locations to check:
  - `src/compiler/diagnosticMessages.json` - for the error message definition
  - `src/compiler/moduleNameResolver.ts` - likely location for module resolution errors
  - `src/compiler/checker.ts` - type checking and error reporting

### 2. Analyze Current Error Reporting
- Examine how the error is currently being reported
- Identify why location information is missing in CLI output
- Determine if the error is being reported at the correct AST node
- Check if the error is being reported with a file reference

### 3. Implement the Fix
The fix will likely involve one or more of these changes:

#### Option A: Add Location to Existing Error Report
```typescript
// Current (hypothetical example):
error(/*node*/ undefined, Diagnostics.This_import_path_is_unsafe...);

// Fixed:
error(importDeclaration, Diagnostics.This_import_path_is_unsafe...);
```

#### Option B: Ensure Error is Attached to Correct Node
- If the error is being reported on a file-level node without specific location
- Attach it to the actual import declaration or module specifier node
- This will automatically include line/column information

#### Option C: Add Additional Context to Error
- Include the problematic import path in the error message
- Add a note showing which project the import resolves to
- Provide a hint about how to fix it (e.g., adjust tsconfig.json paths)

### 4. Testing Strategy
- Create test cases with multi-project scenarios that trigger TS2878
- Verify that error output includes:
  - File path
  - Line and column numbers
  - The import statement causing the error
- Test both CLI output and editor integration
- Ensure the fix doesn't break existing error reporting behavior

### 5. Files to Modify (Estimated)
- `src/compiler/diagnosticMessages.json` - Potentially update error message
- `src/compiler/moduleNameResolver.ts` or similar - Add location to error report
- `tests/cases/compiler/` - Add test cases for the fix
- `tests/baselines/reference/` - Update baselines if needed

### 6. Validation
- Run existing test suite to ensure no regressions
- Manually test with a multi-project TypeScript setup
- Verify error appears correctly in both:
  - Command line output (with file path and line number)
  - Editor (with red squiggles in correct locations)

---

## Notes
- This is a compiler diagnostics improvement, not a breaking change
- The fix should maintain backward compatibility
- Consider if this affects other similar error messages that might have the same issue
