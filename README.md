# Analysis_and_Design
## Course, Analysis and Design Portfolio Submission
## Build and Run
### g++ -std=c++17 -O2 ProjectTwo.cpp -o ProjectTwo
### ./ProjectTwo
### Expected CSV format:
- CSCI100, Introduction to Computer Science
- CSCI200, Data Structures, CSCI100
- Algorithms, CSCI200, MATH150
- MATH150, Discrete Mathematics

## Project One: Run-Time & Memory Analysis (Data Structures)
## Data structures considered
``
std::unordered_map<std::string, Course> (hash table) used to store courses by ID.

std::map<std::string, Course> (balanced BST/red-black tree) considered as an alternative.

std::vector<std::string> — used as a temporary container to sort course IDs for printing.
``
## Asymptotic performance
- Operation / DS	unordered_map	map (RB tree)	vector (keys)
- Insert n courses	Average O(n) (O(1) per insert)	O(n log n)
- Lookup by ID	Average O(1)	O(log n)	O(n) (if scanning)
- Print sorted list	Build vector of n keys O(n) + sort O(n log n) + print O(n) → O(n log n)	In-order traversal O(n) (already sorted)	Sort O(n log n); print O(n)
- Print course info (title + prereqs)	Lookup target O(1) + resolve k prereqs O(k)	O(log n + k log n)

### Why hash + vector?
Most user interactions are lookups by course ID and loading from file; both are constant-time on average with a hash table. Sorting for display happens only on demand and costs O(n log n), which is acceptable and simpler than maintaining tree order everywhere.

### Memory considerations (high-level)

unordered_map: stores buckets + nodes. Extra overhead from buckets (≈ number of elements / load factor; default max load factor is ~1.0) and node pointers. Great lookup density but uses more memory than a tree at the same n.

map: each node holds 3 pointers (left/right/parent) + color/metadata; overhead per element is predictable but higher per node than a compact vector, and typically less spare capacity than a hash table’s buckets.

vector: contiguous; minimal overhead beyond capacity; used transiently for sorting keys, so memory is O(n) only during list printing.

## Project Two: Working Code Summary (Sorting & Printing)

### Menu options implemented:

Load file → Parses CSV, normalizes IDs to uppercase, validates rows, and stores Course{id, title, prereqs} in an unordered_map.

Print course list (alphanumeric) → Copies keys to a vector, sorts case-insensitively, prints "ID, Title".

Print course information → Prompts for a course ID, prints the title and resolves each prerequisite by ID (printing both ID and title; marks missing prereqs if not in the file).

Exit

### Complexities

- Loading: ~O(n + r) (r = total number of prerequisite entries).

- Sorted list: O(n log n).

- Single course info: O(1 + k) average (k prereqs).

  ### Industry best practices applied

- Input validation, clear error messages, comments, and descriptive names.

- Normalization of IDs for stable lookups.

- Separation of concerns: parsing, storage, menu/IO, printing.

- Graceful handling of malformed lines and missing prerequisites.

## Reflection

### Edit these to match your voice/experience if you like.

• What problem were you solving?
I built a small course-planner utility that loads a CSV of courses, then lets an adviser list courses in alphanumeric order and query a specific course to see its title and prerequisites. The focus was choosing data structures that balance fast lookups with easy, correct printing.

• How did you approach the problem (and why do data structures matter)?
I separated parsing, storage, and presentation. I chose a hash map to store courses by ID because it gives constant-time average inserts/lookups during loading and queries. For the sorted list, I collected keys into a vector and sorted them on demand. The choice of data structure directly determined both performance and code simplicity.

• How did you overcome roadblocks?
I handled messy CSV lines by trimming whitespace, skipping comments/blank rows, and reporting lines that lacked an ID/title. Normalizing course IDs to uppercase removed case-sensitivity bugs. I also reported missing prerequisites to make data issues visible instead of failing silently.

• How did this expand your approach to designing software and developing programs?
It reinforced designing around usage patterns: optimize for the common case (loads and lookups), then compose simple structures (vector + sort) for less frequent tasks. It also underscored the value of small, testable functions and clear error handling.

• How has this work evolved the way you write maintainable, readable, adaptable programs?
I now default to clearer interfaces, input validation, and normalization at boundaries. I keep data structures private to a module and expose intent-based functions (load, list, info). That makes swapping implementations (e.g., map instead of unordered_map) straightforward if requirements change.
