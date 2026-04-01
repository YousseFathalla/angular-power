# Accessibility with Angular Aria

`@angular/aria` provides headless, accessible directives implementing WAI-ARIA patterns. They handle keyboard interactions, ARIA attributes, focus management, and screen reader support.

**Your role**: Provide HTML structure and CSS styling. The directives handle accessibility logic.

**CRITICAL**: Install the package first: `npm install @angular/aria`

## Styling Strategy

Angular Aria components are headless — no default styles. Style based on ARIA attributes:

- `[aria-expanded="true"]` / `[aria-expanded="false"]`
- `[aria-selected="true"]`
- `[aria-disabled="true"]`
- `[aria-pressed="true"]`

---

## 1. Accordion

```ts
import { AccordionContent, AccordionGroup, AccordionPanel, AccordionTrigger } from '@angular/aria/accordion';
```

```html
<div ngAccordionGroup [multiExpandable]="false">
  <div class="accordion-item">
    <button ngAccordionTrigger panelId="panel-1">Section 1 <span class="icon">▼</span></button>
    <div ngAccordionPanel panelId="panel-1">
      <ng-template ngAccordionContent>
        <p>Lazy loaded content.</p>
      </ng-template>
    </div>
  </div>
</div>
```

## 2. Listbox

```ts
import { Listbox, Option } from '@angular/aria/listbox';
```

```html
<ul ngListbox [(values)]="selectedItems" orientation="horizontal" [multi]="true">
  <li ngOption value="apple">Apple</li>
  <li ngOption value="banana">Banana</li>
</ul>
```

## 3. Combobox / Select / Multiselect

```ts
import { Combobox, ComboboxInput, ComboboxPopupContainer } from '@angular/aria/combobox';
import { Listbox, Option } from '@angular/aria/listbox';
```

```html
<div ngCombobox [readonly]="true">
  <button ngComboboxInput>{{ selectedValue() || 'Choose' }}</button>
  <ng-template ngComboboxPopupContainer>
    <ul ngListbox [(values)]="selectedValue">
      <li ngOption value="option1">Option 1</li>
      <li ngOption value="option2">Option 2</li>
    </ul>
  </ng-template>
</div>
```

## 4. Menu and Menubar

```ts
import { MenuBar, Menu, MenuContent, MenuItem } from '@angular/aria/menu';
```

```html
<ul ngMenuBar>
  <li ngMenuItem value="file">
    <button ngMenuTrigger [menu]="fileMenu">File</button>
  </li>
</ul>
<ul ngMenu #fileMenu="ngMenu">
  <li ngMenuItem value="new">New</li>
  <li ngMenuItem value="open">Open</li>
</ul>
```

## 5. Tabs

```ts
import { Tab, Tabs, TabList, TabPanel, TabContent } from '@angular/aria/tabs';
```

```html
<div ngTabs>
  <ul ngTabList>
    <li ngTab value="profile">Profile</li>
    <li ngTab value="security">Security</li>
  </ul>
  <div ngTabPanel value="profile">
    <ng-template ngTabContent>Profile Settings</ng-template>
  </div>
  <div ngTabPanel value="security">
    <ng-template ngTabContent>Security Settings</ng-template>
  </div>
</div>
```

## 6. Toolbar

```ts
import { Toolbar, ToolbarWidget, ToolbarWidgetGroup } from '@angular/aria/toolbar';
```

```html
<div ngToolbar>
  <div ngToolbarWidgetGroup [multi]="true" role="group" aria-label="Formatting">
    <button ngToolbarWidget value="bold">B</button>
    <button ngToolbarWidget value="italic">I</button>
  </div>
</div>
```

## 7. Tree

```ts
import { Tree, TreeItem, TreeItemGroup } from '@angular/aria/tree';
```

```html
<ul ngTree>
  <li ngTreeItem value="documents">
    <span>Documents</span>
    <ul ngTreeGroup>
      <li ngTreeItem value="resume">Resume.pdf</li>
    </ul>
  </li>
</ul>
```

## 8. Grid

```html
<table ngGrid [multi]="true" [enableSelection]="true">
  <tr ngGridRow>
    <th ngGridCell role="columnheader">Name</th>
    <th ngGridCell role="columnheader">Status</th>
  </tr>
  <tr ngGridRow>
    <td ngGridCell>Project A</td>
    <td ngGridCell [(selected)]="isSelected">
      <button ngGridCellWidget (activated)="onActivate()">Active</button>
    </td>
  </tr>
</table>
```

## General Rules

1. Never use native `<select>` when implementing these Aria patterns — use the `ng*` directives.
2. Handle CSS manually targeting ARIA attributes the directives toggle.
3. Always use `ng-template` with content directives (`ngAccordionContent`, `ngTabContent`) for lazy rendering.
