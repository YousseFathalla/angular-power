# Styling, Tailwind CSS & Animations

## Component Styling

### Defining Styles

```ts
@Component({
  selector: 'app-photo',
  styles: `img { border-radius: 50%; }`,
  // OR external: styleUrl: 'photo.component.css',
})
export class Photo {}
```

### View Encapsulation

| Mode | Behavior |
|------|----------|
| `Emulated` (Default) | Scopes styles via unique HTML attributes |
| `ShadowDom` | Native Shadow DOM isolation |
| `None` | Styles become global |

### Special Selectors

- `:host` — targets the host element
- `:host-context(.theme-dark)` — targets host based on ancestor
- `::ng-deep` — deprecated, avoid in new code

## Tailwind CSS

**CRITICAL: Use Tailwind v4 practices. Do NOT use v3 patterns (`tailwind.config.js`, `@tailwind` directives).**

### Automated Setup (Recommended)

```bash
ng add tailwindcss
```

### Manual Setup (v4)

1. Install: `npm install tailwindcss @tailwindcss/postcss postcss`
2. Create `.postcssrc.json`:
   ```json
   { "plugins": { "@tailwindcss/postcss": {} } }
   ```
3. In `src/styles.css`: `@import 'tailwindcss';`
   (SCSS: `@use 'tailwindcss';`)

**Do NOT** create `tailwind.config.js`. Configuration in v4 is via CSS variables.

## Animations

### Native CSS Animations (v20.2+ Recommended)

Use `animate.enter` and `animate.leave` for DOM enter/leave animations.

```html
@if (isShown()) {
  <div animate.enter="enter-animation">
    <p>Entering.</p>
  </div>
}
```

```css
.enter-animation {
  animation: slide-fade 1s;
}
@keyframes slide-fade {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}
```

### Event Bindings for JS Libraries

```html
@if (show()) {
  <div (animate.leave)="onLeave($event)">...</div>
}
```

```ts
import { AnimationCallbackEvent } from '@angular/core';

onLeave(event: AnimationCallbackEvent) {
  // Custom animation logic
  event.animationComplete(); // MUST call when done
}
```

### CSS State Animations

```html
<div [class.open]="isOpen">...</div>
```

```css
div { transition: height 0.3s ease-out; height: 100px; }
div.open { height: 200px; }
```

### Animating Auto Height (CSS Grid trick)

```css
.container { display: grid; grid-template-rows: 0fr; transition: grid-template-rows 0.3s; }
.container.open { grid-template-rows: 1fr; }
.container > div { overflow: hidden; }
```

### Legacy Animations DSL (pre v20.2)

```ts
import { trigger, state, style, animate, transition } from '@angular/animations';

@Component({
  animations: [
    trigger('openClose', [
      state('open', style({ opacity: 1 })),
      state('closed', style({ opacity: 0 })),
      transition('open <=> closed', [animate('0.5s')]),
    ]),
  ],
  template: `<div [@openClose]="isOpen() ? 'open' : 'closed'">...</div>`,
})
export class OpenClose {
  isOpen = signal(true);
}
```

Setup: `providers: [provideAnimationsAsync()]`

**Do not mix legacy animations and `animate.enter`/`leave` in the same component.**
