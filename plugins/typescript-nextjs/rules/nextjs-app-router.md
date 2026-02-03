# Next.js App Router Patterns

Apply these patterns when working with Next.js 14+ App Router.

## Server vs Client Components

**Server Components** (default):
- Data fetching
- Access backend resources
- Keep sensitive data on server
- No interactivity, no hooks, no browser APIs

**Client Components** (`"use client"`):
- Interactivity (onClick, onChange)
- Hooks (useState, useEffect)
- Browser APIs (localStorage, window)

```tsx
// ❌ Unnecessary "use client"
"use client"
export default function StaticCard({ title }: { title: string }) {
  return <div>{title}</div>  // No interactivity needed
}

// ✅ Server Component (default)
export default function StaticCard({ title }: { title: string }) {
  return <div>{title}</div>
}

// ✅ Client Component (when needed)
"use client"
export default function Counter() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

## File Conventions

```
app/
├── page.tsx           # Route UI
├── layout.tsx         # Shared layout
├── loading.tsx        # Loading UI (Suspense)
├── error.tsx          # Error boundary
├── not-found.tsx      # 404 UI
└── [slug]/
    └── page.tsx       # Dynamic route
```

## Data Fetching

```tsx
// ✅ Server Component - async/await
export default async function Page({ params }: { params: { id: string } }) {
  const data = await getData(params.id)

  if (!data) return <NotFound />

  return <Content data={data} />
}
```

## Server Actions for Mutations

```tsx
// actions.ts
"use server"

export async function createItem(formData: FormData) {
  // Validate, mutate database
  revalidatePath('/items')
}

// Usage in Server Component
<form action={createItem}>
  <input name="title" />
  <button type="submit">Create</button>
</form>
```

## Forms - Prefer Uncontrolled

```tsx
// ✅ Uncontrolled with form action
export function ContactForm() {
  async function handleSubmit(formData: FormData) {
    "use server"
    const email = formData.get("email")
    // ... process
  }

  return (
    <form action={handleSubmit}>
      <input name="email" type="email" defaultValue="" required />
      <button type="submit">Submit</button>
    </form>
  )
}

// ❌ Avoid controlled components for forms
const [email, setEmail] = useState("")
<input value={email} onChange={e => setEmail(e.target.value)} />
```

## Performance

**Images:**
```tsx
import Image from 'next/image'

// ✅ next/image for optimization
<Image src="/hero.jpg" alt="Hero" width={800} height={400} priority />

// ❌ Native img tag
<img src="/hero.jpg" alt="Hero" />
```

**Links:**
```tsx
import Link from 'next/link'

// ✅ Link for client navigation + prefetching
<Link href="/about">About</Link>

// ❌ Programmatic navigation when link works
router.push('/about')
```

## Anti-Patterns to Avoid

| Pattern | Problem | Solution |
|---------|---------|----------|
| useEffect for fetch | Waterfalls, race conditions | Server Component async/await |
| Unnecessary "use client" | Larger bundles | Server Component default |
| Controlled form inputs | Unnecessary re-renders | Uncontrolled with defaultValue |
| router.push for links | No prefetching | next/link |
| Native img tag | No optimization | next/image |

## Validation Gates

- `next build` passes without errors
- No Pages Router patterns in App Router code
- Proper loading.tsx and error.tsx boundaries
- Server/Client component boundaries are intentional
