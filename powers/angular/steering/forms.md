# Forms: Signal Forms, Reactive Forms & Template-Driven Forms

## Form Strategy Decision

- Angular v21+ new forms → **prefer Signal Forms**
- Older apps or existing forms → use the form type matching the app's current strategy
- Simple forms → Template-driven forms
- Complex forms → Reactive forms

---

## Signal Forms (v21+ Recommended)

**CRITICAL**: Use Angular's Signal Forms API. Do NOT use null as a value or type of any fields.

### Imports

```ts
import {
  form, FormField, submit,
  disabled, hidden, readonly, debounce,
  applyWhen, applyEach, schema,
  validate, validateHttp, validateStandardSchema,
  metadata,
} from '@angular/forms/signals';
```

### Creating a Form

```ts
import { Component, signal } from '@angular/core';
import { form, FormField } from '@angular/forms/signals';

@Component({
  imports: [FormField],
})
export class Example {
  userModel = signal({
    name: '',        // NEVER use null or undefined
    email: '',
    age: 0,          // Use 0 for numbers, NOT null
    address: { street: '', city: '' },
    hobbies: [] as string[],  // Use [] for arrays, NOT null
  });

  userForm = form(this.userModel);
}
```

### Validation

```ts
import { required, email, min, max, minLength, maxLength, pattern } from '@angular/forms/signals';

userForm = form(this.userModel, (s) => {
  required(s.name, { message: 'Name is required' });
  required(s.name, { when({ valueOf }) { return valueOf(s.age) > 10; } });
  email(s.email, { message: 'Invalid email' });
  min(s.age, 18);
  max(s.age, 100);
  minLength(s.password, 8);
  pattern(s.zipCode, /^\d{5}$/);
});
```

**Note:** `when` option only works with `required()`. For conditional non-required validators, use `applyWhen`.

### FieldState vs FormField

**RULE**: Call a field as a function to access its state signals.

```ts
f.cat.name;          // FormField (structural)
f.cat.name();        // FieldState (access signals)
f.cat.name().touched();  // ✅ VALID
f.cat.name.touched();    // ❌ ERROR
```

### Disabled / Readonly / Hidden

```ts
import { disabled, readonly, hidden } from '@angular/forms/signals';

userForm = form(this.userModel, (s) => {
  disabled(s.password, ({ valueOf }) => !valueOf(s.createAccount));
  hidden(s.shippingAddress, ({ valueOf }) => valueOf(s.sameAsBilling));
  readonly(s.username);
});
```

### Template Binding

```html
<input [formField]="userForm.name" />
<input type="checkbox" [formField]="userForm.isAdmin" />
<select [formField]="userForm.country">
  <option value="us">US</option>
</select>
```

**FORBIDDEN with `[formField]`:** `min`, `max`, `value`, `[value]`, `[disabled]`, `[readonly]` attributes.

### Accessing State

```ts
form().invalid()
form.field().dirty()
form.field.subfield().touched()
form.a.b.c.d().value()
form().reset()

// Array length (no parentheses):
form.items.length
```

### Submitting

**CRITICAL**: Callback MUST be `async`.

```ts
import { submit } from '@angular/forms/signals';

onSubmit() {
  submit(this.userForm, async () => {
    await this.apiService.save(this.userModel());
  });
}
```

### Async Validation

```ts
import { resource } from '@angular/core';
import { validateAsync } from '@angular/forms/signals';

userForm = form(this.userModel, (s) => {
  validateAsync(s.username, {
    params: ({ value }) => value(),  // MUST be a function
    factory: (username) => resource({
      params: username,
      loader: async ({ params: value }) => {
        await new Promise(r => setTimeout(r, 1000));
        return value === 'taken';
      },
    }),
    onSuccess: (isTaken) => isTaken ? { kind: 'taken', message: 'Username taken' } : undefined,
    onError: () => ({ kind: 'error', message: 'Validation failed' }),  // REQUIRED
  });
});
```

### applyEach for Arrays

```ts
// CORRECT - single argument
applyEach(s.items, (item) => {
  required(item.name);
});
```

### Nested @for Loops

```html
<!-- CORRECT - use let to store outer index -->
@for (item of form.items; track $index; let outerIndex = $index) {
  @for (option of item.options; track $index) {
    <button (click)="removeOption(outerIndex, $index)">Remove</button>
  }
}
```

### Common Pitfalls

| Mistake | Wrong | Right |
|---------|-------|-------|
| Accessing flags | `form.field.valid()` | `form.field().valid()` |
| Form root flags | `form.invalid()` | `form().invalid()` |
| Setting value | `form.field.set(x)` | Update model signal |
| Array length | `form.items().length` | `form.items.length` |
| Submit callback | `submit(form, () => {...})` | `submit(form, async () => {...})` |
| Null in model | `signal({ name: null })` | `signal({ name: '' })` |
| `when` option | `pattern(p.x, /.../, {when})` | `when` only works with `required()` |

---

## Reactive Forms

Model-driven approach using `FormControl`, `FormGroup`, `FormArray`, `FormBuilder`.

```ts
import { ReactiveFormsModule, FormBuilder, Validators } from '@angular/forms';

@Component({
  imports: [ReactiveFormsModule],
})
export class ProfileEditor {
  private fb = inject(FormBuilder);

  profileForm = this.fb.group({
    firstName: ['', Validators.required],
    lastName: [''],
    address: this.fb.group({ street: [''], city: [''] }),
    aliases: this.fb.array([this.fb.control('')]),
  });
}
```

Template binding: `[formGroup]`, `formControlName`, `formGroupName`, `formArrayName`.

---

## Template-Driven Forms

Use `[(ngModel)]` with `FormsModule`. Every `[(ngModel)]` element MUST have a `name` attribute.

```ts
import { FormsModule } from '@angular/forms';

@Component({
  imports: [FormsModule],
})
export class UserForm {
  user = { name: '', role: 'Guest' };
}
```

```html
<form #userForm="ngForm" (ngSubmit)="onSubmit()">
  <input type="text" required [(ngModel)]="user.name" name="name" #nameCtrl="ngModel" />
  @if (nameCtrl.invalid && (nameCtrl.dirty || nameCtrl.touched)) {
    <div>Name is required.</div>
  }
  <button type="submit" [disabled]="!userForm.form.valid">Submit</button>
</form>
```
