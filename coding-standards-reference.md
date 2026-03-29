# React Coding Standards Reference

This document provides detailed standards for auditing React applications.

## React Component Patterns

### Function Components (Preferred)

```jsx
// GOOD - Function component with hooks
function UserProfile({ name, email }) {
  const [isEditing, setIsEditing] = useState(false);

  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
    </div>
  );
}

// BAD - Class component (legacy pattern)
class UserProfile extends React.Component {
  render() {
    return (
      <div>
        <h2>{this.props.name}</h2>
        <p>{this.props.email}</p>
      </div>
    );
  }
}
```

### Hooks Rules

```jsx
// GOOD - Hooks at top level
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    fetchResults(query).then(setResults);
  }, [query]);

  return <ResultsList items={results} />;
}

// BAD - Hook inside conditional
function SearchResults({ query }) {
  if (!query) return null;  // Early return BEFORE hooks!

  const [results, setResults] = useState([]);  // Violates rules of hooks
  // ...
}

// BAD - Hook inside loop
items.forEach(item => {
  const [value, setValue] = useState(item.value);  // Never do this
});
```

#### Grep Patterns:
- `if.*\buse[A-Z]` - Conditional hook call
- `for.*\buse[A-Z]` or `\.forEach.*\buse[A-Z]` - Hook in loop
- `\?\s*use[A-Z]` - Ternary hook call
- `&&\s*use[A-Z]` - Short-circuit hook call

---

## Naming Conventions

### Components and Files
```
GOOD:
  UserProfile.jsx       (PascalCase component file)
  useAuth.js            (camelCase with use prefix for hooks)
  formatDate.js         (camelCase for utilities)
  constants.js          (camelCase for config files)
  MAX_RETRIES = 3       (SCREAMING_SNAKE_CASE for constants)

BAD:
  user-profile.jsx      (kebab-case component file)
  userprofile.jsx       (no casing distinction)
  UseAuth.js            (PascalCase hook file)
  auth_hook.js          (snake_case)
```

### Props and State
```jsx
// GOOD
const [isLoading, setIsLoading] = useState(false);    // Boolean: is/has/should prefix
const [count, setCount] = useState(0);                 // Descriptive name
const [selectedItems, setSelectedItems] = useState([]); // Plural for arrays

function Button({ onClick, isDisabled, children }) {}  // Event handler: on prefix
function List({ items, onItemSelect, renderItem }) {}  // Render prop: render prefix

// BAD
const [loading, setLoading] = useState(false);  // Missing boolean prefix
const [data, setData] = useState([]);            // Generic name
const [flag, setFlag] = useState(false);         // Meaningless name
```

---

## Component Organization

### Recommended File Structure
```
src/ (or app/javascript/)
  components/         # Reusable components
    Button.jsx
    Modal.jsx
  pages/              # Route-level components
    Dashboard.jsx
    Settings.jsx
  hooks/              # Custom hooks
    useAuth.js
    useDebounce.js
  lib/ or utils/      # Utility functions
    formatDate.js
    api.js
  constants/          # Constants and config
    routes.js
    theme.js
```

### Component Internal Structure
```jsx
// Recommended ordering within a component file:

// 1. Imports (React, external libs, internal modules, styles)
import { useState, useEffect } from 'react';
import { useForm } from '@inertiajs/react';
import Button from '../components/Button';

// 2. Constants (outside component)
const MAX_ITEMS = 10;

// 3. Component definition
export default function ItemList({ items, onSelect }) {
  // 3a. Hooks (state, refs, context, custom hooks)
  const [filter, setFilter] = useState('');
  const listRef = useRef(null);

  // 3b. Derived state / computed values
  const filteredItems = items.filter(i => i.name.includes(filter));

  // 3c. Effects
  useEffect(() => {
    // ...
  }, [filter]);

  // 3d. Event handlers
  const handleSelect = (item) => {
    onSelect(item);
  };

  // 3e. Render
  return (
    <ul ref={listRef}>
      {filteredItems.map(item => (
        <li key={item.id} onClick={() => handleSelect(item)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}
```

---

## State Management Patterns

### Unnecessary State
```jsx
// BAD - Derived state stored in useState
function ItemList({ items }) {
  const [filteredItems, setFilteredItems] = useState(items);
  const [filter, setFilter] = useState('');

  useEffect(() => {
    setFilteredItems(items.filter(i => i.name.includes(filter)));
  }, [items, filter]);  // useEffect to sync state = code smell

  return <List items={filteredItems} />;
}

// GOOD - Derive during render
function ItemList({ items }) {
  const [filter, setFilter] = useState('');
  const filteredItems = items.filter(i => i.name.includes(filter));

  return <List items={filteredItems} />;
}
```

### Direct State Mutation
```jsx
// BAD - Mutating state directly
const [items, setItems] = useState([1, 2, 3]);
items.push(4);          // Mutating array
setItems(items);         // Same reference, won't re-render

const [user, setUser] = useState({ name: 'Alice' });
user.name = 'Bob';       // Mutating object
setUser(user);

// GOOD - Immutable updates
setItems([...items, 4]);
setItems(prev => [...prev, 4]);

setUser({ ...user, name: 'Bob' });
setUser(prev => ({ ...prev, name: 'Bob' }));
```

#### Grep Patterns:
- `state\.push\(` or `state\.splice\(` - Array mutation
- `state\.\w+\s*=\s*` - Object property mutation (after a useState call)

---

## Key Prop Usage

