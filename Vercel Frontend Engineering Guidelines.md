# Vercel Frontend Engineering Guidelines

**Version 1.0.0**
Vercel Engineering
January 2026

> A comprehensive guide to building high-performance, maintainable React and Next.js applications. This document combines React performance best practices, composition patterns, and web interface guidelines into a single authoritative reference.

---

## Table of Contents

### Part 1: React Performance
- [Chapter 1: Eliminating Waterfalls](#chapter-1-eliminating-waterfalls)
- [Chapter 2: Bundle Size Optimization](#chapter-2-bundle-size-optimization)
- [Chapter 3: Server-Side Performance](#chapter-3-server-side-performance)
- [Chapter 4: Client-Side Data Fetching](#chapter-4-client-side-data-fetching)
- [Chapter 5: Re-render Optimization](#chapter-5-re-render-optimization)
- [Chapter 6: Rendering Performance](#chapter-6-rendering-performance)
- [Chapter 7: JavaScript Performance](#chapter-7-javascript-performance)
- [Chapter 8: Advanced Patterns](#chapter-8-advanced-patterns)

### Part 2: Composition Patterns
- [Chapter 9: Component Architecture](#chapter-9-component-architecture)
- [Chapter 10: State Management](#chapter-10-state-management)
- [Chapter 11: Implementation Patterns](#chapter-11-implementation-patterns)
- [Chapter 12: React 19 APIs](#chapter-12-react-19-apis)

### Part 3: Web Interface Guidelines
- [Chapter 13: Accessibility](#chapter-13-accessibility)
- [Chapter 14: Focus States](#chapter-14-focus-states)
- [Chapter 15: Forms](#chapter-15-forms)
- [Chapter 16: Animation](#chapter-16-animation)
- [Chapter 17: Typography](#chapter-17-typography)
- [Chapter 18: Content Handling](#chapter-18-content-handling)
- [Chapter 19: Images](#chapter-19-images)
- [Chapter 20: Performance](#chapter-20-performance)
- [Chapter 21: Navigation](#chapter-21-navigation)
- [Chapter 22: Touch and Interaction](#chapter-22-touch-and-interaction)
- [Chapter 23: Dark Mode](#chapter-23-dark-mode)
- [Chapter 24: Locale and i18n](#chapter-24-locale-and-i18n)
- [Chapter 25: Hydration Safety](#chapter-25-hydration-safety)
- [Chapter 26: Anti-patterns Checklist](#chapter-26-anti-patterns-checklist)

---

# Part 1: React Performance

Performance optimization guide for React and Next.js applications. Contains 40+ rules across 8 categories, prioritized by impact from critical to incremental.

---

## Chapter 1: Eliminating Waterfalls

**Impact: CRITICAL**

Waterfalls are the #1 performance killer. Each sequential await adds full network latency.

### 1.1 Defer Await Until Needed

Move `await` operations into the branches where they're actually used.

**Incorrect: blocks both branches**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  const userData = await fetchUserData(userId)
  if (skipProcessing) {
    return { skipped: true }
  }
  return processUserData(userData)
}
```

**Correct: only blocks when needed**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  if (skipProcessing) {
    return { skipped: true }
  }
  const userData = await fetchUserData(userId)
  return processUserData(userData)
}
```

### 1.2 Dependency-Based Parallelization

**Impact: CRITICAL (2-10x improvement)**

```typescript
import { all } from 'better-all'

const { user, config, profile } = await all({
  async user() { return fetchUser() },
  async config() { return fetchConfig() },
  async profile() {
    return fetchProfile((await this.$.user).id)
  }
})
```

### 1.3 Prevent Waterfall Chains in API Routes

Start independent operations immediately.

**Correct: auth and config start immediately**

```typescript
export async function GET(request: Request) {
  const sessionPromise = auth()
  const configPromise = fetchConfig()
  const session = await sessionPromise
  const [config, data] = await Promise.all([
    configPromise,
    fetchData(session.user.id)
  ])
  return Response.json({ data, config })
}
```

### 1.4 Promise.all() for Independent Operations

**Impact: CRITICAL (2-10x improvement)**

```typescript
const [user, posts, comments] = await Promise.all([
  fetchUser(),
  fetchPosts(),
  fetchComments()
])
```

### 1.5 Strategic Suspense Boundaries

**Impact: HIGH (faster initial paint)**

```tsx
function Page() {
  return (
    <div>
      <div>Sidebar</div>
      <Suspense fallback={<Skeleton />}>
        <DataDisplay />
      </Suspense>
      <div>Footer</div>
    </div>
  )
}

async function DataDisplay() {
  const data = await fetchData()
  return <div>{data.content}</div>
}
```

---

## Chapter 2: Bundle Size Optimization

**Impact: CRITICAL**

### 2.1 Avoid Barrel File Imports

**Impact: CRITICAL (200-800ms import cost)**

```tsx
// Incorrect
import { Check, X, Menu } from 'lucide-react'

// Correct
import Check from 'lucide-react/dist/esm/icons/check'
import X from 'lucide-react/dist/esm/icons/x'
```

### 2.2 Conditional Module Loading

```tsx
useEffect(() => {
  if (enabled && !frames && typeof window !== 'undefined') {
    import('./animation-frames.js')
      .then(mod => setFrames(mod.frames))
  }
}, [enabled, frames])
```

### 2.3 Defer Non-Critical Third-Party Libraries

```tsx
import dynamic from 'next/dynamic'

const Analytics = dynamic(
  () => import('@vercel/analytics/react').then(m => m.Analytics),
  { ssr: false }
)
```

### 2.4 Dynamic Imports for Heavy Components

**Impact: CRITICAL (directly affects TTI and LCP)**

```tsx
const MonacoEditor = dynamic(
  () => import('./monaco-editor').then(m => m.MonacoEditor),
  { ssr: false }
)
```

### 2.5 Preload Based on User Intent

```tsx
function EditorButton({ onClick }) {
  const preload = () => {
    if (typeof window !== 'undefined') {
      void import('./monaco-editor')
    }
  }

  return (
    <button onMouseEnter={preload} onFocus={preload} onClick={onClick}>
      Open Editor
    </button>
  )
}
```

---

## Chapter 3: Server-Side Performance

**Impact: HIGH**

### 3.1 Authenticate Server Actions Like API Routes

**Impact: CRITICAL**

```typescript
'use server'

export async function deleteUser(userId: string) {
  const session = await verifySession()
  if (!session) throw new Error('Must be logged in')
  if (session.user.role !== 'admin' && session.user.id !== userId) {
    throw new Error('Cannot delete other users')
  }
  await db.user.delete({ where: { id: userId } })
  return { success: true }
}
```

### 3.2 Minimize Serialization at RSC Boundaries

**Impact: HIGH**

```tsx
// Incorrect: serializes all 50 fields
<Profile user={user} />

// Correct: serializes only 1 field
<Profile name={user.name} />
```

### 3.3 Cross-Request LRU Caching

```typescript
import { LRUCache } from 'lru-cache'

const cache = new LRUCache<string, any>({
  max: 1000,
  ttl: 5 * 60 * 1000
})

export async function getUser(id: string) {
  const cached = cache.get(id)
  if (cached) return cached
  const user = await db.user.findUnique({ where: { id } })
  cache.set(id, user)
  return user
}
```

### 3.4 Parallel Data Fetching with Component Composition

**Impact: CRITICAL**

```tsx
async function Header() {
  const data = await fetchHeader()
  return <div>{data}</div>
}

async function Sidebar() {
  const items = await fetchSidebarItems()
  return <nav>{items.map(renderItem)}</nav>
}

export default function Page() {
  return (
    <div>
      <Header />
      <Sidebar />
    </div>
  )
}
```

### 3.5 Per-Request Deduplication with React.cache()

```typescript
import { cache } from 'react'

export const getCurrentUser = cache(async () => {
  const session = await auth()
  if (!session?.user?.id) return null
  return await db.user.findUnique({ where: { id: session.user.id } })
})
```

### 3.6 Use after() for Non-Blocking Operations

```tsx
import { after } from 'next/server'

export async function POST(request: Request) {
  await updateDatabase(request)
  after(async () => {
    logUserAction({ userAgent: request.headers.get('user-agent') })
  })
  return new Response(JSON.stringify({ status: 'success' }))
}
```

---

## Chapter 4: Client-Side Data Fetching

**Impact: MEDIUM-HIGH**

### 4.1 Use SWR for Automatic Deduplication

```tsx
import useSWR from 'swr'

function UserList() {
  const { data: users } = useSWR('/api/users', fetcher)
}
```

### 4.2 Use Passive Event Listeners for Scrolling Performance

```typescript
document.addEventListener('touchstart', handleTouch, { passive: true })
document.addEventListener('wheel', handleWheel, { passive: true })
```

### 4.3 Version and Minimize localStorage Data

```typescript
const VERSION = 'v2'

function saveConfig(config) {
  try {
    localStorage.setItem(`userConfig:${VERSION}`, JSON.stringify(config))
  } catch {}
}
```

---

## Chapter 5: Re-render Optimization

**Impact: MEDIUM**

### 5.1 Calculate Derived State During Rendering

```tsx
// Incorrect
const [fullName, setFullName] = useState('')
useEffect(() => {
  setFullName(firstName + ' ' + lastName)
}, [firstName, lastName])

// Correct
const fullName = firstName + ' ' + lastName
```

### 5.2 Use Functional setState Updates

```tsx
// Incorrect: requires state as dependency
const addItems = useCallback((newItems) => {
  setItems([...items, ...newItems])
}, [items])

// Correct: stable callbacks
const addItems = useCallback((newItems) => {
  setItems(curr => [...curr, ...newItems])
}, [])
```

### 5.3 Use Lazy State Initialization

```tsx
// Incorrect: runs on every render
const [searchIndex, setSearchIndex] = useState(buildSearchIndex(items))

// Correct: runs only once
const [searchIndex, setSearchIndex] = useState(() => buildSearchIndex(items))
```

### 5.4 Use Transitions for Non-Urgent Updates

```tsx
import { startTransition } from 'react'

const handler = () => {
  startTransition(() => setScrollY(window.scrollY))
}
```

### 5.5 Use useRef for Transient Values

```tsx
function Tracker() {
  const lastXRef = useRef(0)
  const dotRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const onMove = (e) => {
      lastXRef.current = e.clientX
      if (dotRef.current) {
        dotRef.current.style.transform = `translateX(${e.clientX}px)`
      }
    }
    window.addEventListener('mousemove', onMove)
    return () => window.removeEventListener('mousemove', onMove)
  }, [])

  return <div ref={dotRef} />
}
```

---

## Chapter 6: Rendering Performance

**Impact: MEDIUM**

### 6.1 CSS content-visibility for Long Lists

```css
.message-item {
  content-visibility: auto;
  contain-intrinsic-size: 0 80px;
}
```

### 6.2 Hoist Static JSX Elements

```tsx
const loadingSkeleton = (
  <div className="animate-pulse h-20 bg-gray-200" />
)

function Container() {
  return <div>{loading && loadingSkeleton}</div>
}
```

### 6.3 Prevent Hydration Mismatch Without Flickering

```tsx
function ThemeWrapper({ children }) {
  return (
    <>
      <div id="theme-wrapper">{children}</div>
      <script
        dangerouslySetInnerHTML={{
          __html: `(function() {
            var theme = localStorage.getItem('theme') || 'light';
            var el = document.getElementById('theme-wrapper');
            if (el) el.className = theme;
          })();`,
        }}
      />
    </>
  )
}
```

### 6.4 Use Explicit Conditional Rendering

```tsx
// Incorrect: renders "0" when count is 0
{count && <span>{count}</span>}

// Correct
{count > 0 ? <span>{count}</span> : null}
```

---

## Chapter 7: JavaScript Performance

**Impact: LOW-MEDIUM**

### 7.1 Build Index Maps for Repeated Lookups

```typescript
// O(1) per lookup instead of O(n)
const userById = new Map(users.map(u => [u.id, u]))
return orders.map(order => ({
  ...order,
  user: userById.get(order.userId)
}))
```

### 7.2 Combine Multiple Array Iterations

```typescript
const admins = [], testers = [], inactive = []
for (const user of users) {
  if (user.isAdmin) admins.push(user)
  if (user.isTester) testers.push(user)
  if (!user.isActive) inactive.push(user)
}
```

### 7.3 Use toSorted() Instead of sort()

```typescript
// Incorrect: mutates
users.sort((a, b) => a.name.localeCompare(b.name))

// Correct: immutable
users.toSorted((a, b) => a.name.localeCompare(b.name))
```

### 7.4 Use Set/Map for O(1) Lookups

```typescript
const allowedIds = new Set(['a', 'b', 'c'])
items.filter(item => allowedIds.has(item.id))
```

---

## Chapter 8: Advanced Patterns

**Impact: LOW**

### 8.1 Initialize App Once, Not Per Mount

```tsx
let didInit = false

function Comp() {
  useEffect(() => {
    if (didInit) return
    didInit = true
    loadFromStorage()
    checkAuthToken()
  }, [])
}
```

### 8.2 useEffectEvent for Stable Callback Refs

```tsx
import { useEffectEvent } from 'react'

function SearchInput({ onSearch }) {
  const [query, setQuery] = useState('')
  const onSearchEvent = useEffectEvent(onSearch)

  useEffect(() => {
    const timeout = setTimeout(() => onSearchEvent(query), 300)
    return () => clearTimeout(timeout)
  }, [query])
}
```

---

# Part 2: Composition Patterns

Composition patterns for building flexible, maintainable React components.

---

## Chapter 9: Component Architecture

**Impact: HIGH**

### 9.1 Avoid Boolean Prop Proliferation

**Impact: CRITICAL**

```tsx
// Incorrect: exponential complexity
<Composer isThread isEditing={false} channelId='abc' />

// Correct: composition
function ThreadComposer({ channelId }) {
  return (
    <Composer.Frame>
      <Composer.Input />
      <AlsoSendToChannelField id={channelId} />
      <Composer.Footer>
        <Composer.Submit />
      </Composer.Footer>
    </Composer.Frame>
  )
}
```

### 9.2 Use Compound Components

```tsx
const ComposerContext = createContext(null)

function ComposerProvider({ children, state, actions, meta }) {
  return (
    <ComposerContext value={{ state, actions, meta }}>
      {children}
    </ComposerContext>
  )
}

function ComposerInput() {
  const { state, actions: { update }, meta: { inputRef } } = use(ComposerContext)
  return (
    <TextInput
      ref={inputRef}
      value={state.input}
      onChangeText={(text) => update((s) => ({ ...s, input: text }))}
    />
  )
}

const Composer = {
  Provider: ComposerProvider,
  Frame: ComposerFrame,
  Input: ComposerInput,
  Submit: ComposerSubmit,
}
```

---

## Chapter 10: State Management

**Impact: MEDIUM**

### 10.1 Decouple State Management from UI

```tsx
function ChannelProvider({ channelId, children }) {
  const { state, update, submit } = useGlobalChannel(channelId)
  const inputRef = useRef(null)

  return (
    <Composer.Provider
      state={state}
      actions={{ update, submit }}
      meta={{ inputRef }}
    >
      {children}
    </Composer.Provider>
  )
}
```

### 10.2 Define Generic Context Interfaces

```tsx
interface ComposerContextValue {
  state: ComposerState
  actions: ComposerActions
  meta: ComposerMeta
}
```

### 10.3 Lift State into Provider Components

```tsx
function ForwardMessageDialog() {
  return (
    <ForwardMessageProvider>
      <Dialog>
        <ForwardMessageComposer />
        <MessagePreview />
        <DialogActions>
          <ForwardButton />
        </DialogActions>
      </Dialog>
    </ForwardMessageProvider>
  )
}

function ForwardButton() {
  const { actions } = use(Composer.Context)
  return <Button onPress={actions.submit}>Forward</Button>
}
```

---

## Chapter 11: Implementation Patterns

**Impact: MEDIUM**

### 11.1 Create Explicit Component Variants

```tsx
<ThreadComposer channelId="abc" />
<EditMessageComposer messageId="xyz" />
<ForwardMessageComposer messageId="123" />
```

### 11.2 Prefer Composing Children Over Render Props

```tsx
// Correct: compound components with children
<Composer.Frame>
  <CustomHeader />
  <Composer.Input />
  <Composer.Footer>
    <Composer.Formatting />
    <SubmitButton />
  </Composer.Footer>
</Composer.Frame>
```

---

## Chapter 12: React 19 APIs

**Impact: MEDIUM**

### 12.1 ref as a Regular Prop

```tsx
// React 19: no forwardRef needed
function ComposerInput({ ref, ...props }) {
  return <TextInput ref={ref} {...props} />
}
```

### 12.2 use() Instead of useContext()

```tsx
// React 19
const value = use(MyContext)
```

---

# Part 3: Web Interface Guidelines

---

## Chapter 13: Accessibility

### 13.1 Semantic HTML

```tsx
// Correct
<button onClick={handleClick}>Click me</button>

// Incorrect
<div onClick={handleClick}>Click me</div>
```

### 13.2 ARIA Labels

```tsx
<button aria-label="Close dialog">
  <CloseIcon />
</button>
```

### 13.3 Screen Reader Support

```tsx
<div role="status" aria-live="polite">
  {statusMessage}
</div>
```

### 13.4 Keyboard Navigation

```tsx
<div
  role="menuitem"
  tabIndex={0}
  onClick={onClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') onClick()
  }}
>
  {children}
</div>
```

---

## Chapter 14: Focus States

### 14.1 Visible Focus Indicators

```css
button:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}
```

### 14.2 Focus Management

```tsx
useEffect(() => {
  if (isOpen) closeButtonRef.current?.focus()
}, [isOpen])
```

---

## Chapter 15: Forms

### 15.1 Label Association

```tsx
<label htmlFor="email">Email</label>
<input id="email" type="email" />
```

### 15.2 Error Messages

```tsx
<input
  id={id}
  aria-invalid={!!error}
  aria-describedby={error ? errorId : undefined}
/>
{error && <span id={errorId} role="alert">{error}</span>}
```

### 15.3 Input Types

```tsx
<input type="email" inputMode="email" />
<input type="tel" inputMode="tel" />
<input type="number" inputMode="numeric" />
```

---

## Chapter 16: Animation

### 16.1 Respect Motion Preferences

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 16.2 Performance-Conscious Animation

```css
/* Good - GPU accelerated */
.animate-slide {
  transform: translateX(100px);
  transition: transform 200ms ease-out;
}
```

---

## Chapter 17: Typography

### 17.1 Readable Line Length

```css
.prose { max-width: 65ch; }
```

### 17.2 Responsive Font Sizes

```css
html {
  font-size: clamp(16px, 1vw + 14px, 20px);
}
```

---

## Chapter 18: Content Handling

### 18.1 Text Overflow

```css
.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### 18.2 Empty States

```tsx
if (items.length === 0) {
  return (
    <div className="empty-state">
      <EmptyIcon />
      <h3>No items yet</h3>
      <Button>Create Item</Button>
    </div>
  )
}
```

---

## Chapter 19: Images

### 19.1 Next.js Image Optimization

```tsx
import Image from 'next/image'

<Image
  src={user.avatarUrl}
  alt={`${user.name}'s avatar`}
  width={48}
  height={48}
/>
```

### 19.2 Responsive Images

```tsx
<Image
  src="/hero.jpg"
  alt="Hero image"
  fill
  sizes="(max-width: 768px) 100vw, 50vw"
  priority
/>
```

---

## Chapter 20: Performance

### 20.1 Core Web Vitals

- **LCP**: < 2.5s
- **FID**: < 100ms
- **CLS**: < 0.1

### 20.2 Resource Hints

```tsx
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="dns-prefetch" href="https://api.example.com" />
```

---

## Chapter 21: Navigation

### 21.1 Active Link States

```tsx
import { usePathname } from 'next/navigation'

function NavLink({ href, children }) {
  const pathname = usePathname()
  const isActive = pathname === href

  return (
    <Link
      href={href}
      className={isActive ? 'text-blue-600' : 'text-gray-600'}
      aria-current={isActive ? 'page' : undefined}
    >
      {children}
    </Link>
  )
}
```

---

## Chapter 22: Touch and Interaction

### 22.1 Touch Target Size

```css
.button {
  min-height: 44px;
  min-width: 44px;
}
```

### 22.2 Touch Feedback

```css
.button:active {
  transform: scale(0.98);
}

@media (hover: hover) {
  .button:hover {
    background-color: var(--hover-color);
  }
}
```

---

## Chapter 23: Dark Mode

### 23.1 CSS Custom Properties

```css
:root {
  --bg-primary: #ffffff;
  --text-primary: #1a1a1a;
}

[data-theme='dark'] {
  --bg-primary: #1a1a1a;
  --text-primary: #ffffff;
}
```

---

## Chapter 24: Locale and i18n

### 24.1 Date and Number Formatting

```tsx
const formatted = new Intl.DateTimeFormat(locale, {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
}).format(new Date(date))
```

### 24.2 RTL Support

```css
.card {
  margin-inline-start: 1rem;
  padding-inline-end: 1rem;
}
```

---

## Chapter 25: Hydration Safety

### 25.1 Client-Only Content

```tsx
function ClientOnly({ children, fallback = null }) {
  const [mounted, setMounted] = useState(false)
  useEffect(() => setMounted(true), [])
  if (!mounted) return fallback
  return children
}
```

### 25.2 suppressHydrationWarning

```tsx
<time suppressHydrationWarning>
  {new Date().toLocaleTimeString()}
</time>
```

---

## Chapter 26: Anti-patterns Checklist

### UI Anti-patterns
- Using `div` instead of semantic HTML
- Removing focus outlines without alternatives
- Relying solely on color to convey information

### Performance Anti-patterns
- Importing entire libraries
- Not lazy-loading below-the-fold content
- Creating new objects in render without memoization

### Accessibility Anti-patterns
- Missing alt text on meaningful images
- Not associating labels with form inputs
- Not supporting keyboard navigation

### State Management Anti-patterns
- Duplicating state that can be derived
- Not handling loading, error, and success states
- Mutating state directly

---

## References

1. [React Documentation](https://react.dev)
2. [Next.js Documentation](https://nextjs.org/docs)
3. [SWR Documentation](https://swr.vercel.app)
4. [Web Content Accessibility Guidelines](https://www.w3.org/WAI/standards-guidelines/wcag/)
5. [Core Web Vitals](https://web.dev/vitals/)

---

*Vercel Engineering - January 2026*