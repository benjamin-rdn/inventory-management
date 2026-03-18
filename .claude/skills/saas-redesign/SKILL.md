---
name: saas-redesign
description: Redesign a Vue 3 application's UI into a modern SaaS-style interface with vertical sidebar navigation, consistent spacing, and polished professional look. Use this skill to transform a top-nav layout into a sidebar layout.
---

# SaaS UI Redesign Guide

Transform the application from a horizontal top-nav layout to a modern SaaS-style interface with a fixed vertical sidebar, consistent spacing, and polished professional appearance.

**IMPORTANT**: Per CLAUDE.md rules, delegate ALL `.vue` file modifications to the `vue-expert` subagent. Provide vue-expert with the complete code changes for each file.

## Scope

**Files to modify:**
- `client/src/App.vue` -- Major restructure (template + styles)
- `client/src/components/FilterBar.vue` -- Remove sticky positioning

**Preserve all existing functionality:**
- Routing (all 6 routes)
- Filters (4 dropdowns + reset)
- Modals (ProfileDetailsModal, TasksModal)
- i18n (LanguageSwitcher)
- Profile menu with dropdown
- All global styles (`.card`, `.badge`, `.stat-card`, table styles, etc.)

## Design System Reference

Use the existing color palette and typography -- do not introduce new colors or fonts.

**Colors:**
- Text: `#0f172a` (headings), `#1e293b` (body), `#334155` (secondary), `#64748b` (muted)
- Borders: `#e2e8f0` (standard), `#cbd5e1` (hover)
- Backgrounds: `#f8fafc` (page), `#f1f5f9` (hover), `#ffffff` (cards/sidebar)
- Accent: `#2563eb` (active), `#eff6ff` (active bg)
- Status: green `#059669`/`#d1fae5`, yellow `#ea580c`/`#fed7aa`, red `#dc2626`/`#fecaca`

**Typography:** Inter, system font stack. No changes to font sizes or weights.

**Spacing:** 0.5rem, 0.75rem, 1rem, 1.25rem, 1.5rem, 2rem scale.

**Border radius:** 10px cards, 6px buttons/inputs, 8px nav items.

**No emojis in UI.**

## Target Layout

```
+--------------------+------------------------------------------+
|                    |  FilterBar (inline, not sticky)           |
|     Sidebar        +------------------------------------------+
|     (240px)        |                                          |
|  +--------------+  |          Main Content Area               |
|  | Logo/Brand   |  |          (router-view)                   |
|  +--------------+  |          max-width: 1400px               |
|  | Nav Links    |  |          padding: 1.5rem 2rem            |
|  | with icons   |  |                                          |
|  |              |  |                                          |
|  |              |  |                                          |
|  +--------------+  |                                          |
|  | (spacer)     |  |                                          |
|  +--------------+  |                                          |
|  | Lang Switch  |  |                                          |
|  | Profile Menu |  |                                          |
|  +--------------+  |                                          |
+--------------------+------------------------------------------+
```

## Step 1: Restructure App.vue Template

Replace the `<header class="top-nav">` block and reorganize the layout. The new template structure:

```html
<template>
  <div class="app">
    <aside class="sidebar">
      <div class="sidebar-header">
        <h1 class="sidebar-logo">{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>

      <nav class="sidebar-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <rect x="2" y="2" width="7" height="7" rx="1.5"/>
            <rect x="11" y="2" width="7" height="7" rx="1.5"/>
            <rect x="2" y="11" width="7" height="7" rx="1.5"/>
            <rect x="11" y="11" width="7" height="7" rx="1.5"/>
          </svg>
          {{ t('nav.overview') }}
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 6l7-4 7 4v8l-7 4-7-4V6z"/>
            <path d="M10 10V2"/>
            <path d="M3 6l7 4 7-4"/>
          </svg>
          {{ t('nav.inventory') }}
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M4 2h12a1 1 0 011 1v14a1 1 0 01-1 1H4a1 1 0 01-1-1V3a1 1 0 011-1z"/>
            <path d="M7 6h6M7 10h6M7 14h4"/>
          </svg>
          {{ t('nav.orders') }}
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M10 2v16M6 5.5C6 4.12 7.79 3 10 3s4 1.12 4 2.5S12.21 8 10 8 6 9.12 6 10.5 7.79 13 10 13s4 1.12 4 2.5S12.21 18 10 18"/>
          </svg>
          {{ t('nav.finance') }}
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M2 16l5-5 3 3 6-8"/>
            <path d="M14 6h4v4"/>
          </svg>
          {{ t('nav.demandForecast') }}
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M5 2h10a1 1 0 011 1v14a1 1 0 01-1 1H5a1 1 0 01-1-1V3a1 1 0 011-1z"/>
            <path d="M8 6h4M8 10h4M8 14h2"/>
          </svg>
          Reports
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="content-area">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>
```

**Script block**: No changes required. All imports and setup logic remain identical.

## Step 2: Replace App.vue Styles

Remove ALL existing nav-related styles (`.top-nav`, `.nav-container`, `.nav-tabs`, `.logo`, `.subtitle`). Add the sidebar styles below. **Keep all global styles unchanged** (`.card`, `.badge`, `.stat-card`, `.stats-grid`, table styles, `.loading`, `.error`, `.page-header`).

### Styles to Remove

```css
/* DELETE these style blocks entirely: */
.top-nav { ... }
.nav-container { ... }
.nav-container > .nav-tabs { ... }
.nav-container > .language-switcher { ... }
.logo { ... }
.logo h1 { ... }
.subtitle { ... }
.nav-tabs { ... }
.nav-tabs a { ... }
.nav-tabs a:hover { ... }
.nav-tabs a.active { ... }
.nav-tabs a.active::after { ... }
```

