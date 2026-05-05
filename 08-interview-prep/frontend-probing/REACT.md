Typical frontend / React interviews usually probe **three things**:

1. Can you explain how the browser, JavaScript, and React actually work?
2. Can you build and debug real UI features?
3. Can you make sensible engineering decisions, not just write syntax?

Below is a practical breakdown.

---

# 1. HTML, CSS, and Browser Fundamentals

Interviewers often check whether the candidate understands the platform before React.

## Common questions

**HTML**

- What is semantic HTML?
- Why use `<button>` instead of a clickable `<div>`?
- What is the difference between block and inline elements?
- What are forms, labels, and input accessibility concerns?
- How does the browser parse and render HTML?

**CSS**

- Explain the box model.
- What is the difference between `relative`, `absolute`, `fixed`, and `sticky` positioning?
- How does flexbox work?
- When would you use grid instead of flexbox?
- How does CSS specificity work?
- What causes layout shift?
- How would you make a responsive layout?
- How would you center a div?
- What are pseudo-classes like `:hover`, `:focus`, `:nth-child`?

**Browser**

- What happens when you type a URL and press Enter?
- What is the DOM?
- What is event bubbling?
- What is the difference between `localStorage`, `sessionStorage`, and cookies?
- What are CORS errors?
- How does the browser render a page?

## What they are really testing

They want to know whether the candidate can build usable interfaces without relying blindly on libraries.

A strong candidate can explain that React sits **on top of browser fundamentals**, not instead of them.

---

# 2. JavaScript Fundamentals

This is usually the biggest filter for junior to mid-level frontend roles.

## Common questions

**Variables and types**

- What is the difference between `let`, `const`, and `var`?
- What are primitive and reference types?
- What is the difference between `null` and `undefined`?
- What is truthy and falsy?

**Functions**

- What is a callback function?
- What is the difference between function declarations and arrow functions?
- What is closure?
- What is scope?
- What is hoisting?

**Objects and arrays**

- How do you copy an object or array?
- What is destructuring?
- What is the spread operator?
- What is the difference between shallow copy and deep copy?
- How do `map`, `filter`, `reduce`, `find`, and `forEach` differ?

**Async JavaScript**

- What is a Promise?
- What is `async/await`?
- What is the event loop?
- What is the difference between synchronous and asynchronous code?
- How would you handle errors in async code?
- What is the difference between `Promise.all`, `Promise.race`, and `Promise.allSettled`?

## Practical probing examples

They may ask:

```js
const user = { name: "Ava", age: 25 };
const copy = user;
copy.name = "Ben";

console.log(user.name);
```

Expected understanding: `copy` points to the same object, so `user.name` becomes `"Ben"`.

Or:

```js
console.log("A");

setTimeout(() => {
  console.log("B");
}, 0);

console.log("C");
```

Expected output:

```txt
A
C
B
```

They are testing async behavior and the event loop.

---

# 3. React Core Concepts

This is where candidates often know the syntax but not the model.

## Common questions

**Components**

- What is a React component?
- What is the difference between props and state?
- What makes a component reusable?
- What is component composition?
- What is the difference between controlled and uncontrolled components?

**JSX**

- What is JSX?
- Why do we use `className` instead of `class`?
- Why do components need to return one parent element?
- Can you use JavaScript inside JSX?

**State**

- What is `useState`?
- Why should state not be mutated directly?
- What happens when state changes?
- Why is state update sometimes asynchronous?
- When would you lift state up?

**Effects**

- What is `useEffect` for?
- What is a dependency array?
- What happens if the dependency array is empty?
- What happens if there is no dependency array?
- What is cleanup in `useEffect`?
- Should you use `useEffect` for everything?

**Rendering**

- What causes a React component to re-render?
- What is the virtual DOM?
- What is reconciliation?
- Why do lists need keys?
- Why should keys not usually be array indexes?

## Very common probing question

```jsx
const [count, setCount] = useState(0);

function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
}
```

Question: What will `count` become after one click?

Expected answer: usually `1`, not `3`, because each update uses the same captured `count` value.

Better version:

```jsx
setCount((prev) => prev + 1);
setCount((prev) => prev + 1);
setCount((prev) => prev + 1);
```

Now it becomes `3`.

They are testing whether the candidate understands state updates, batching, and functional updates.

