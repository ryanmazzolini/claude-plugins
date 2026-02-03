---
description: Usability heuristics, WCAG accessibility, responsive design. Auto-apply when reviewing or writing UI components, CSS, React/Vue files, or discussing UX.
---

# Human-Computer Interaction Standards

Apply these principles when working with user interfaces.

## Nielsen's 10 Usability Heuristics

### 1. Visibility of System Status
Keep users informed about what's happening.

- Loading states during async operations
- Progress indicators for multi-step processes
- Error feedback on failures
- Disabled button explanations

```tsx
// ❌ No feedback
<button onClick={save}>Save</button>

// ✅ Clear status
<button onClick={save} disabled={saving}>
  {saving ? 'Saving...' : 'Save'}
</button>
```

### 2. Match System and Real World
Speak the user's language, not technical jargon.

- Industry-standard terminology
- Natural ordering (chronological, alphabetical)
- Familiar icons and metaphors

```tsx
// ❌ Technical jargon
<div>Error: 500 Internal Server Error</div>

// ✅ Human language
<div>Unable to save. Please check your connection and try again.</div>
```

### 3. User Control and Freedom
Provide emergency exits and undo.

- Cancel buttons on long operations
- Undo/redo functionality
- Exit paths from modals (X, ESC, click outside)
- Back navigation in multi-step flows

### 4. Consistency and Standards
Follow platform and industry conventions.

- Consistent button styles across screens
- Standard patterns (magnifying glass = search)
- Uniform terminology throughout
- Platform conventions (cmd+S to save on Mac)

### 5. Error Prevention
Better than good error messages.

- Input validation before submission
- Constraints on inputs (min/max, format hints)
- Confirmation dialogs on destructive actions
- Disabled states with explanations

```tsx
// ❌ Allow invalid submit
<button type="submit">Submit</button>

// ✅ Prevent errors
<button type="submit" disabled={!isValid}>
  Submit
</button>
{!isValid && <span>Please fill all required fields</span>}
```

### 6. Recognition Rather Than Recall
Minimize memory load.

- Visible options (dropdowns vs remembering codes)
- Recently used items
- Breadcrumbs and location indicators
- Inline help and placeholders

### 7. Flexibility and Efficiency of Use
Accelerators for expert users.

- Keyboard shortcuts for common actions
- Bulk operations
- Customizable defaults
- Search/filter capabilities

### 8. Aesthetic and Minimalist Design
Remove irrelevant information.

- Focus on primary actions
- Progressive disclosure (hide advanced options)
- Clear visual hierarchy
- Whitespace for breathing room

### 9. Help Users Recognize, Diagnose, and Recover from Errors
Error messages in plain language.

- Precise problem description
- Constructive solution
- Preserve user input after errors

```tsx
// ❌ Vague error
<div className="error">Invalid input</div>

// ✅ Actionable error
<div className="error">
  Email format should be: name@example.com
</div>
```

### 10. Help and Documentation
Context-sensitive assistance.

- Inline help text for complex fields
- Tooltips on unfamiliar terms
- Empty state guidance
- Onboarding for new users

---

## WCAG Accessibility (Level AA)

### Perceivable

**Text alternatives**: All non-text content has text alternatives.
```tsx
// ❌ Missing alt
<img src="chart.png" />

// ✅ Descriptive alt
<img src="chart.png" alt="Sales increased 25% from Q1 to Q2" />

// ✅ Decorative image
<img src="decoration.png" alt="" role="presentation" />
```

**Color contrast**: 4.5:1 for normal text, 3:1 for large text.
```css
/* ❌ Low contrast */
color: #999; background: #fff; /* 2.8:1 */

/* ✅ Sufficient contrast */
color: #595959; background: #fff; /* 7:1 */
```

**Don't rely on color alone**: Use icons, patterns, or text alongside color.

### Operable

**Keyboard accessible**: All functionality available via keyboard.
```tsx
// ❌ Mouse-only
<div onClick={handleClick}>Click me</div>

// ✅ Keyboard accessible
<button onClick={handleClick}>Click me</button>

// ✅ If div required
<div onClick={handleClick} onKeyDown={handleKeyDown} tabIndex={0} role="button">
  Click me
</div>
```

**Focus visible**: Clear focus indicators for keyboard navigation.
```css
/* ❌ Hidden focus */
:focus { outline: none; }

/* ✅ Visible focus */
:focus-visible { outline: 2px solid #0066cc; outline-offset: 2px; }
```

**Skip links**: Allow skipping repetitive navigation.
```tsx
<a href="#main-content" className="skip-link">Skip to main content</a>
```

### Understandable

**Language**: Declare page language.
```html
<html lang="en">
```

**Labels**: Form inputs have associated labels.
```tsx
// ❌ No label
<input type="email" placeholder="Email" />

// ✅ Proper label
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ✅ Or aria-label
<input type="email" aria-label="Email address" placeholder="Email" />
```

**Error identification**: Errors clearly identified and described.

### Robust

**Valid HTML**: Proper semantic structure.
```tsx
// ❌ Div soup
<div class="header"><div class="nav">...</div></div>

// ✅ Semantic HTML
<header><nav>...</nav></header>
```

**ARIA when needed**: Use ARIA to enhance, not replace, semantic HTML.
```tsx
// ❌ ARIA overuse
<div role="button" aria-label="Submit">Submit</div>

// ✅ Native element
<button>Submit</button>

// ✅ ARIA when necessary
<div role="tablist">
  <button role="tab" aria-selected="true">Tab 1</button>
  <button role="tab" aria-selected="false">Tab 2</button>
</div>
```

---

## Responsive Design

### Mobile-First Approach
```css
/* Base styles for mobile */
.container { padding: 1rem; }

/* Enhance for larger screens */
@media (min-width: 768px) {
  .container { padding: 2rem; }
}
```

### Touch Targets
Minimum 44x44px for touch targets.
```css
.button {
  min-height: 44px;
  min-width: 44px;
  padding: 12px 16px;
}
```

### Viewport Meta
```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

### Flexible Layouts
- Use relative units (rem, %, vw/vh)
- CSS Grid and Flexbox for layout
- Avoid fixed widths for content containers

---

## Quick Checklist

When reviewing UI code, verify:

- [ ] Loading states for async operations
- [ ] Error messages are actionable
- [ ] Keyboard navigation works
- [ ] Color contrast meets WCAG AA (4.5:1)
- [ ] Images have alt text
- [ ] Form inputs have labels
- [ ] Focus states are visible
- [ ] Touch targets are 44px minimum
- [ ] Semantic HTML used appropriately

## Sources

- [Nielsen Norman: 10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