### Styles to Add

```css
/* --- Sidebar --- */
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 240px;
  height: 100vh;
  background: #ffffff;
  border-right: 1px solid #e2e8f0;
  display: flex;
  flex-direction: column;
  z-index: 100;
}

.sidebar-header {
  padding: 1.5rem 1.25rem;
  border-bottom: 1px solid #e2e8f0;
}

.sidebar-logo {
  font-size: 1.125rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
  margin-bottom: 0.25rem;
}

.sidebar-subtitle {
  font-size: 0.75rem;
  color: #64748b;
  font-weight: 400;
}

.sidebar-nav {
  flex: 1;
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  overflow-y: auto;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  color: #64748b;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.875rem;
  border-radius: 8px;
  transition: all 0.15s ease;
  position: relative;
}

.sidebar-nav a svg {
  flex-shrink: 0;
  width: 20px;
  height: 20px;
}

.sidebar-nav a:hover {
  color: #0f172a;
  background: #f1f5f9;
}

.sidebar-nav a.active {
  color: #2563eb;
  background: #eff6ff;
}

.sidebar-nav a.active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0.375rem;
  bottom: 0.375rem;
  width: 3px;
  background: #2563eb;
  border-radius: 0 3px 3px 0;
}

.sidebar-footer {
  padding: 0.75rem 1.25rem;
  border-top: 1px solid #e2e8f0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

/* --- Content Area --- */
.content-area {
  margin-left: 240px;
  flex: 1;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
```

### Styles to Modify

Update `.app` to use row direction:

```css
/* CHANGE from flex-direction: column to row */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}
```

Update `.main-content` max-width:

```css
/* CHANGE max-width from 1600px to 1400px */
.main-content {
  flex: 1;
  max-width: 1400px;
  width: 100%;
  margin: 0 auto;
  padding: 1.5rem 2rem;
}
```

## Step 3: Adjust FilterBar.vue

Remove the sticky positioning so FilterBar becomes a normal flow element within the content area.

### Changes to FilterBar.vue Styles

Remove or modify these properties from `.filters-bar`:

```css
/* REMOVE these properties from .filters-bar: */
position: sticky;
top: 70px;
z-index: 90;

/* KEEP these properties: */
background: #f8fafc;
border-bottom: 1px solid #e2e8f0;
padding: 0.75rem 0;
```

Remove `max-width: 1600px` from `.filters-container` -- the container should fit naturally within the content area. Also remove `margin: 0 auto` if present.

## Step 4: ProfileMenu Dropdown Adjustment

The ProfileMenu is now at the bottom of the sidebar. Its dropdown currently opens downward. It should open **upward** to avoid being clipped by the viewport edge.

If the ProfileMenu dropdown has `top: calc(100% + 0.5rem)`, change it to:
```css
bottom: calc(100% + 0.5rem);
top: auto;
```

Also ensure the dropdown opens to the **right** of the sidebar if it was positioned `right: 0`:
```css
left: 0;
right: auto;
```

This adjustment may need to be made in `ProfileMenu.vue` scoped styles.

## SVG Icon Reference

All icons use: `width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"`

| Route | Icon | SVG Paths |
|-------|------|-----------|
| Overview `/` | Grid | 4 rounded rects in 2x2 grid |
| Inventory `/inventory` | Box | 3D box with center line and horizontal divide |
| Orders `/orders` | Clipboard | Rectangle with 3 horizontal lines |
| Finance `/spending` | Dollar | Vertical line with S-curve path |
| Demand `/demand` | Trending Up | Rising line with arrow endpoint |
| Reports `/reports` | Document | Rectangle with 3 horizontal lines (narrower) |

## Spacing & Polish Guidelines

When applying the redesign, ensure:

1. **Consistent card spacing**: All `.card` elements use `padding: 1.25rem` and `margin-bottom: 1.25rem`
2. **Page header spacing**: `.page-header` uses `margin-bottom: 1.5rem`
3. **Stats grid gap**: `.stats-grid` uses `gap: 1.25rem`
4. **Content padding**: Main content area has `padding: 1.5rem 2rem`
5. **No redundant max-width**: Views should not set their own max-width since `.main-content` constrains width

## Verification Checklist

After applying changes, verify:

- [ ] Sidebar renders on the left with logo, 6 nav links, language switcher, and profile menu
- [ ] All 6 routes navigate correctly and show the active state (blue highlight + left accent bar)
- [ ] FilterBar renders at the top of the content area (not sticky, not full viewport width)
- [ ] All 4 filter dropdowns function correctly
- [ ] Reset filters button works
- [ ] ProfileMenu dropdown opens upward and is fully visible
- [ ] LanguageSwitcher toggles locale correctly
- [ ] ProfileDetailsModal and TasksModal overlay the full viewport correctly
- [ ] No horizontal scrollbar on standard viewport widths (1280px+)
- [ ] Page background (#f8fafc) is visible in the content area
- [ ] Cards, tables, and charts render properly within the new content width
- [ ] SVG icons are visible and inherit the correct color from their parent link

## Optional Follow-Up: Sidebar Collapse

To add a collapse/expand toggle after the initial redesign:

1. Add a `sidebarCollapsed` ref in App.vue setup
2. Toggle button at the bottom of sidebar-header (hamburger/chevron icon)
3. When collapsed: sidebar width shrinks to 64px, only icons visible, text hidden
4. Content area adjusts: `margin-left: 64px`
5. Use CSS transition for smooth animation: `transition: width 0.2s ease`
6. Store preference in localStorage

This is not part of the initial redesign -- implement only if requested.
