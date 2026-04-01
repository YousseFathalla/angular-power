# Components, Inputs, Outputs & Host Elements

## Component Definition

Use the `@Component` decorator to define a component's metadata.

```ts
@Component({
  selector: 'app-profile',
  template: `
    <img src="profile.jpg" alt="Profile photo" />
    <button (click)="save()">Save</button>
  `,
  styles: `
    img {
      border-radius: 50%;
    }
  `,
})
export class Profile {
  save() { /* ... */ }
}
```

## Metadata Options

- `selector`: CSS selector that identifies this component in templates.
- `template` / `templateUrl`: Inline or external HTML template.
- `styles` / `styleUrl` / `styleUrls`: Inline or external CSS.
- `imports`: Components, directives, or pipes used in this component's template.

## Using Components

Add to the `imports` array and use the selector in the template.

```ts
@Component({
  selector: 'app-root',
  imports: [Profile],
  template: `<app-profile />`,
})
export class App {}
```

## Template Control Flow

### Conditional Rendering (`@if`)

```html
@if (user.isAdmin) {
  <admin-dashboard />
} @else if (user.isModerator) {
  <mod-dashboard />
} @else {
  <standard-dashboard />
}
```

**Result aliasing:**

```html
@if (user.settings(); as settings) {
  <p>Theme: {{ settings.theme }}</p>
}
```

### Loops (`@for`)

The `track` expression is **required** for performance and DOM reuse.

```html
<ul>
  @for (item of items(); track item.id; let i = $index, total = $count) {
    <li>{{ i + 1 }}/{{ total }}: {{ item.name }}</li>
  } @empty {
    <li>No items to display.</li>
  }
</ul>
```

Implicit Variables: `$index`, `$count`, `$first`, `$last`, `$even`, `$odd`.

### Switching (`@switch`)

```html
@switch (status()) {
  @case ('loading') { <app-spinner /> }
  @case ('error') { <app-error-msg /> }
  @case ('success') { <app-data-grid /> }
  @default { <p>Unknown status</p> }
}
```

Use `@default never;` for exhaustive type checking.

## Core Concepts

- **Standalone**: Default since Angular 19. For older versions, `standalone: true` must be explicit.
- **Component Naming**: Do not add the `Component` suffix unless the project convention requires it.
- **Component Tree**: Applications are structured as a tree of components.

## Signal-based Inputs

```ts
import { Component, input, computed } from '@angular/core';

@Component({
  selector: 'app-user',
  template: `<p>User: {{ name() }} ({{ age() }})</p>`,
})
export class User {
  name = input('Guest');           // Optional with default
  age = input.required<number>();  // Required
  label = computed(() => `Name: ${this.name()}`);
}
```

### Configuration Options

```ts
import { input, booleanAttribute } from '@angular/core';

@Component({...})
export class CustomButton {
  label = input('', { alias: 'btnLabel' });
  disabled = input(false, { transform: booleanAttribute });
}
```

### Model Inputs (Two-Way Binding)

```ts
@Component({
  selector: 'custom-counter',
  template: `<button (click)="increment()">+</button>`,
})
export class CustomCounter {
  value = model(0);
  increment() { this.value.update(v => v + 1); }
}
```

Usage: `<custom-counter [(value)]="mySignal" />`

## Function-based Outputs

```ts
import { Component, output } from '@angular/core';

@Component({
  selector: 'custom-slider',
  template: `<button (click)="changeValue(50)">Set to 50</button>`,
})
export class CustomSlider {
  panelClosed = output<void>();
  valueChanged = output<number>();
  changeValue(newValue: number) { this.valueChanged.emit(newValue); }
}
```

Usage: `<custom-slider (valueChanged)="logValue($event)" />`

### Best Practices for Inputs/Outputs

- Prefer `input()` over `@Input()` and `output()` over `@Output()`
- Use `input.required()` for mandatory data
- Use `camelCase` for output names, avoid `on` prefix
- Avoid names that collide with DOM properties/events

## Host Elements

Use the `host` property to bind to the host element:

```ts
@Component({
  selector: 'custom-slider',
  host: {
    'role': 'slider',
    '[attr.aria-valuenow]': 'value',
    '[class.active]': 'isActive()',
    '[style.color]': 'color()',
    '[tabIndex]': 'disabled ? -1 : 0',
    '(keydown)': 'onKeyDown($event)',
  },
})
export class CustomSlider { /* ... */ }
```

### Injecting Host Attributes

```ts
import { HostAttributeToken, inject } from '@angular/core';

@Component({ selector: 'app-btn', template: `<ng-content />` })
export class AppButton {
  type = inject(new HostAttributeToken('type'));
}
```
