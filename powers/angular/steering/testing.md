# Testing

## Unit Testing Fundamentals

### Core Philosophy: Zoneless & Async-First

**Do NOT** use `fixture.detectChanges()` to manually trigger updates.
**ALWAYS** use the "Act, Wait, Assert" pattern:

1. **Act:** Update state or perform an action
2. **Wait:** `await fixture.whenStable()`
3. **Assert:** Verify the outcome

```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';

describe('MyComponent', () => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [MyComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
  });

  it('should display the default title', async () => {
    await fixture.whenStable();
    const h1 = fixture.nativeElement.querySelector('h1');
    expect(h1.textContent).toContain('Default Title');
  });

  it('should update after state change', async () => {
    component.title.set('New Title');
    await fixture.whenStable();
    const h1 = fixture.nativeElement.querySelector('h1');
    expect(h1.textContent).toContain('New Title');
  });
});
```

### TestBed and ComponentFixture

- `TestBed.configureTestingModule({...})`: Set up test module
- `fixture.componentInstance`: Access component class
- `fixture.nativeElement`: Access root DOM element
- `fixture.debugElement`: Angular-specific DOM wrapper with `query(By.css('p'))`

## Component Harnesses

Preferred way to interact with components in tests — robust, user-centric, resilient to DOM changes.

```ts
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
import { MatButtonHarness } from '@angular/material/button/testing';

describe('MyComponent', () => {
  let loader: HarnessLoader;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [MyComponent, MatButtonModule],
    }).compileComponents();

    const fixture = TestBed.createComponent(MyComponent);
    loader = TestbedHarnessEnvironment.loader(fixture);
  });

  it('should find and click a button', async () => {
    const btn = await loader.getHarness(MatButtonHarness.with({ text: 'Submit' }));
    expect(await btn.isDisabled()).toBe(false);
    await btn.click();
  });
});
```

## Router Testing

Use `RouterTestingHarness` — do NOT mock the Router.

```ts
import { provideRouter } from '@angular/router';
import { RouterTestingHarness } from '@angular/router/testing';

describe('Dashboard Routing', () => {
  let harness: RouterTestingHarness;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      providers: [
        provideRouter([
          { path: '', component: Dashboard },
          { path: 'heroes/:id', component: HeroDetail },
        ]),
      ],
    }).compileComponents();

    harness = await RouterTestingHarness.create();
  });

  it('should navigate to hero detail', async () => {
    const dashboard = await harness.navigateByUrl('/', Dashboard);
    dashboard.selectHero({ id: 42, name: 'Test Hero' });
    await harness.fixture.whenStable();
    expect(harness.router.url).toEqual('/heroes/42');
  });
});
```

## E2E Testing (Cypress)

```typescript
describe('Profiler', () => {
  beforeEach(() => {
    cy.visit('/?e2e-app');
    cy.get('ng-devtools-tabs').find('a').contains('Profiler').click();
  });

  it('should record profiling data', () => {
    cy.get('button[aria-label="start-recording-button"]').click();
    cy.get('body').find('#cards button').first().click();
    cy.get('button[aria-label="stop-recording-button"]').click();
    cy.get('ng-devtools-recording-timeline').find('canvas').should('be.visible');
  });
});
```

### E2E Best Practices

- Use `data-cy` attributes for element selection
- Encapsulate common actions into custom commands
- Prefer waiting for specific UI elements over `cy.wait()`
