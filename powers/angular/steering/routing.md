# Routing

## Basic Configuration

```ts
// app.routes.ts
export const routes: Routes = [
  { path: '', component: HomePage },
  { path: 'admin', component: AdminPage },
];

// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes)],
};
```

## URL Paths

- **Static**: `'admin'`
- **Route Parameters**: `'user/:id'`
- **Wildcard**: `**` (always place last)

Angular uses first-match wins — specific routes before less specific ones.

## Redirects

```ts
{ path: 'articles', redirectTo: '/blog' },
```

## Nested Routes

```ts
{
  path: 'product/:id',
  component: Product,
  children: [
    { path: 'info', component: ProductInfo },
    { path: 'reviews', component: ProductReviews },
  ],
}
```

Parent must include `<router-outlet />`.

## Lazy Loading

### Components

```ts
{ path: 'admin', loadComponent: () => import('./admin/admin.component').then(m => m.AdminComponent) }
```

### Child Routes

```ts
{ path: 'settings', loadChildren: () => import('./settings/settings.routes') }
```

### Context-Aware Loading

```ts
{
  path: 'dashboard',
  loadComponent: () => {
    const flags = inject(FeatureFlags);
    return flags.isPremium ? import('./premium-dashboard') : import('./basic-dashboard');
  },
}
```

## Router Outlet

```html
<app-header />
<router-outlet />
<app-footer />
```

### Named Outlets

```html
<router-outlet />
<router-outlet name="sidebar" />
```

### Passing Data via `routerOutletData`

```ts
// Parent
<router-outlet [routerOutletData]="{ theme: 'dark' }" />

// Routed Component
outletData = inject(ROUTER_OUTLET_DATA) as Signal<{ theme: string }>;
```

## Navigation

### Declarative (`RouterLink`)

```ts
import { RouterLink, RouterLinkActive } from '@angular/router';

@Component({
  imports: [RouterLink, RouterLinkActive],
  template: `
    <a routerLink="/dashboard" routerLinkActive="active-link">Dashboard</a>
    <a [routerLink]="['/user', userId]">Profile</a>
  `,
})
export class Nav { userId = '123'; }
```

### Programmatic (`Router`)

```ts
private router = inject(Router);
private route = inject(ActivatedRoute);

this.router.navigate(['/profile']);
this.router.navigate(['/search'], { queryParams: { q: 'angular' } });
this.router.navigate(['edit'], { relativeTo: this.route });
this.router.navigateByUrl('/products/123?view=details');
```

## Route Guards

Functional guards (since Angular 15):

```ts
export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);
  if (authService.isLoggedIn()) return true;
  return router.parseUrl('/login');
};
```

Apply to routes:

```ts
{ path: 'admin', component: Admin, canActivate: [authGuard] }
```

Guard types: `CanActivate`, `CanActivateChild`, `CanDeactivate`, `CanMatch`

**Client-side guards are NOT a substitute for server-side security.**

## Data Resolvers

```ts
export const userResolver: ResolveFn<User> = (route, state) => {
  const userService = inject(UserService);
  return userService.getUser(route.paramMap.get('id')!);
};
```

Access via component inputs (with `withComponentInputBinding()`):

```ts
provideRouter(routes, withComponentInputBinding());

// In component:
user = input.required<User>();
```

## Rendering Strategies

| Requirement | Strategy |
|-------------|----------|
| SEO + Static Content | SSG (Prerendering) |
| SEO + Dynamic Content | SSR |
| No SEO + High Interactivity | CSR |
| Mixed | Hybrid (Route-based) |

## Route Transition Animations

```ts
provideRouter(routes, withViewTransitions());
```

Customize in global CSS:

```css
::view-transition-old(root) { animation: 90ms ease both fade-out; }
::view-transition-new(root) { animation: 210ms ease 90ms both fade-in; }
```

## Router Events

```ts
this.router.events.pipe(
  filter(e => e instanceof NavigationEnd)
).subscribe(event => console.log('Navigated to:', event.url));
```

Enable debug tracing: `provideRouter(routes, withDebugTracing())`
