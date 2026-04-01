# Signals, Computed, LinkedSignal, Resource & Effects

## Writable Signals (`signal`)

```ts
import { signal } from '@angular/core';

const count = signal(0);
console.log(count());       // Read
count.set(3);               // Set directly
count.update(v => v + 1);   // Update based on previous
```

### Exposing as Readonly

```ts
private readonly _count = signal(0);
readonly count = this._count.asReadonly();
```

## Computed Signals (`computed`)

Lazily evaluated, memoized, with dynamic dependency tracking.

```ts
import { signal, computed } from '@angular/core';

const count = signal(0);
const doubleCount = computed(() => count() * 2);
```

## Reactive Contexts

Angular monitors signal reads in reactive contexts: `computed`, `effect`, `linkedSignal`, and component templates.

### Untracked Reads

```ts
import { effect, untracked } from '@angular/core';

effect(() => {
  console.log(`User: ${currentUser()}, Count: ${untracked(counter)}`);
});
```

### Async Operations

The reactive context is only active for synchronous code. Always read signals before `await`.

```ts
// ✅ CORRECT
effect(async () => {
  const currentTheme = theme();
  const data = await fetchUserData();
  console.log(currentTheme);
});
```

## Dependent State with `linkedSignal`

Creates writable state linked to other state. If the source changes, it resets.

```ts
import { signal, linkedSignal } from '@angular/core';

@Component({...})
export class ShippingMethodPicker {
  shippingOptions = signal(['Ground', 'Air', 'Sea']);
  selectedOption = linkedSignal(() => this.shippingOptions()[0]);

  changeShipping(index: number) {
    this.selectedOption.set(this.shippingOptions()[index]);
  }
}
```

### Advanced: Preserving Previous State

```ts
selectedOption = linkedSignal<ShippingMethod[], ShippingMethod>({
  source: this.shippingOptions,
  computation: (newOptions, previous) => {
    return newOptions.find(opt => opt.id === previous?.value.id) ?? newOptions[0];
  }
});
```

### When to use what

- `computed`: Strictly derived, never manually updated
- `linkedSignal`: Derived with manual override capability
- **Never** use `effect` to sync state — that's an anti-pattern

## Async Reactivity with `resource`

> The `resource` API is currently experimental.

```ts
import { resource, signal, computed } from '@angular/core';

@Component({...})
export class UserProfile {
  userId = signal('123');

  userResource = resource({
    params: () => ({ id: this.userId() }),
    loader: async ({ params, abortSignal }) => {
      const response = await fetch(`/api/users/${params.id}`, { signal: abortSignal });
      if (!response.ok) throw new Error('Network error');
      return response.json();
    }
  });

  userName = computed(() => {
    if (this.userResource.hasValue()) {
      return this.userResource.value()?.name;
    }
    return 'Loading...';
  });
}
```

### Resource Status Signals

- `value()`: Resolved data or `undefined`
- `hasValue()`: Type-guard boolean
- `isLoading()`: Boolean for loading state
- `error()`: Error or `undefined`
- `status()`: `'idle'`, `'loading'`, `'resolved'`, `'error'`, `'reloading'`, `'local'`

### Reloading and Local Mutation

```ts
this.userResource.reload();                          // Force re-fetch
this.userResource.value.set({ name: 'Optimistic' }); // Optimistic update
```

### `httpResource`

If using Angular's `HttpClient`, prefer `httpResource` — it leverages interceptors while providing the same signal-based API.

## Side Effects with `effect`

**CRITICAL: DO NOT use effects to propagate state.** Using `.set()` or `.update()` inside an effect causes `ExpressionChangedAfterItHasBeenChecked` errors. Use `computed()` or `linkedSignal()` instead.

**Valid use cases:** Logging, syncing to `localStorage`, custom rendering to `<canvas>` or 3rd-party libraries.

```ts
import { signal, effect } from '@angular/core';

@Component({...})
export class Example {
  count = signal(0);

  constructor() {
    effect((onCleanup) => {
      console.log(`Count changed to ${this.count()}`);
      const timer = setTimeout(() => console.log('Timer finished'), 1000);
      onCleanup(() => clearTimeout(timer));
    });
  }
}
```

## DOM Manipulation with `afterRenderEffect`

Runs after Angular finishes rendering. Use phases to prevent layout thrashing.

```ts
import { afterRenderEffect, viewChild, ElementRef } from '@angular/core';

@Component({...})
export class Chart {
  canvas = viewChild.required<ElementRef>('canvas');

  constructor() {
    afterRenderEffect({
      earlyRead: () => this.canvas().nativeElement.getBoundingClientRect().width,
      write: (width) => setupChart(this.canvas().nativeElement, width),
    });
  }
}
```

**Phases (in order):** `earlyRead` → `write` (never read here) → `mixedReadWrite` (avoid) → `read` (never write here)

`afterRenderEffect` only runs on the client, never during SSR.
