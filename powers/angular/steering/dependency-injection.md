# Dependency Injection

## How DI Works

Two primary interactions: **Providing** (making values available) and **Injecting** (requesting values).

## Creating Services

Use `providedIn: 'root'` for singleton services available everywhere (recommended).

```ts
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class AnalyticsLogger {
  trackEvent(category: string, value: string) {
    console.log('Analytics event:', { category, value });
  }
}
```

Generate via CLI: `ng generate service my-data`

## Injecting Dependencies

Use `inject()` function in class field initializers (recommended) or constructors.

```ts
import { Component, inject } from '@angular/core';

@Component({...})
export class Navbar {
  private router = inject(Router);
  private analytics = inject(AnalyticsLogger);
}
```

### Valid Injection Contexts

1. Class field initializers (recommended)
2. Constructor body
3. Functional route guards and resolvers
4. Factory functions in providers

## InjectionToken

For non-class dependencies (config objects, functions, primitives):

```ts
import { InjectionToken } from '@angular/core';

export interface AppConfig { apiUrl: string; }

export const APP_CONFIG = new InjectionToken<AppConfig>('app.config', {
  providedIn: 'root',
  factory: () => ({ apiUrl: 'https://api.example.com' }),
});
```

## Manual Provision

Use the `providers` array when a service lacks `providedIn`, or you need component-specific instances:

```ts
@Component({
  providers: [
    LocalService,
    { provide: Logger, useClass: BetterLogger },
    { provide: API_URL_TOKEN, useValue: 'https://api.example.com' },
    { provide: ApiClient, useFactory: (http = inject(HttpClient)) => new ApiClient(http) },
    { provide: OldLogger, useExisting: NewLogger },
    { provide: INTERCEPTOR_TOKEN, useClass: AuthInterceptor, multi: true },
  ],
})
export class Example {}
```

## Scopes

- **Application Bootstrap**: Global singletons
- **Component/Directive**: Isolated instances, destroyed with the component
- **Route**: Feature-specific services loaded with specific routes

## Library Pattern

```ts
export function provideAnalytics(config: AnalyticsConfig): Provider[] {
  return [{ provide: ANALYTICS_CONFIG, useValue: config }, AnalyticsService];
}
```

## Hierarchical Injectors

1. **`EnvironmentInjector`**: Global singletons via `providedIn: 'root'`
2. **`ElementInjector`**: Created at each DOM element via `providers`/`viewProviders`

### Resolution Order

ElementInjector tree (bottom-up) → EnvironmentInjector tree → Error (unless optional)

### Resolution Modifiers

```ts
optionalService = inject(MyService, { optional: true });  // null if not found
parentService = inject(ParentService, { skipSelf: true }); // skip current, check parent
```

### `providers` vs `viewProviders`

- `providers`: Available to component, view, AND projected content
- `viewProviders`: Available to component and view, NOT projected content

## `runInInjectionContext`

For running code that needs `inject()` outside normal injection contexts:

```ts
@Injectable({ providedIn: 'root' })
export class MyService {
  private injector = inject(EnvironmentInjector);

  doSomethingDynamic() {
    runInInjectionContext(this.injector, () => {
      const router = inject(Router);
    });
  }
}
```
