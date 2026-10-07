# Trace

Animated, step-by-step explanations of data structures and algorithms, built for coding-interview prep. Every topic shows the structure changing on screen, a one-line explanation of each step, the live values of the variables, and the matching line of Java code highlighted as it runs.

Trace is a single self-contained HTML file (`trace.html`). There is no build step, no server and no dependencies beyond two optional Google Fonts.

## What's inside

42 topics in three sections.

### Data structures, built from scratch

These keep their state between runs, so you can build a structure up one operation at a time. **Reset data** restores the starting state.

| Topic | Operations |
|---|---|
| Array | insert, delete, search |
| Linked list | insert, delete, search |
| Stack | push, pop, peek |
| Queue | enqueue, dequeue |
| Hash table | insert, search, delete (separate chaining) |
| Binary search tree | insert, search, in-order walk |
| Heap | push, pop, peek (min-heap) |
| Graph | add edge, remove edge, neighbors (adjacency list) |
| Trie | insert, search |
| LRU cache | get, put (hash map + doubly linked list) |

### Java built-ins

The same animations as the from-scratch versions, but the code panel shows the real `java.util` API. Each built-in links to its from-scratch counterpart ("How it works inside"), and each from-scratch topic links back to its built-in.

| Topic | Operations |
|---|---|
| `ArrayList` | `add(index, value)`, `remove(index)`, `indexOf(value)` |
| `LinkedList` | `add(index, value)`, `remove(value)`, `indexOf(value)` |
| `ArrayDeque` as a stack | `push`, `pop`, `peek` |
| `ArrayDeque` as a queue | `offer`, `poll` |
| `HashSet` and `HashMap` | `add`, `contains`, `remove` |
| `TreeSet` and `TreeMap` | `add`, `contains`, sorted for-each |
| `PriorityQueue` (min-heap) | `offer`, `poll`, `peek` |
| `PriorityQueue` (max-heap) | `offer`, `poll`, `peek` with `Collections.reverseOrder()` |
| `LinkedHashMap` as an LRU cache | `get`, `put` with `accessOrder = true` and `removeEldestEntry` |

### Algorithm patterns

Listed in the order the page shows them, which is a reasonable study order.

