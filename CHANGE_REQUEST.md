# Change Request: Conway's Game of Life

## Description

Implement Conway's Game of Life - a cellular automaton simulation where cells on a grid evolve based on simple rules. The Game of Life is a zero-player game that demonstrates how complex patterns can emerge from simple rules.

### Rules:
1. Any live cell with 2 or 3 live neighbors survives
2. Any dead cell with exactly 3 live neighbors becomes alive
3. All other cells die or stay dead

## Implementation Plan

### A. Core Components

#### 1. **Grid/Board Class** (`src/game-of-life/Grid.ts`)
- Represents the 2D grid of cells
- Properties:
  - `width: number` - grid width
  - `height: number` - grid height
  - `cells: boolean[][]` - 2D array representing alive (true) or dead (false) cells
- Methods:
  - `constructor(width: number, height: number)` - initialize grid
  - `getCell(x: number, y: number): boolean` - get cell state
  - `setCell(x: number, y: number, alive: boolean): void` - set cell state
  - `clear(): void` - reset all cells to dead
  - `randomize(): void` - randomly populate the grid

#### 2. **Game Engine Class** (`src/game-of-life/GameEngine.ts`)
- Handles the game logic and state transitions
- Properties:
  - `grid: Grid` - current grid state
  - `generation: number` - current generation count
- Methods:
  - `constructor(grid: Grid)` - initialize with a grid
  - `nextGeneration(): void` - calculate and apply next generation
  - `countLiveNeighbors(x: number, y: number): number` - count live neighbors for a cell
  - `shouldCellLive(x: number, y: number, isAlive: boolean): boolean` - apply Game of Life rules
  - `reset(): void` - reset to generation 0

#### 3. **Patterns Utility** (`src/game-of-life/Patterns.ts`)
- Pre-defined patterns that can be placed on the grid
- Export common patterns:
  - `GLIDER` - moves diagonally
  - `BLINKER` - oscillates between two states
  - `TOAD` - oscillator with period 2
  - `BEACON` - oscillator with period 2
  - `PULSAR` - oscillator with period 3
  - `GLIDER_GUN` - creates gliders
- Helper function:
  - `placePattern(grid: Grid, pattern: boolean[][], x: number, y: number): void`

#### 4. **Renderer Interface** (`src/game-of-life/Renderer.ts`)
- Abstract rendering interface for flexibility
- Interface `IRenderer`:
  - `render(grid: Grid): void` - render the grid
  - `clear(): void` - clear the display
- Implementation `ConsoleRenderer`:
  - Renders grid to console using characters (e.g., █ for alive, · for dead)

#### 5. **Main Game Controller** (`src/game-of-life/GameOfLife.ts`)
- Orchestrates the game components
- Properties:
  - `engine: GameEngine`
  - `renderer: IRenderer`
  - `isRunning: boolean`
  - `speed: number` - milliseconds between generations
- Methods:
  - `constructor(width: number, height: number, renderer: IRenderer)`
  - `start(): void` - start the simulation
  - `stop(): void` - stop the simulation
  - `step(): void` - advance one generation
  - `setSpeed(speed: number): void` - adjust simulation speed

### B. File Structure
```
src/
  game-of-life/
    Grid.ts
    GameEngine.ts
    Patterns.ts
    Renderer.ts
    GameOfLife.ts
    index.ts (exports all public APIs)
  examples/
    basic-game-of-life.ts (demo script)
```

### C. Testing Strategy
Create test files for each component:
- `Grid.test.ts` - test grid operations
- `GameEngine.test.ts` - test game rules and transitions
- `Patterns.test.ts` - verify pattern definitions

### D. Documentation
- Add README.md in `src/game-of-life/` with:
  - Overview of Conway's Game of Life
  - Usage examples
  - API documentation
  - Pattern showcase

### E. Type Safety
- Use strict TypeScript types throughout
- Ensure proper error handling for out-of-bounds access
- Use readonly where appropriate for immutability

### F. Performance Considerations
- Use efficient neighbor counting algorithm
- Consider double-buffering for grid updates
- Optimize for grids up to 100x100 cells

## Success Criteria
- ✅ All Game of Life rules correctly implemented
- ✅ Grid can be initialized with custom patterns
- ✅ Simulation can be started, stopped, and stepped through
- ✅ Console renderer displays the grid clearly
- ✅ Pre-defined patterns work as expected
- ✅ Unit tests cover core functionality
- ✅ Code is well-typed and documented