---

# 4. React Hooks

Hooks are often used to reveal whether the candidate understands React’s lifecycle-like behavior.

## Common questions

- What are hooks?
- Why must hooks be called at the top level?
- Why can’t hooks be called conditionally?
- What is `useState`?
- What is `useEffect`?
- What is `useRef`?
- What is `useMemo`?
- What is `useCallback`?
- What is a custom hook?
- When would you create a custom hook?

## Probing deeper

**`useRef`**

- How is `useRef` different from `useState`?
- Why does changing a ref not cause a re-render?
- When would you use a ref?

**`useMemo`**

- What problem does `useMemo` solve?
- Can overusing `useMemo` make the code worse?
- What is memoization?

**`useCallback`**

- Why would a function be recreated on every render?
- When does `useCallback` actually help?
- How does it relate to child component re-rendering?

A good candidate should not say “useMemo and useCallback make everything faster.” A stronger answer is: they help in specific cases, but they also add complexity and should be used when there is a real performance or referential equality issue.

---

# 5. Data Fetching and API Integration

This is very common because real frontend apps need backend data.

## Common questions

- How do you fetch data in React?
- Where should data fetching happen?
- How do you handle loading state?
- How do you handle error state?
- How do you avoid fetching data repeatedly by mistake?
- How do you cancel or ignore stale requests?
- What is the difference between REST and GraphQL?
- What are HTTP methods like GET, POST, PUT, PATCH, DELETE?
- What are common HTTP status codes?
- What is JSON?
- How do you send authentication tokens?

## Example practical task

“Build a component that fetches users from an API and displays loading, error, and success states.”

They are looking for something like:

