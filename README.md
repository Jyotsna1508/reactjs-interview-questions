# reactjs-interview-questions
Real interview questions asked for senior level in react with answers based on my experience and market trend(coding questions also included)

1- Let’s walk through a real scenario and the exact questions it triggers 👇
🧩 The Scenario (Parent → Child Props)

function Parent() {
 const [count, setCount] = React.useState(0);
 const handleClick = () => {
 setCount(count + 1);
 };
 const styles = {
 padding: "10px",
 backgroundColor: "lightblue",
 };
 return (
 <Child
 count={count}
 onClick​={handleClick}
 styles={styles}
 />
 );
}
const Child = React.memo(({ count, onClick, styles }) => {
 console.log("Child rendered");
 return (
 <button style={styles} onClick​={onClick}>
 Count: {count}
 </button>
 );
});
Now the interview starts 😄
2️⃣ Why are changes happening at both the component level and handleClick during rendering?
Because:
handleClick updates state
State update triggers Parent re-render
Parent re-render recreates everything inside it
➡️ React doesn’t re-run only handleClick.
 It re-executes the entire Parent function.
3️⃣ What happens when styles and onClick are passed from parent to child?
Every render creates:
A new function (handleClick)
A new object (styles)
Even if the values look the same, the references are different.
So Child receives new props on every render.
4️⃣ How does React handle this internally?
React uses shallow comparison for props:

prevProps.onClick !== nextProps.onClick // true
prevProps.styles !== nextProps.styles // true
➡️ React thinks props changed
 ➡️ React.memo cannot prevent re-render
 ➡️ Child re-renders anyway
5️⃣ What problems can occur behind the scenes even if the UI works?
⚠️ This is where senior-level answers matter:
Unnecessary child re-renders
Memoization becomes useless
Performance issues in large trees
Hard-to-track rendering bugs
Expensive calculations running again
It works… but at a hidden cost.
6️⃣ Which React principles are being impacted here?
❌ Referential stability
❌ Efficient reconciliation
❌ Predictable render optimization
❌ Proper memoization usage
7️⃣ How do we fix this and improve performance?
By stabilizing references 👇

function Parent() {
 const [count, setCount] = React.useState(0);

 const handleClick = React.useCallback(() => {
 setCount(prev => prev + 1);
 }, []);

 const styles = React.useMemo(() => ({
 padding: "10px",
 backgroundColor: "lightblue",
 }), []);

 return (
 <Child
 count={count}
 onClick​={handleClick}
 styles={styles}
 />
 );
}


2- create a todo list app 
function App() {
  const [list, setList] = useState([]);
  const [activity, setActivity] = useState("");
  const handleAddTask = () => {
    if(!activity.trim()) return;
    const hasActivity = list.filter((item)=> item.value === activity);
    if(hasActivity.length) return;
    const id = crypto.randomUUID();
    setList(prev => [...prev, {value: activity, id, isEdit: false}]);
    setActivity("");
  } 
  const handleTaskEdit = (selectedTask) => {
   setList((prev)=> prev.map((item)=> item.id === selectedTask.id ? {...item, isEdit: !item.isEdit}: item))
  };
   const handleTaskDelete = (selectedTask) => {
     setList(list.filter((prev)=> prev.id !== selectedTask.id))
  }
  
  const handleTaskSave = (value, id)=> {
    setList(prev => prev.map((item) => item.id === id? {...item, value, isEdit: false} : item));
  } 
  return (
    <div>
      <div>
        <input type="text" value={activity} onChange={(e)=> setActivity(e.target.value)}></input>
        <button type="submit" onClick={handleAddTask}>Add task</button>
      </div>
      <ol>{list.map((item) => (
      <li key={item.id}>{item.isEdit ? <input type="text" value={item.value}  onChange={(e) => {
    const newVal = e.target.value;
    setList(prev =>
      prev.map(i => i.id === item.id ? { ...i, value: newVal } : i)
    );
  }} onBlur={(e)=> handleTaskSave(e.target.value, item.id)}/> : item.value} 
       <button type="button" onClick={()=> handleTaskEdit(item)}>Edit</button>
       <button type="button" onClick={()=> handleTaskDelete(item)}>Delete</button>
       </li>
      ))}</ol>
    </div>
  )
}

export default App;

3- create a custom hook tha runs on second mount and data update and also handles the cleanup
import React, { useState } from 'react';
import useUpdateEffect from './useUpdateEffect';

function App() {
  const [count, setCount] = useState(0);

  useUpdateEffect(() => {
    console.log('This runs on 2nd mount and when count updates:', count);

    // Optional cleanup
    return () => {
      console.log('Cleanup for count:', count);
    };
  }, [count]);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => setCount(prev => prev + 1)}>Increment</button>
    </div>
  );
}

export default App;
4. How to manage asynchronous tasks?
Use async/await for cleaner flow, handle failures with try/catch, cancel unnecessary requests using AbortController, and optimize frequent calls with debouncing or throttling.

5. How to improve sluggish behavior during initial app load?
Reduce the initial bundle using code splitting, lazy load non-critical components, defer heavy scripts, enable caching, and use skeleton loaders to improve perceived performance.

