🎲 Rubik's Cube Solver

A high-performance C++ implementation of multiple search algorithms to solve Rubik's Cube puzzles. This project demonstrates the evolution of AI search strategies from basic DFS to advanced IDA* with pattern database heuristics.
🎯 Overview

This solver implements 4 different search algorithms with increasing sophistication, showcasing the fundamental concepts of state-space search, heuristic optimization, and algorithm complexity trade-offs.

Current Active Algorithm: IDA* (Iterative Deepening A*) with Corner Pattern Database
🔍 Algorithms Implemented
1. DFS (Depth-First Search)

    Strategy: Explores one path deeply before backtracking
    Time Complexity: O(b^d) where b=18 moves, d=depth
    Space Complexity: O(d) - minimal memory usage
    Best For: Simple scrambles (≤6 moves)
    Limitation: No guarantee of optimal solution

2. BFS (Breadth-First Search)

    Strategy: Explores all states level-by-level
    Time Complexity: O(b^d)
    Space Complexity: O(b^d) - stores all visited states
    Advantage: Guarantees shortest solution
    Limitation: Memory explosion beyond 7-8 moves

3. IDDFS (Iterative Deepening DFS)

    Strategy: Repeatedly runs DFS with increasing depth limits
    Time Complexity: O(b^d)
    Space Complexity: O(d) - combines DFS memory efficiency with BFS completeness
    Best For: Medium difficulty (≤8 moves)
    Trade-off: Re-explores states but uses minimal memory

4. IDA (Iterative Deepening A)** ⭐ Currently Active

    Strategy: IDDFS enhanced with heuristic pruning
    Heuristic: Corner Pattern Database (pre-computed lookup table)
    Formula: f(n) = g(n) + h(n)
        g(n) = actual cost (moves taken)
        h(n) = estimated cost (from pattern database)
    Advantage: Dramatically reduces search space
    Capability: Solves complex 13+ move scrambles efficiently
    Space Complexity: O(d) with pattern database overhead

🏗️ Architecture
Cube Representations

The project implements three different cube data structures:

    RubiksCube3dArray - 3D array representation (intuitive)
    RubiksCube1dArray - Flattened 1D array (cache-friendly)
    RubiksCubeBitboard - Bitwise representation (most optimized)

Pattern Database

    File: Databases/cornerDepth5V1.txt
    Purpose: Pre-computed heuristic for corner piece positions
    Function: Provides admissible lower bound estimates for IDA*
    Storage: Compressed using NibbleArray (4-bit values)

📁 Project Structure

.
├── Model/                      # Cube representations
│   ├── RubiksCube.h           # Base class interface
│   ├── RubiksCube3dArray.cpp  # 3D array implementation
│   ├── RubiksCube1dArray.cpp  # 1D array implementation
│   └── RubiksCubeBitboard.cpp # Bitboard implementation
│
├── Solver/                     # Algorithm implementations
│   ├── DFSSolver.h            # Depth-First Search
│   ├── BFSSolver.h            # Breadth-First Search
│   ├── IDDFSSolver.h          # Iterative Deepening DFS
│   └── IDAstarSolver.h        # IDA* with heuristics
│
├── PatternDatabases/          # Heuristic database system
│   ├── PatternDatabase.h      # Base pattern database
│   ├── CornerPatternDatabase.h # Corner heuristic
│   ├── CornerDBMaker.h        # Database generator
│   ├── NibbleArray.h          # Compressed storage
│   └── PermutationIndexer.h   # State indexing
│
├── Databases/                 # Pre-computed data
│   └── cornerDepth5V1.txt     # Corner pattern database
│
└── main.cpp                   # Entry point & testing

🚀 Getting Started
Prerequisites

    C++ compiler (Clang/GCC with C++14 support)
    CMake 3.20 or higher

Building the Project

# Create build directory
mkdir build && cd build

# Configure with CMake
cmake ..

# Compile
make

# Run
./rubiks_cube_solver

Usage