```jsx
function UsersList() {
  const [users, setUsers] = useState([]);
  const [status, setStatus] = useState("idle");
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchUsers() {
      try {
        setStatus("loading");
        const response = await fetch("/api/users");

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data = await response.json();
        setUsers(data);
        setStatus("success");
      } catch (err) {
        setError(err.message);
        setStatus("error");
      }
    }

    fetchUsers();
  }, []);

  if (status === "loading") return <p>Loading...</p>;
  if (status === "error") return <p>{error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

They may then probe:

- What happens if the component unmounts before the fetch finishes?
- What if the API returns a 500?
- What if the API returns an empty array?
- How would you refetch?
- How would you cache the result?
- Would you use React Query / TanStack Query?

---

# 6. State Management

For junior roles, local state is often enough. For mid-level roles, they may ask about app-level state.

## Common questions

- When should state live inside a component?
- When should state be lifted up?
- What is prop drilling?
- What is Context API?
- When would you use Redux, Zustand, Jotai, or another state library?
- What kind of state should be global?
- What is the difference between server state and client state?

## Important distinction

A strong frontend developer understands this:

**Client state** means UI-owned state, such as:

- modal open/closed
- selected tab
- form input value
- theme preference

**Server state** means backend-owned data, such as:

- user profile
- products
- orders
- search results

For server state, tools like TanStack Query are often better than manually managing everything with `useState` and `useEffect`.

---

# 7. Forms and Validation

Forms are a common practical interview topic because they reveal real app-building ability.

## Common questions

- What is a controlled input?
- What is an uncontrolled input?
- How do you handle form submission?
- How do you validate user input?
- How do you show form errors?
- How do you prevent default form behavior?
- How would you handle a large form?
- What are libraries like React Hook Form and Zod used for?

## Example probing

```jsx
<input value={name} onChange={(e) => setName(e.target.value)} />
```

They may ask:

- Why is this called a controlled component?
- What happens if `value` is undefined?
- Why do we use `e.target.value`?
- What does `onChange` actually listen for?

---

# 8. Routing

For React roles, routing is often tested through React Router or Next.js routing.

## Common questions

- What is client-side routing?
- How is it different from traditional page navigation?
- What is React Router?
- What is a dynamic route?
- How do you read URL params?
- How do you protect a route?
- How do you redirect a user after login?
- How do query parameters work?

## Example

They may ask:

“Build a product details page where the URL is `/products/:id`.”

They are testing whether the candidate understands that the URL can drive application state.

---

# 9. Performance

Performance questions are more common for mid-level and senior frontend roles.

## Common questions

- What causes unnecessary re-renders?
- How do you optimize a slow React component?
- What is lazy loading?
- What is code splitting?
- What is debouncing?
- What is throttling?
- What is memoization?
- What is `React.memo`?
- What is the difference between `useMemo`, `useCallback`, and `React.memo`?
- How do you optimize large lists?
- What is virtualization?

## Good answer pattern

A good candidate does not immediately say “use memoization.”

A better answer:

1. Measure first.
2. Identify the bottleneck.
3. Reduce unnecessary work.
4. Apply memoization or virtualization only where useful.
5. Re-measure.

---

# 10. Accessibility

This is increasingly important.

## Common questions

- What is web accessibility?
- Why is semantic HTML important for accessibility?
- What is keyboard navigation?
- What is focus management?
- What are ARIA attributes?
- When should you avoid ARIA?
- How do labels help screen readers?
- How would you make a modal accessible?
- How do you make a button accessible?

## Strong answer

Use semantic HTML first. Use ARIA only when native HTML cannot express the behavior clearly.

For example, prefer:

```html
<button>Submit</button>
```

over:

```html
<div role="button">Submit</div>
```

unless there is a strong reason.

---

# 11. Testing

Testing questions vary by company, but frontend developers are often asked about confidence and reliability.

## Common questions

- What is unit testing?
- What is integration testing?
- What is end-to-end testing?
- What would you test in a React component?
- What is React Testing Library?
- What is Jest or Vitest?
- What is Playwright or Cypress?
- Should you test implementation details?
- How would you test a form?
- How would you test API loading and error states?

## Good principle

Test behavior, not implementation details.

For example, test:

- user sees loading message
- user can type into a form
- error appears when input is invalid
- submit button calls expected behavior

Not:

- internal state variable changed from `false` to `true`

---

# 12. Git, Collaboration, and Engineering Workflow

Especially for real-world roles, interviewers may ask how the candidate works with others.

## Common questions

- How do you use Git?
- What is the difference between Git and GitHub?
- What is a pull request?
- What is a merge conflict?
- How do you resolve conflicts?
- What makes a good commit?
- How do you review someone else’s code?
- How do you structure a branch?
- How do you debug a broken build?

## Experience-based probes

- Tell me about a time you had to debug a difficult frontend issue.
- Tell me about a time you received code review feedback.
- Tell me about a time you disagreed with another developer.
- Tell me about a feature you built from scratch.
- Tell me about a mistake you made in production or during a project.

They are testing maturity, not just technical ability.

---

# 13. Architecture and Component Design

This is especially relevant for mid-level roles.

## Common questions

- How do you structure a React project?
- How do you decide when to split a component?
- What makes a component reusable?
- What is the difference between presentational and container components?
- How do you avoid deeply nested prop drilling?
- How do you manage shared logic?
- How do you design a component library?
- How do you handle design consistency?
- How do you organize API calls?

## Practical probe

“Design a shopping cart UI.”

They may expect discussion around:

- product list
- cart state
- quantity changes
- derived total price
- add/remove item behavior
- persistence
- API sync
- optimistic updates
- error handling

A weak candidate jumps straight into code.

A stronger candidate clarifies data shape, state ownership, user flows, and edge cases.

---

# 14. TypeScript

Many frontend roles now expect at least basic TypeScript.

## Common questions

- What problem does TypeScript solve?
- What is the difference between `type` and `interface`?
- What is a union type?
- What is optional property syntax?
- What are generics?
- How do you type React props?
- How do you type event handlers?
- How do you type API responses?
- What is `unknown` vs `any`?

## Example

```tsx
type ButtonProps = {
  label: string;
  onClick: () => void;
  disabled?: boolean;
};
```

They may ask:

- Why is `disabled` optional?
- What does `() => void` mean?
- What happens if the parent passes the wrong type?

---

# 15. Security Basics

Frontend developers are not usually expected to be security experts, but they should know the basics.

## Common questions

- What is XSS?
- What is CSRF?
- Why is `dangerouslySetInnerHTML` dangerous?
- Where should you store authentication tokens?
- What are HTTP-only cookies?
- What is CORS?
- Why should frontend validation not be the only validation?
- How do you avoid exposing secrets in frontend code?

## Important point

Anything shipped to the browser is visible to users. So API keys, secrets, private credentials, and sensitive business logic should not live in frontend code.

---

# 16. Next.js / Framework Questions

For Next.js roles, they may ask:

- What is the difference between React and Next.js?
- What is server-side rendering?
- What is static site generation?
- What is client-side rendering?
- What are server components?
- What are client components?
- When do you use `"use client"`?
- What are API routes?
- How does routing work in Next.js?
- How do you handle metadata and SEO?
- How do you fetch data in Next.js?

The real test is whether the candidate understands that Next.js adds routing, rendering strategies, backend-like capabilities, and optimization on top of React.

---

# 17. Common Live Coding Tasks

Frontend interviews often include small implementation tasks.

## Typical tasks

- Build a counter.
- Build a todo list.
- Build a search filter.
- Build tabs.
- Build an accordion.
- Build a modal.
- Build a dropdown.
- Fetch and display API data.
- Build pagination.
- Build infinite scroll.
- Build a form with validation.
- Build a shopping cart.
- Build a table with sorting/filtering.
- Convert static HTML/CSS into React components.
- Debug a broken component.
- Refactor repeated code into reusable components.

## What interviewers watch

They usually care about:

- Can the candidate explain their thinking?
- Do they manage state correctly?
- Do they handle edge cases?
- Do they write readable code?
- Do they test manually?
- Do they debug calmly?
- Do they ask good clarifying questions?

---

# 18. Strong Probing Questions by Topic

Here are good interviewer-style probes.

## React state

- Where should this state live?
- Is this state derived or stored?
- What happens if the parent re-renders?
- Could this be computed instead of stored?
- What happens if two updates happen quickly?

## useEffect

- Why is this effect needed?
- Could this logic run inside an event handler instead?
- What should go in the dependency array?
- What happens if this dependency is missing?
- Does this effect need cleanup?

## Components

- Is this component too large?
- What props should this component receive?
- Should this be reusable or feature-specific?
- Is this component responsible for too many things?

## API

- What should happen while the data is loading?
- What should happen if the request fails?
- What should happen if the data is empty?
- What should happen if the user retries?
- How would you prevent duplicate requests?

## Performance

- What part of this is expensive?
- How do you know it is slow?
- Would memoization actually help here?
- What would you measure?

## Accessibility

- Can this be used with a keyboard?
- Does this element have the right semantic meaning?
- Can a screen reader understand it?
- What happens to focus when the modal opens or closes?

---

# 19. What Junior, Mid-Level, and Senior Candidates Are Usually Judged On

## Junior frontend developer

Expected:

- Basic HTML/CSS/JS
- Basic React components
- `useState`, `useEffect`, props
- simple forms
- basic API fetching
- simple debugging
- willingness to learn

Not expected:

- deep architecture
- advanced performance optimization
- complex state management
- deep TypeScript mastery

## Mid-level frontend developer

Expected:

- good React mental model
- component design
- API integration
- error/loading states
- reusable hooks
- routing
- testing basics
- practical performance awareness
- TypeScript comfort
- ability to work independently

## Senior frontend developer

Expected:

- architecture decisions
- trade-off reasoning
- design systems
- performance diagnosis
- accessibility maturity
- mentoring
- testing strategy
- security awareness
- frontend/backend boundary understanding
- product thinking

---

# 20. The Best Interview Questions Reveal Experience

The best questions are not only:

“What is `useEffect`?”

Better questions are:

“Tell me about a time you used `useEffect` incorrectly. What happened?”

Or:

“Show me how you would fetch data here, then explain what could go wrong.”

Or:

“This component works, but users report it feels slow. How would you investigate?”

These questions reveal whether someone has actually built, broken, debugged, and improved real frontend applications.

---

# Useful Interview Evaluation Lens

For frontend / React candidates, I would evaluate across these five areas:

| Area                   | What to look for                           |
| ---------------------- | ------------------------------------------ |
| Fundamentals           | HTML, CSS, JS, browser knowledge           |
| React model            | props, state, rendering, effects, hooks    |
| Practical delivery     | forms, APIs, routing, loading/error states |
| Engineering maturity   | debugging, Git, testing, architecture      |
| Product/user awareness | accessibility, UX, performance, edge cases |

The biggest red flag is when someone can repeat definitions but cannot explain what happens in a real component when state changes, data loads, or the user interacts with the UI.