```jsx
// BAD - Index as key (problematic for reorderable lists)
{items.map((item, index) => (
  <ListItem key={index} item={item} />
))}

// BAD - Missing key
{items.map(item => (
  <ListItem item={item} />
))}

// GOOD - Stable unique ID as key
{items.map(item => (
  <ListItem key={item.id} item={item} />
))}
```

#### Grep Patterns:
- `key=\{.*index` or `key=\{i\}` or `key=\{idx\}` - Index as key
- `\.map\(` in JSX — check that each mapped element has a `key` prop

---

## Effect Patterns

### Missing Cleanup
```jsx
// BAD - No cleanup for subscriptions/timers
useEffect(() => {
  const interval = setInterval(fetchData, 5000);
  // Missing return cleanup!
}, []);

// BAD - No cleanup for event listeners
useEffect(() => {
  window.addEventListener('resize', handleResize);
  // Missing cleanup!
}, []);

// GOOD - Cleanup in return
useEffect(() => {
  const interval = setInterval(fetchData, 5000);
  return () => clearInterval(interval);
}, []);

useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

#### Grep Patterns:
- `setInterval` inside `useEffect` — check for cleanup
- `addEventListener` inside `useEffect` — check for `removeEventListener` cleanup
- `subscribe` inside `useEffect` — check for unsubscribe cleanup

### Unnecessary Effects
```jsx
// BAD - Effect to transform props (derive instead)
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// GOOD - Derive during render
const fullName = `${firstName} ${lastName}`;

// BAD - Effect to reset state on prop change
useEffect(() => {
  setSelectedItem(null);
}, [items]);

// GOOD - Use key to reset component
<ItemPicker key={listId} items={items} />
```

---

## Common Anti-Patterns

### 1. God Components
Components >300 lines with too many responsibilities. Should be split into smaller, focused components.

### 2. Prop Drilling
Passing props through 3+ levels of components. Consider:
- React Context for widely-shared state
- Composition (children prop, render props)
- State management libraries for complex cases

### 3. Inline Functions in JSX (Performance)
```jsx
// POTENTIAL ISSUE - New function every render (matters in lists)
{items.map(item => (
  <Button onClick={() => handleClick(item.id)} />  // New fn each render
))}

// BETTER for large lists - Memoized or component extraction
const ItemButton = React.memo(({ id, onClick }) => (
  <Button onClick={() => onClick(id)} />
));
```

### 4. Stale Closures
```jsx
// BAD - Stale state in effect
useEffect(() => {
  const interval = setInterval(() => {
    setCount(count + 1);  // Always uses initial count value!
  }, 1000);
  return () => clearInterval(interval);
}, []);  // Empty deps = stale closure

// GOOD - Use functional update
useEffect(() => {
  const interval = setInterval(() => {
    setCount(prev => prev + 1);  // Always uses latest value
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

### 5. Direct DOM Manipulation
```jsx
// BAD - Direct DOM access in React
document.getElementById('myElement').style.display = 'none';
document.querySelector('.modal').classList.add('open');

// GOOD - Use React state and refs
const [isVisible, setIsVisible] = useState(true);
const elementRef = useRef(null);
```

#### Grep Patterns:
- `document\.getElementById` - Direct DOM access
- `document\.querySelector` - Direct DOM query
- `document\.getElementsBy` - Direct DOM query
- `\.classList\.` - Direct class manipulation

---

## CSS / Tailwind Standards

### Tailwind Consistency
```jsx
// BAD - Mixing inline styles with Tailwind
<div className="flex items-center" style={{ marginTop: '16px' }}>

// GOOD - All Tailwind
<div className="flex items-center mt-4">

// BAD - @apply overuse (defeats purpose of utility-first)
// In CSS:
.my-button {
  @apply px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 focus:ring-2;
}

// GOOD - Use className directly, or create component
function Button({ children, ...props }) {
  return (
    <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 focus:ring-2" {...props}>
      {children}
    </button>
  );
}
```

### Design Token Usage
```jsx
// BAD - Hardcoded colors (not using design tokens)
<div className="bg-[#FF582A]">
<div style={{ color: '#23201F' }}>

// GOOD - Using design tokens
<div className="bg-mm-orange-200">
<div className="text-mm-vintage-black">
```

#### Grep Patterns:
- `style=\{\{` in projects using Tailwind (should prefer utility classes)
- `bg-\[#` or `text-\[#` - Arbitrary Tailwind values (should use design tokens)
- `@apply` in CSS files (check for overuse)

---

## Import Organization

### Recommended Order
```jsx
// 1. React/framework imports
import { useState, useEffect } from 'react';
import { usePage, router } from '@inertiajs/react';

// 2. External library imports
import { useDropzone } from 'react-dropzone';
import WaveSurfer from 'wavesurfer.js';

// 3. Internal components/hooks/utils
import Button from '../components/Button';
import { useAuth } from '../hooks/useAuth';
import { formatDate } from '../lib/formatDate';

// 4. Assets/styles
import logoIcon from '../icons/logo.svg';
import './styles.css';
```

---

## Component Size Thresholds

| Metric | Good | Flag | Investigate |
|--------|------|------|-------------|
| Lines per component | <150 | 150-300 | >300 |
| useState calls | 1-3 | 4-5 | >5 |
| useEffect calls | 0-1 | 2-3 | >3 |
| Props count | 1-5 | 6-8 | >8 |
| JSX nesting depth | 1-4 | 5-7 | >7 |