| Topic | Example problem |
|---|---|
| Two pointers | pair with a target sum |
| Sliding window | longest substring without repeating characters |
| Prefix sum | count subarrays that sum to k |
| Hash map | two sum, group anagrams |
| Intervals | merge overlapping intervals |
| Stack | next greater element (monotonic stack) |
| Binary search | find a value in a sorted array |
| Binary search variants | search a rotated array; binary search on the answer (Koko eating bananas) |
| Sorting | merge sort, quicksort, quickselect |
| Linked list techniques | reverse in place, find the middle, detect a cycle |
| Binary tree | level order, max depth, validate BST, lowest common ancestor |
| Top K | kth largest with a size-k min-heap |
| DFS | depth-first traversal with a call stack |
| BFS | breadth-first traversal with a queue |
| Graphs | number of islands |
| Topological sort | course schedule (Kahn's algorithm), with cycle detection |
| Union-Find | connected groups and redundant edges (path compression, union by size) |
| Dijkstra | shortest paths in a weighted graph |
| Backtracking | N-Queens (4, 5 or 6) |
| Tries | autocomplete by prefix |
| DP: 1D | house robber |
| DP: 2D grid | longest common subsequence |
| DP: knapsack | 0/1 knapsack |

## Features

- **Step-by-step playback.** Play, pause, step forward and back, jump to the first or last step, and change the speed.
- **Synced Java code.** The line that produced each step is highlighted. For Java built-ins, the method call you're running stays highlighted while the animation shows what happens inside it.
- **Live variables.** Values like `lo`, `mid`, `hi`, `visited` or `dp[i][j]` are shown under the caption as they change.
- **Your own inputs.** Every topic takes custom input: numbers, strings, words, edges, grids, trees in level order, weights. Invalid input gets a plain explanation of what to enter instead.
- **"How to spot it" notes.** Every algorithm lists the cues that should make you reach for it. Data structures and built-ins list when to use them, including Java gotchas such as `remove(int)` versus `remove(Object)`.
- **Time and space complexity** for every operation.
- **Answer tracing.** DP topics trace back through the table to show the actual choice (which houses, which letters, which items), not just the number.
- **Dark mode, mobile layout, reduced motion and keyboard control.**

## Using it

Open `trace.html` in any modern browser.

1. Pick a topic from the sidebar (or the dropdown on mobile).
2. For topics with several operations, pick one from the tabs.
3. Edit the inputs and press **Run**. Algorithm topics run with their default input as soon as you open them.
4. Step through, or press play.

Keyboard: **←** and **→** step back and forward, **space** plays and pauses (when focus isn't in an input).

Color key:

| Color | Meaning |
|---|---|
| Amber | current element |
| Purple | being compared |
| Teal | newly added |
| Green | found, or part of the result |
| Red | removed or ruled out |
| Blue | visited |
| Grey or faded | done, or out of the current range |

The page remembers the last topic you opened in the browser's local storage. If storage is blocked it simply starts at the first topic.

## How it's built

Everything lives in one `<script>` block inside `trace.html`, in this order:

| Section | What it does |
|---|---|
| Core | Scene primitives (`box`, `circ`, `tag`, `txt`, `band`, `edge`), pointer-tag stacking, the code-marker parser and the step recorder `Rec` |
| Data structures | One module per structure, each with `init`, `view`, `ops` and `run` |
| Algorithms | One module per pattern, plus shared helpers (`arrScene`, `parseTree`, `treeScene`, the `LL` list drawer, `GPOS` graph layout, `TRIE`) |
| Java built-ins | `libMod(baseId, ...)` clones a from-scratch module and swaps in library code |
| How to spot it | The `SPOT` notes and the study order for the algorithm list |
| UI and engine | Navigation, form fields, playback, SVG rendering and tweening |

### How a step is recorded

A module's `run(model, opId, values, R)` performs the real operation and calls `R.add(caption, marker, scene, vars)` for every step worth showing. The scene is a plain object listing nodes and edges with keys. The renderer matches keys between consecutive scenes, so an element with the same key glides to its new position, new keys fade in and missing keys fade out. That's what makes swaps, shifts and re-linking animate without any per-topic animation code.

`run` returns a string instead of recording steps when the input is invalid; the string is shown as the error.

### Code markers

Java code is stored as a template string. A line ending in `⟨name⟩` can be highlighted by a step that passes `'name'` as its marker. In library code, `⟨op:push⟩` marks the line for the `push` operation: any step of that operation without a more specific marker highlights it. Markers are stripped before display.

```js
code: `
int binarySearch(int[] nums, int target) {
    int lo = 0, hi = nums.length - 1;       ⟨init⟩
    ...`
```

### Adding a topic

Push a module onto `MODS`:

```js
MODS.push({
  id: 'mytopic', group: 'algo',            // 'ds', 'java' or 'algo'
  title: 'My topic', sub: 'short subtitle',
  blurb: 'One-paragraph explanation.',
  ops: [{
    id: 'run', label: 'Run', cx: 'Time O(n), space O(1)',
    fields: [{ id: 'nums', label: 'Numbers', type: 'text', def: '1, 2, 3', wide: true }],
    code: `...Java with ⟨markers⟩...`
  }],
  run(model, op, v, R) {
    const a = nums(v.nums);
    R.add('First step.', 'init', arrScene(a, { tags: { i: 0 } }), { i: 0 });
  }
});
```

Field types are `num`, `text`, `area` (multi-line) and `select`. Data-structure modules also define `init()` (the starting state) and `view(model)` (the idle scene). Then add an entry to `SPOT` and, for algorithms, to the study-order list.

## Testing

Each change was checked by running every operation of every topic headlessly in Node (scene validity: no duplicate keys, no missing edge endpoints, no out-of-bounds or NaN coordinates) and checking each topic's final answer against known results and edge cases. A Playwright pass then clicked through every operation in Chromium, confirming no runtime errors, and screenshots were reviewed for layout.

## Simplifications to know about

The animations are faithful to the algorithms, but a few demos simplify the real Java classes. Each topic's description says so where it applies:

- Java's `LinkedList` is doubly linked and walks from the nearer end; the drawing is singly linked.
- `TreeSet` and `TreeMap` are red-black trees that rotate to stay balanced; the rotations aren't animated.
- `HashSet` and `HashMap` start with 16 buckets and resize at 75% full; the demo uses 7 fixed buckets so collisions are easy to see.
- `ArrayList` grows its backing array when full; the demo stops at 8 slots and says what would happen.
- `LinkedHashMap` keeps the eldest entry first; the LRU drawing puts the most recent next to `head`.
- Inputs are capped (for example, 8 to 12 array elements, 15 tree nodes, nodes A to H in graphs) so every scene fits on screen.

## Possible next additions

- BST delete (the three cases, including the in-order successor swap)
- Dynamic array growth and amortized O(1) append
- Circular queue (ring buffer)
- Hash table resizing, with a note on open addressing
- Building a heap in O(n)
