# TypeScript Patterns

Apply these patterns when working with TypeScript code.

## Strict Mode

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true
  }
}
```

## No `any`

```tsx
// ❌ any
function process(data: any) { ... }

// ✅ Proper typing
function process(data: UserData) { ... }

// ✅ unknown for truly unknown data
function parseJSON(json: string): unknown {
  return JSON.parse(json)
}
```

## Props Definition

```tsx
// ✅ Props in component file
interface ButtonProps {
  variant: 'primary' | 'secondary'
  children: React.ReactNode
  onClick?: () => void
}

export function Button({ variant, children, onClick }: ButtonProps) {
  return <button className={variant} onClick={onClick}>{children}</button>
}

// ❌ Don't add explicit return types to components
export function Button({ ... }): JSX.Element { ... }  // Unnecessary
```

## Shared Types

Only create in `lib/types/` when truly reusable across 3+ components.

```tsx
// lib/types/user.ts - Shared across many components
export interface User {
  id: string
  email: string
  name: string
}

// Component-specific props stay in component file
```

## Generic Constraints

```tsx
// ✅ Constrained generics
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}

// ✅ Conditional types when needed
type AsyncReturnType<T> = T extends (...args: any[]) => Promise<infer R> ? R : never
```

## Naming Conventions

- **Interfaces/Types**: PascalCase (`UserProfile`, `ApiResponse`)
- **Components**: PascalCase (`UserCard`, `DashboardLayout`)
- **Props interfaces**: ComponentName + Props (`UserCardProps`)
- **Functions/variables**: camelCase (`getUserById`, `isLoading`)
- **Constants**: SCREAMING_SNAKE_CASE for true constants (`MAX_RETRIES`)

## Validation Gates

- `tsc --noEmit` passes with no errors
- ESLint TypeScript rules pass
- No `@ts-ignore` or `@ts-expect-error` without explanation