6. How does lazy loading work?
Lazy loading delays loading of components, images, or data until they are actually needed (for example, when a component enters the viewport). This reduces initial load time, lowers memory usage, and improves overall app responsiveness.

7. Performance optimization: API call with a timer showing seconds
Execute the API call asynchronously while updating a timer state using setInterval or requestAnimationFrame, ensuring UI updates remain smooth and non-blocking.

8. How to persist data?
Persist client data using localStorage, sessionStorage, or IndexedDB; for global state, use Redux Persist to rehydrate the Redux store across page reloads; use backend storage for long-term or shared data.

9. Difference between custom hook and normal function
A custom hook encapsulates reusable, stateful React logic using hooks, while a normal function is stateless, reusable utility logic that cannot access React lifecycle or hooks.

10. Scenario: You’re implementing infinite scroll and want to avoid scroll event performance issues.
Answer: Use IntersectionObserver to detect when the last element enters the viewport instead of listening to scroll events.

11. Scenario: A user quickly switches between tabs and multiple API calls are fired unnecessarily.
Answer: Use AbortController to cancel previous in-flight requests when a new request starts.

12. Scenario: Images should load only when they are about to appear on screen.
Answer: Combine IntersectionObserver + lazy loading to defer image downloads and improve LCP.

13. Scenario: A component unmounts but an API response still tries to update state.
Answer: Abort the request using AbortController inside useEffect cleanup to prevent memory leaks.

14. Scenario: You want to pause background data fetching when a tab is inactive.
Answer: Use the Page Visibility API along with React effects to stop unnecessary re-renders and network calls.

15. Scenario: You need to avoid race conditions when multiple search queries are triggered rapidly.
Answer: Cancel older requests with AbortController so only the latest response updates the UI.

1️6. How do you structure a large-scale React application?
In interviews, I usually explain using a feature-based architecture:
 • Group code by features, not by file type
 • Keep shared components, hooks, and utilities in a common layer
 • Separate UI, business logic, and data access
This improves scalability, maintainability, and team collaboration.

17. How do you handle error states, retries, partial failures, and loading strategies?
A common expectation in interviews:
 • Use Error Boundaries for render-time failures
 • Show granular loading states instead of global spinners
 • Implement retry logic for recoverable API failures
 • Handle partial failures by rendering available data and fallback UI
This leads to better resilience and user experience.

18. What web accessibility fundamentals should a React developer know?
Interviewers often look for WCAG-aware decisions, such as:
 • Semantic HTML (button, nav, header)
 • Keyboard navigation & focus management
 • ARIA attributes only when necessary
 • Proper contrast ratios and screen-reader support
Accessibility is not optional—it’s part of performance and UX.

1️9. What is Concurrent Rendering, and how do transitions improve UX?
Concurrent Rendering allows React to interrupt rendering work to keep the app responsive. Transitions mark non-urgent updates so urgent UI interactions (typing, clicking) stay smooth.

🔹 useTransition vs startTransition:
 • useTransition: A hook that provides a isPending flag to show loading states during transitions. Used inside components.
 • startTransition: A function used when you don’t need pending state (e.g., outside React components or for simple state updates).

20. What is hydration in SSR, and common Next.js hydration issues?
Hydration is when React attaches event listeners to server-rendered HTML on the client. Issues happen when server and client output differ (using window, random values, locale-based formatting, or conditional rendering).

21. How do Error Boundaries work, and what can’t they catch?
Error Boundaries catch errors during rendering, lifecycle methods, and constructors of child components. They do not catch errors in event handlers, async code, or server-side rendering.

22. CSR vs SSR vs SSG — when to use what and why?
 • CSR: Best for highly interactive apps (dashboards, internal tools).
 • SSR: Best for SEO + frequently changing data (e-commerce, auth-based pages).
 • SSG: Best for static, high-performance content (blogs, landing pages).

24.  Scenario: API is called on every keystroke in a search box
❓ How will you optimize it?
 ✅ Answer:
 Use debounce (via setTimeout or lodash), and trigger API calls inside useEffect.
 Also cancel previous requests using AbortController.
📌 Keywords: useEffect, debouncing, API optimization

25. Scenario: Component re-renders even when props don’t change
❓ How do you fix unnecessary re-renders?
 ✅ Answer:
 Use:
React.memo for components
useCallback for functions
useMemo for expensive calculations
📌 Prevents reference changes causing re-renders

26. Scenario: Same API is called in multiple components
❓ How will you avoid repeated API calls?
 ✅ Answer:
Lift state up to parent
Use Context API or Redux / Zustand
Or cache data using React Query / SWR
📌 Shows real-world state management knowledge

27. Scenario: User navigates quickly, old API calls should cancel
❓ How do you cancel previous requests?
 ✅ Answer:
 Use AbortController inside useEffect cleanup function.
📌 Prevents race conditions & memory leaks

28. Scenario 5: Large list (5k+ items) causes UI lag
❓ How will you optimize performance?
 ✅ Answer:
Use list virtualization (react-window, react-virtualized)
Memoize row components
Avoid inline functions in render
📌 Very common performance question



