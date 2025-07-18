# Angular Components

## What is a Component?
A component is a building block of an Angular application. It contains:
- A **TypeScript class** (logic)
- An **HTML template**
- An optional **CSS/SCSS file**

## Decorator: `@Component`

```ts
@Component({
  selector: 'app-hello',
  templateUrl: './hello.component.html',
  styleUrls: ['./hello.component.css']
})
export class HelloComponent implements OnInit {
  ngOnInit(): void {
    console.log('Component initialized');
  }
}
```

# Angular Directives

## Types of Directives

Angular provides two main types of directives:

### 1. Structural Directives  
Change the DOM layout by adding/removing elements.

Examples:
- `*ngIf`
 ```html
<p *ngIf="isLoggedIn">Welcome!</p>
```
- `*ngFor`
```html
<ul>
  <li *ngFor="let item of items">{{ item }}</li>
</ul>
```
- `*ngSwitch`
```html
<div [ngSwitch]="fruit">
  <p *ngSwitchCase="'apple'">You selected Apple 🍎</p>
  <p *ngSwitchCase="'banana'">You selected Banana 🍌</p>
  <p *ngSwitchDefault>Please select a fruit</p>
</div>
```

### 2. Attribute Directives  
Change the appearance or behavior of an element.

Examples:
- `ngClass`
- `ngStyle`
- Custom directives like `appHighlight`
```html
<p [ngClass]="{ active: isActive }">Styled with ngClass</p>

<p [ngStyle]="{ 'color': isActive ? 'green' : 'gray' }">
  Styled with ngStyle
</p>
```

| HTML Template | TypeScript Component |
|---------------|----------------------|
| `<div [ngSwitch]="role">`<br>`  <p *ngSwitchCase="'admin'">Admin Panel</p>`<br>`  <p *ngSwitchCase="'user'">User Dashboard</p>`<br>`  <p *ngSwitchDefault>Guest View</p>`<br>`</div>` | `role = 'user';`<br><br>`ngOnInit() {`<br>`  console.log('Role:', this.role);`<br>`}` |



| Directive | HTML Template | TypeScript Component |
|-----------|----------------|----------------------|
| **ngIf** | `<p *ngIf="isLoggedIn">Welcome back!</p>` | `isLoggedIn = true;` |
| **ngFor** | `<ul><li *ngFor="let item of items">{{ item }}</li></ul>` | `items = ['Apple', 'Banana', 'Mango'];` |
| **ngStyle** | `<p [ngStyle]="{ color: isActive ? 'green' : 'gray' }">Status</p>` | `isActive = true;` |
