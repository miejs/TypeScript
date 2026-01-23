# Change Request: HTML Report Compiler Option

## Description

This feature request is to implement a new TypeScript compiler option that generates compilation reports in HTML format. This would provide developers with a human-readable, visually appealing summary of the compilation process, including:

- Compilation statistics (files processed, time taken, etc.)
- Error and warning summaries with severity levels
- File-by-file compilation details
- Dependencies and module resolution information
- Performance metrics and bottlenecks
- Optional charts and visualizations

The HTML report would be useful for:
- Code reviews and documentation
- CI/CD pipeline reports
- Build analysis and optimization
- Team collaboration and transparency
- Debugging complex build issues

## Implementation Plan

### 1. Compiler Options Extension
**Files to modify:**
- `src/compiler/types.ts` - Add new `htmlReport` and `htmlReportPath` options to `CompilerOptions` interface
- `src/compiler/commandLineParser.ts` - Register the new command line options

**Changes:**
```typescript
// In CompilerOptions interface
htmlReport?: boolean;
htmlReportPath?: string;
```

### 2. Report Data Collection
**Files to create/modify:**
- `src/compiler/reportGenerator.ts` (new) - Core report generation logic
- `src/compiler/watch.ts` - Hook into watch mode to collect incremental compilation data
- `src/compiler/program.ts` - Collect compilation statistics during program creation

**Data to collect:**
- Start and end timestamps
- List of all source files processed
- Errors and warnings with file locations
- Module resolution results
- Emit results (files written, sizes)
- Type checking time
- Parse time vs check time breakdown

### 3. HTML Report Generator
**Files to create:**
- `src/compiler/htmlReportTemplate.ts` (new) - HTML template with embedded CSS/JS
- `src/compiler/reportFormatter.ts` (new) - Format collected data into HTML structure

**Report sections:**
1. **Summary Dashboard**
   - Total files, errors, warnings
   - Compilation time
   - Success/failure status

2. **Error/Warning Details**
   - Grouped by severity
   - File location links
   - Code snippets with line numbers

3. **File List**
   - Table with file paths, sizes, processing times
   - Module type (ES6, CommonJS, etc.)

4. **Performance Metrics**
   - Parse time per file
   - Type check time per file
   - Emit time
   - Charts (if practical with inline SVG/Canvas)

5. **Configuration**
   - Active compiler options
   - tsconfig.json used

### 4. Integration Points
**Files to modify:**
- `src/compiler/tsc.ts` - Main entry point to trigger report generation
- `src/executeCommandLine/executeCommandLine.ts` - CLI integration
- `src/compiler/builder.ts` - For incremental builds

**Workflow:**
1. User adds `--htmlReport` or sets `htmlReport: true` in tsconfig.json
2. Optionally specify output path with `--htmlReportPath ./report.html`
3. After compilation completes, generate and write HTML report
4. Console log: "HTML report generated at: ./report.html"

### 5. HTML Template Structure
```html
<!DOCTYPE html>
<html>
<head>
    <title>TypeScript Compilation Report</title>
    <style>/* Embedded CSS with responsive design */</style>
</head>
<body>
    <header>
        <h1>TypeScript Compilation Report</h1>
        <div class="summary">/* Summary stats */</div>
    </header>
    <main>
        <section id="errors">/* Errors and warnings */</section>
        <section id="files">/* File list table */</section>
        <section id="performance">/* Performance metrics */</section>
        <section id="config">/* Configuration details */</section>
    </main>
    <script>/* Minimal JS for interactivity (filtering, sorting) */</script>
</body>
</html>
```

### 6. Testing Strategy
**Files to create:**
- `src/testRunner/unittests/htmlReport.ts` (new) - Unit tests for report generation
- Add test cases in existing test suites

**Test scenarios:**
- Report generation with no errors
- Report generation with errors and warnings
- Custom report path handling
- Watch mode report updates
- Large projects (performance test)
- Missing output directory creation

### 7. Documentation
**Files to update:**
- Add documentation for the new compiler options
- Update any relevant configuration guides

## Dependencies

- No external dependencies required (self-contained HTML with inline CSS/JS)
- Uses existing TypeScript compiler infrastructure
- Compatible with existing build tools and workflows

## Backward Compatibility

- This is an additive feature with no breaking changes
- Default behavior unchanged (reports not generated unless explicitly enabled)
- Existing compiler options remain unaffected

## Future Enhancements (Out of Scope)

- JSON/XML report formats
- Real-time report updates in watch mode
- Integration with coverage tools
- Comparison reports between builds
- Plugin system for custom report sections