The current implementation:

    Creates a solved Rubik's Cube
    Randomly scrambles it with 13 moves
    Displays the scrambled state
    Solves using IDA* algorithm
    Shows the solution moves

Sample Output:

Rubik's Cube:
       G W W 
       O W Y 
       O Y Y 
...

Scramble: U U' B2 L2 R U U2 B' B' D D' U' L'

Rubik's Cube:
       W W W 
       W W W 
       W W W 
...

Solution: L U B2 U L2 R' B2

🎓 Learning Outcomes

This project demonstrates:

✅ Graph Search Algorithms - Understanding uninformed vs informed search
✅ Heuristic Functions - Admissible heuristics and A* optimality
✅ Space-Time Tradeoffs - Memory efficiency vs computational speed
✅ State Space Search - Navigating exponential search spaces
✅ Data Structure Optimization - Multiple cube representations
✅ Pattern Databases - Pre-computation for faster solving
🔬 Algorithm Comparison
Algorithm	Memory Usage	Solution Quality	Speed	Max Scramble
DFS	Very Low	Not Optimal	Slow	~6 moves
BFS	Very High	Optimal	Fast	~7 moves
IDDFS	Very Low	Optimal	Medium	~8 moves
IDA*	Low	Near-Optimal	Fastest	13+ moves
🛠️ Technical Details
Move Notation

    18 possible moves: 6 faces × 3 rotations each
    Faces: U (Up), D (Down), L (Left), R (Right), F (Front), B (Back)
    Rotations: Clockwise, Counter-clockwise ('), 180° (2)
    Example: R U R' U' = Right clockwise, Up clockwise, Right counter-clockwise, Up counter-clockwise

Data Structures Used

    unordered_map - Hash table for visited states (O(1) lookup)
    queue - FIFO for BFS level-order traversal
    priority_queue - Min-heap for IDA* best-first search
    vector - Dynamic array for move sequences
    Bitboards - Compact 64-bit state representation

Hash Functions

Custom hash functions for each cube representation enable efficient state storage and lookup in hash maps.
🔄 Switching Algorithms

To test different solvers, uncomment the relevant sections in main.cpp:

// DFS Example (lines 178-192)
DFSSolver<RubiksCube3dArray, Hash3d> dfsSolver(cube, 8);
vector<RubiksCube::MOVE> solve_moves = dfsSolver.solve();

// BFS Example (lines 195-209)
BFSSolver<RubiksCubeBitboard, HashBitboard> bfsSolver(cube);
vector<RubiksCube::MOVE> solve_moves = bfsSolver.solve();

// IDDFS Example (lines 211-225)
IDDFSSolver<RubiksCubeBitboard, HashBitboard> iddfsSolver(cube, 7);
vector<RubiksCube::MOVE> solve_moves = iddfsSolver.solve();

// IDA* Example (currently active, lines 227-240)
IDAstarSolver<RubiksCubeBitboard, HashBitboard> idaStarSolver(cube, fileName);
vector<RubiksCube::MOVE> moves = idaStarSolver.solve();

📊 Performance Notes

    IDA Solving Time:* 13-move scrambles typically solve in 10-60 seconds depending on complexity
    Pattern Database: Pre-loaded from disk for instant heuristic access
    State Space: 43,252,003,274,489,856,000 possible Rubik's Cube configurations
    God's Number: Any cube can be solved in 20 moves or less (proven in 2010)

🤝 Use Cases

    Academic Projects: Algorithm analysis and complexity studies
    Interview Preparation: Demonstrates search algorithm expertise
    AI Learning: Understanding heuristic search and optimization
    Competitive Programming: State-space search optimization techniques

📝 License

This project is available under the MIT License - see the LICENSE file for details.
🙏 Acknowledgments

    Pattern database concept inspired by Richard Korf's pioneering work on IDA*
    Rubik's Cube move notation follows standard WCA (World Cube Association) conventions

Built with: C++14 | CMake | Clang
Algorithms: DFS, BFS, IDDFS, IDA*
Focus: Search Optimization & Heuristic Design
