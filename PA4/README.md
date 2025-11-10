# Programming Assignment 4: Movie and TV Show Search and Sort
**CPTS 223 Advanced Data Structures**

## Project Overview
This project implements a command-line REPL (Read-Eval-Print Loop) system for searching and sorting movies and TV shows from the IMDB Top 1000 dataset. The application supports genre-based searches with customizable sorting options.

## Design Decisions

### Data Structures

#### 1. MediaItem Class
Encapsulates all information about a movie or TV show:
- Title, year, rating, runtime
- Multiple genres (stored as vector)
- Certificate, overview, director

**Justification**: Object-oriented design allows for clean encapsulation and easy extension.

#### 2. Storage: `vector<MediaItem>`
Stores all media items in a single contiguous vector.

**Justification**:
- Memory efficient with good cache locality
- Simple iteration for building indices
- O(1) access by index
- Modern C++ preferred container for sequential data

#### 3. Genre Index: `unordered_map<string, vector<MediaItem*>>`
Maps genre names to pointers of items in that genre.

**Justification**:
- O(1) average-case genre lookup
- Pointers avoid data duplication (items can be in multiple genres)
- Case-insensitive genre matching via lowercase keys
- Trade-off: Extra memory for pointers vs. fast lookup time

### Sorting Algorithms

Both **Merge Sort** and **Quick Sort** are implemented as generic function templates that accept:
- Any data type via templates
- Custom comparison functions via `std::function<bool(const T&, const T&)>`

#### Merge Sort
- **Time Complexity**: O(n log n) guaranteed
- **Space Complexity**: O(n) for temporary arrays
- **Stability**: Stable sort
- **Use Case**: When stability and guaranteed performance are needed

#### Quick Sort
- **Time Complexity**: O(n log n) average, O(n²) worst case
- **Space Complexity**: O(log n) for recursion stack
- **Stability**: Not stable
- **Use Case**: General purpose, often faster in practice

**Selection Mechanism**: The `SORTING_ALGORITHM` constant (1 for Merge Sort, 2 for Quick Sort) allows switching between algorithms.

### CSV Parsing

**Challenges**:
1. Space-delimited format (not comma-delimited despite .csv extension)
2. Fields containing spaces are wrapped in quotes
3. Multiple genres within a single field (comma-separated)
4. Runtime format: "142 min" requires extraction

**Solution**:
- Custom parser handles quote-delimited fields
- Genre parser splits on commas and trims whitespace
- Runtime parser extracts numeric value
- Robust error handling for malformed lines

### Missing Data Handling

**Approach**:
- Empty genres: Skip item or use empty vector
- Missing runtime: Set to 0
- Invalid numeric fields: Use try-catch, skip line with warning
- Document all decisions in code comments

**Justification**: Graceful degradation ensures maximum data utilization while maintaining data integrity.

## File Structure
```
PA4/
├── main.cpp              # REPL interface and command processing
├── MediaItem.h/.cpp      # Media item class
├── MovieDatabase.h/.cpp  # Data storage and CSV loading
├── Sort.h/.cpp           # Generic sorting algorithms
├── Comparators.h         # Comparator factory for different fields
├── Testing.h/.cpp        # Test suite with cassert
├── Makefile              # Build configuration
├── README.md             # This file
└── imdb_top_1000.csv     # Dataset (not included in repo)
```

## Building and Running

### Compile
```bash
make
```

### Run
```bash
make run
# OR
./pa4
```

### Clean
```bash
make clean
```

## Usage

### Available Commands

#### 1. findByGenre
Find all movies/TV shows in a genre and sort them.

**Syntax**: `findByGenre <genre> <sort_field> <order>`

**Parameters**:
- `genre`: Genre name (e.g., Drama, Action, Comedy)
- `sort_field`: Field to sort by (title, year, rating, runtime)
- `order`: Sort order (asc or desc)

**Example**:
```
PA4> findByGenre Action rating desc
```

#### 2. topRated
Show top N movies/TV shows in a genre by rating.

**Syntax**: `topRated <genre> <count> <order>`

**Parameters**:
- `genre`: Genre name
- `count`: Number of items to display
- `order`: Sort order (asc or desc)

**Example**:
```
PA4> topRated Drama 10 desc
```

#### 3. Built-in Commands
- `:help` - Display help message
- `:quit` - Exit the application

## Testing Strategy

### Test Coverage

#### 1. Sorting Algorithm Tests
- **Integer sorting**: Normal case (unsorted), edge case (already sorted)
- **String sorting**: Normal case (random), edge case (single element)
- **Descending order**: Both algorithms with reverse comparator
- **Edge cases**: Empty vector, single element, all duplicates

#### 2. MediaItem Sorting Tests
- Sort by rating (ascending/descending)
- Sort by year (ascending/descending)
- Sort by title and runtime
- Verify comparator correctness

#### 3. Data Structure Tests
- Genre lookup with valid/invalid genres
- Multiple genres per item
- Case-insensitive genre matching

### Test Cases Justification

**Normal Cases**: Verify expected behavior with typical inputs
**Edge Cases**: Ensure robustness with boundary conditions
- Empty collections
- Single elements
- Duplicate values
- Already sorted data
- Reverse sorted data

**Why These Tests**: These cases cover common usage patterns and potential failure points, ensuring algorithm correctness across diverse scenarios.

### Running Tests
Tests run automatically on program startup. All tests use `cassert` for validation.

## Algorithm Selection

To switch between sorting algorithms, modify the constant in `main.cpp`:
```cpp
const int SORTING_ALGORITHM = 1;  // 1 = Merge Sort, 2 = Quick Sort
```

## Implementation Notes

1. **Generic Templates**: Both sorting algorithms work with any comparable type
2. **Comparator Pattern**: Flexible sorting criteria via function objects
3. **Case-Insensitive Genres**: All genre comparisons use lowercase conversion
4. **Memory Efficiency**: Pointers used in results to avoid copying MediaItem objects
5. **Error Handling**: Comprehensive validation for user input and CSV parsing

## Known Limitations

1. CSV parsing assumes space-delimited format specific to this dataset
2. No support for multi-word genre names in command line (requires quotes handling)
3. Quick sort uses last element as pivot (could be optimized with median-of-three)

## Future Enhancements

1. Add filtering by year range, rating threshold
2. Support for saving/exporting results
3. Advanced search with multiple criteria
4. Optimized quick sort with randomized pivot selection

## Author
[Your Name]
CPTS 223 - Fall 2024

## References
- Dataset: IMDB Top 1000 Movies and TV Shows (Kaggle)
- Algorithms: Introduction to Algorithms (CLRS)
```

## File 13: .gitignore
```
# Compiled executables
pa4

# Object files
*.o

# Debug files
*.dSYM/

# IDE files
.vscode/
.idea/
*.swp
*~

# Dataset (too large for git)
imdb_top_1000.csv

# Video file (too large for git)
walkthrough.mp4

# Temporary files
*.tmp
