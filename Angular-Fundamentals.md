# Angular Fundamentals

## Table of Contents

- [Angular Basics](#angular-basics)
- [Project Structure](#project-structure)
- [Build & Bundling (Webpack)](#build--bundling-webpack)
- [Node.js in Angular Projects](#nodejs-in-angular-projects)
- [Angular CLI & Testing](#angular-cli--testing)
- [Modules & Standalone Components](#modules--standalone-components)
- [Bootstrapping Flow](#bootstrapping-flow)
- [Components](#components)
- [Lifecycle Hooks](#lifecycle-hooks)
- [Data Binding](#data-binding)
- [Directives](#directives)
- [Pipes](#pipes)
- [Forms](#forms)
- [Component Communication](#component-communication)
- [Services & Dependency Injection](#services--dependency-injection)
- [HTTP (HttpClient) & Interceptors](#http-httpclient--interceptors)
- [Routing](#routing)
- [Route Guards](#route-guards)
- [Resolvers](#resolvers)
- [Error Handling](#error-handling)
- [Async Pipe](#async-pipe)
- [RxJS & Async Patterns](#rxjs--async-patterns)
- [JavaScript Engine & Node.js](#javascript-engine--nodejs)
- [Module Loaders](#module-loaders)
- [Testing Framework](#testing-framework)
- [Class Decorators](#class-decorators)
- [Route Animations](#route-animations)
- [Internationalization (i18n)](#internationalization-i18n)

---

## Angular Basics

- Directives manipulate the DOM / view.
- Directives types:
  - Structural directives: change DOM structure (add/remove elements)
  - Attribute directives: change appearance/behavior
  - Components are directives with a template
- Common directives:
  - `*ngFor` repeats a template
  - `*ngIf` / `*ngSwitch` add/remove DOM elements
  - `ngClass` adds/removes CSS classes
  - `ngStyle` modifies styles based on expressions
- Pipes transform data for display.
- Lifecycle hooks run when Angular creates/updates/destroys components.
- Forms:
  - Reactive forms
  - Template-driven forms
- Services & DI: injector resolves dependencies declared via providers.
- `HttpClient` uses RxJS Observables; you subscribe to receive responses.
- Router: SPA navigation and view switching.

---

## Project Structure

Typical Angular CLI project layout:

```text
├── node_modules
├── e2e
├── dist
├── src
│   ├── app
│   │   ├── app-routing.module.ts
│   │   ├── app.component.*
│   │   ├── app.module.ts
│   ├── assets
│   ├── index.html
│   ├── main.ts
│   ├── polyfills.ts
│   ├── styles.css
│   ├── test.ts
├── angular.json
├── package.json
├── package-lock.json
├── karma.conf.js
├── tsconfig*.json
```

Key files:

- `angular.json`
  - Main configuration for build/serve/test
  - Assets/styles/scripts inclusion
  - Dev/prod build options
- `tsconfig*.json`
  - TypeScript compiler settings
  - Separate configs for app, tests, e2e
- `polyfills.ts`
  - Compatibility support for older browsers

---

## Build & Bundling (Webpack)

- Webpack is the bundler used by Angular CLI.
- `ng build` triggers Webpack and produces the `dist/` output.

Common artifacts in `dist/`:

- `main.*.js`: your app code (components/services/modules)
- `vendor.*.js`: third-party libraries
- `polyfills.*.js`: browser compatibility
- `runtime.*.js`: webpack runtime/module loader
- `styles.*.css`: global styles
- `index.html`: entry file referencing bundles

---

## Node.js in Angular Projects

- Node.js is **not required in production** to run the built Angular app in a browser.
- Node.js is needed for:
  - Developing Angular apps (`ng serve`, `ng build`, etc.)
  - Managing dependencies (`npm`/`yarn`)
  - Running dev/build tooling (bundling, minification, optimization)
  - Running a lightweight dev server during development
- After build (`dist/`), any static web server can serve the files.

---

## Angular CLI & Testing

- Angular CLI creates/manages the project and configures tooling.
- Typical testing setup:
  - Jasmine: unit/integration test framework (assertions/syntax)
  - Karma: test runner (runs tests in real browsers)
  - Protractor/Cypress: end-to-end (E2E) testing

---

## Modules & Standalone Components

### NgModules

`@NgModule` configuration summary:

- `declarations`: components, directives, pipes
- `imports`: other modules
- `providers`: services
- `bootstrap`: root component

Example:

```ts
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule, TestUserAppModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

`BrowserModule` vs `CommonModule`:

- `BrowserModule`
  - Only in root module
  - Contains browser-specific bootstrap services
- `CommonModule`
  - Used in feature modules
  - Provides common directives (`ngIf`, `ngFor`, etc.) without browser bootstrap

### Standalone Components (Angular v14+)

- Declared with `standalone: true`
- Dependencies imported directly into the component

```ts
@Component({
  selector: 'app-hello',
  standalone: true,
  imports: [CommonModule],
  template: `<h1>Hello, Standalone!</h1>`
})
export class HelloComponent {}
```

---

## Bootstrapping Flow

High-level flow:

1. Browser loads `index.html`
2. Bundled JS/CSS are loaded
3. `main.ts` runs
4. Root module (`AppModule`) bootstraps
5. Root component (`AppComponent`) renders into `<app-root>`

---

## Components

A component usually has:

- `.ts`: logic
- `.html`: template
- `.css/.scss`: styles
- `.spec.ts`: tests

Example:

```ts
@Component({
  selector: 'app-hello-world',
  templateUrl: './hello-world.component.html',
  styleUrls: ['./hello-world.component.css']
})
export class HelloWorldComponent {
  title = 'Hello World';
}
```

---

## Lifecycle Hooks

- Lifecycle hooks are callbacks Angular invokes as it creates/updates/destroys components/directives.

Common hooks (high level):

- `ngOnChanges`: when an `@Input()` changes (strict reference check)
- `ngOnInit`: once after first `ngOnChanges` (good for initialization)
- `ngDoCheck`: every change detection cycle (custom checks)
- `ngAfterContentInit`: after projected content (`ng-content`) initializes
- `ngAfterContentChecked`: after every check of projected content
- `ngAfterViewInit`: after component view + child views initialize
- `ngAfterViewChecked`: after every check of component view + child views
- `ngOnDestroy`: cleanup (unsubscribe, remove handlers)

Notes:

- `ngOnChanges` only fires if you have `@Input()` and it changes.
- For objects/arrays, mutation doesn't change reference, so `ngOnChanges` may not fire unless you replace with a new object.

---

## Data Binding

### 1) Interpolation (one-way)

- Component -> view
- `{{ expression }}`
- Supports pipes: `{{ title | uppercase }}`
- Avoid heavy methods in templates (called every change detection cycle)

`ngNonBindable` prevents Angular from binding inside an element.

### 2) Property Binding (one-way)

- Component -> DOM property
- `[target]="source"`

```html
<h1 [innerText]="title"></h1>
<button [disabled]="isDisabled">I am disabled</button>
<button [attr.aria-label]="closeLabel">X</button>
```

### 3) Event Binding (one-way)

- View -> component method
- `(event)="handler()"`

```html
<button (click)="onSave()">Save</button>
<input (input)="handleInput($event)">
```

### 4) Two-way Binding

- `[(...)]` syntax
- Common for forms using `ngModel` (requires `FormsModule`)

```html
<input type="text" name="value" [(ngModel)]="value" />
```

Custom two-way binding pattern:

```ts
@Input() count: number = 0;
@Output() countChange = new EventEmitter<number>();
```

```html
<counter [(count)]="count"></counter>
```

---

## Directives

- Structural: `*ngIf`, `*ngFor`, `*ngSwitch`
- Attribute: `ngClass`, `ngStyle`, custom attribute directives

Create a directive:

```bash
ng generate directive directive-name
```

Example custom attribute directive:

```ts
@Directive({ selector: '[appHighlight]' })
export class HighlightDirective implements OnInit {
  @Input() appHighlight: string = 'yellow';

  constructor(private el: ElementRef, private renderer: Renderer2) {}

  ngOnInit() {
    this.renderer.setStyle(this.el.nativeElement, 'backgroundColor', this.appHighlight);
  }
}
```

### `*ngFor`

```html
<tr *ngFor="let customer of customers;">
  <td>{{customer.customerNo}}</td>
  <td>{{customer.name}}</td>
  <td>{{customer.address}}</td>
  <td>{{customer.city}}</td>
  <td>{{customer.state}}</td>
</tr>
```

Local variables:

```html
<tr
  *ngFor="let movie of movies; let i=index; let o=odd; let e=even;"
  [ngClass]="{ odd: o, even: e }"
>
  <td>{{i}}</td>
  <td>{{movie.title}}</td>
  <td>{{movie.director}}</td>
  <td>{{movie.cast}}</td>
  <td>{{movie.releaseDate}}</td>
</tr>
```

`trackBy` improves performance for large/changing lists by letting Angular identify items by an id instead of re-rendering all DOM nodes.

```ts
trackById(index: number, item: any): number {
  return item.id;
}
```

```html
<li *ngFor="let user of users; trackBy: trackById">{{ user.name }}</li>
```

### `*ngSwitch`

```html
<div [ngSwitch]="switchExpression">
  <div *ngSwitchCase="matchExpression1">First Template</div>
  <div *ngSwitchCase="matchExpression2">Second Template</div>
  <div *ngSwitchDefault>Default Template</div>
</div>
```

### `*ngIf` vs `[hidden]`

- `*ngIf` adds/removes elements from the DOM.
- `[hidden]` only hides the element (it stays in the DOM).

If/else with templates:

```html
<div *ngIf="condition; then thenBlock else elseBlock">
  This content is not shown
</div>

<ng-template #thenBlock>
  content to render when the condition is true.
</ng-template>

<ng-template #elseBlock>
  content to render when condition is false.
</ng-template>
```

### `ngClass` / `ngStyle`

```html
<div [ngClass]="'first second'">...</div>
<div [ngStyle]="{ 'background-color': isActive ? 'lightgreen' : 'transparent' }">...</div>
```

### Custom Structural Directive (example)

```ts
@Directive({ selector: '[appUnless]' })
export class UnlessDirective {
  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef
  ) {}

  @Input() set appUnless(condition: boolean) {
    if (!condition) {
      this.viewContainer.createEmbeddedView(this.templateRef);
    } else {
      this.viewContainer.clear();
    }
  }
}
```

```html
<p *appUnless="isLoggedIn">You are NOT logged in!</p>
```

Note: a component/directive/pipe cannot be declared in more than one module.

---

## Pipes

- Format/transform data in templates
- Syntax: `expression | pipeOperator[:args]`

Pipe chaining:

```html
{{ toDate | date | uppercase }}
```

Currency pipe:

```html
{{ amount | currency:'INR':true:'4.2-2' }}
```

Date pipe:

```html
{{ dateValue | date:'medium' }}
```

Using pipes in TypeScript (example):

```ts
import { DatePipe } from '@angular/common';

const datePipe = new DatePipe('en-US');
const formattedDate = datePipe.transform(new Date(), 'fullDate');
```

Custom pipe notes:

- Create a pipe class
- Decorate with `@Pipe({ name: '...' })`
- Implement `PipeTransform` and the `transform()` method
- Declare in module (if not standalone)

Pure vs impure pipes:

- Pure pipes run only when input reference changes (default)
- Impure pipes run every change detection cycle (`pure: false`)

Custom pipe example:

```ts
@Pipe({ name: 'tempConverter' })
export class TempConverterPipe implements PipeTransform {
  transform(value: number, unit: string) {
    if (value && !isNaN(value)) {
      if (unit === 'C') {
        const temperature = (value - 32) / 1.8;
        return temperature.toFixed(2);
      }
      if (unit === 'F') {
        const temperature = (value * 1.8) + 32;
        return temperature.toFixed(2);
      }
    }
    return;
  }
}
```

Impure pipe example:

```ts
@Pipe({ name: 'impureFilter', pure: false })
export class ImpureFilterPipe implements PipeTransform {
  transform(items: any[], searchText: string): any[] {
    if (!items || !searchText) return items;
    return items.filter(item => item.name.toLowerCase().includes(searchText.toLowerCase()));
  }
}
```

```html
{{ toDate | date:'medium' }}
```

---

## Forms

Two approaches:

- Reactive forms
- Template-driven forms

Reactive forms (recommended for complex/dynamic forms):

- Explicit form model in TypeScript
- Uses `ReactiveFormsModule`
- Building blocks:
  - `FormControl`: single input
  - `FormGroup`: group of named controls
  - `FormArray`: array of controls/groups
  - `FormRecord`: like `FormGroup`, but for dynamic keys

Template-driven forms (good for simple forms):

- Form logic mostly in template
- Uses `FormsModule`
- Uses `ngForm` + `ngModel`

Reference:

- https://www.tektutorialshub.com/angular/how-to-set-value-in-template-driven-forms-in-angular/

Template-driven example:

```html
<form #userForm="ngForm" (ngSubmit)="onSubmit(userForm)">
  <input name="userName" ngModel required minlength="3" #userName="ngModel" placeholder="User Name">
  <div *ngIf="userName.invalid && userName.touched">
    <small *ngIf="userName.errors?.required">User Name is required.</small>
    <small *ngIf="userName.errors?.minlength">Minimum 3 characters.</small>
  </div>

  <input type="email" name="email" ngModel required #email="ngModel" placeholder="Email">
  <div *ngIf="email.invalid && email.touched">
    <small *ngIf="email.errors?.required">Email is required.</small>
    <small *ngIf="email.errors?.email">Invalid email.</small>
  </div>

  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

Reactive example:

```ts
userForm = new FormGroup({
  userName: new FormControl('', [Validators.required, Validators.minLength(3)]),
  email: new FormControl('', [Validators.required, Validators.email])
});
```

```html
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <input formControlName="userName" placeholder="User Name">
  <input type="email" formControlName="email" placeholder="Email">
  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

---

## Component Communication

1. Parent -> child

- Pass data via `@Input()`
- Use `ngOnChanges` (or an `@Input()` setter) when you need to react to input changes

Example:

```ts
export class ChildComponent {
  @Input() count!: number;
}
```

```html
<child-component [count]="clickCounter"></child-component>
```

2. Child -> parent

- Emit events via `@Output()` + `EventEmitter`

Example:

```ts
export class ChildComponent {
  @Output() countChanged = new EventEmitter<number>();

  increment() {
    this.countChanged.emit(1);
  }
}
```

```html
<child-component (countChanged)="countChangedHandler($event)"></child-component>
```

3. Sibling / no parent-child relationship

- Share data via a service using `Subject`/`BehaviorSubject`

Example service pattern:

```ts
private messageSource = new Subject<string>();
message$ = this.messageSource.asObservable();

sendMessage(message: string) {
  this.messageSource.next(message);
}
```

---

## Services & Dependency Injection

- `@Injectable({ providedIn: 'root' })` gives a singleton service in the root injector (recommended)
- Using `providers: [MyService]` in a module/component scopes instances

```ts
@Injectable({ providedIn: 'root' })
export class MyService {}
```

---

## HTTP (HttpClient) & Interceptors

- `HttpClient` returns Observables.
- Interceptors can modify requests/responses (auth tokens, logging, error handling).

Basic setup:

```ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [HttpClientModule]
})
export class AppModule {}
```

Interceptor example:

```ts
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const authReq = req.clone({
      setHeaders: { Authorization: 'Bearer my-token' }
    });
    return next.handle(authReq);
  }
}
```

Register:

```ts
providers: [
  { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
]
```

---

## Routing

Basic routes:

```ts
const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserComponent },
  { path: 'dashboard', component: DashboardComponent, children: [
    { path: 'stats', component: StatsComponent }
  ]},
  { path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) },
  { path: '**', component: NotFoundComponent }
];
```

- `RouterModule.forRoot(routes)` for root module
- `RouterModule.forChild(routes)` for feature modules
- `<router-outlet>` is where routed components render

### Lazy loading

```ts
{ path: 'user', loadChildren: () => import('./user/user.module').then(m => m.UserModule) }
```

Preloading strategies:

- `NoPreloading` (default)
- `PreloadAllModules`

```ts
RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })
```

---

## Route Guards

Guard types:

- `CanActivate`
- `CanDeactivate`
- `CanLoad`
- `CanActivateChild`
- `Resolve`

Example `CanActivate`:

```ts
@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
  canActivate(): boolean {
    return isLoggedIn();
  }
}
```

---

## Resolvers

- Fetch data before a route activates

```ts
@Injectable({ providedIn: 'root' })
export class MyResolver implements Resolve<any> {
  constructor(private dataService: DataService) {}
  resolve() {
    return this.dataService.getData();
  }
}
```

Route:

```ts
{ path: 'details/:id', component: DetailsComponent, resolve: { item: MyResolver } }
```

---

## Error Handling

Option 1: handle in pipe:

```ts
this.http.get('/api/data').pipe(
  catchError(error => throwError(() => error))
).subscribe({
  next: data => {},
  error: err => {}
});
```

Option 2: Global error handler:

```ts
@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  handleError(error: any): void {
    console.error('Global error:', error);
  }
}
```

Option 3: Error interceptor (HTTP errors).

---

## Async Pipe

- Subscribes to an Observable/Promise in the template
- Auto-unsubscribes when the component is destroyed

```html
{{ expression | async }}
```

---

## RxJS & Async Patterns

### Promises

- `resolve(value)` fulfills the promise
  - This function is called when asynchronous operation completes successfully. It changes the Promise's state to "fulfilled" and passes the value to the `.then()` handler
- `reject(error)` rejects the promise
  - This function is called when asynchronous operation fails. It changes the Promise's state to "rejected" and passes the error to the `.catch()` handler
- `then(handler)` runs when the promise is fulfilled; receives the resolved value
- `catch(handler)` runs when the promise is rejected; receives the error
- `finally(handler)` runs when the promise is settled (fulfilled or rejected); useful for cleanup

Promise example:

```ts
const myPromise = new Promise<string>((resolve, reject) => {
  if (/* success condition */) {
    resolve('Success!');
  } else {
    reject('Something went wrong.');
  }
});

myPromise
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

Promise example with `finally`:

```ts
myPromise
  .then(result => console.log(result))
  .catch(error => console.error(error))
  .finally(() => console.log('Complete'));
```

Fetch example:

```ts
fetch('url')
  .then(response => response.json())
  .catch(error => console.error(error))
  .finally(() => console.log('Done'));
```

Observable example (`of` + `pipe`):

```ts
import { of } from 'rxjs';
import { map, catchError } from 'rxjs/operators';

of(1, 2, 3)
  .pipe(
    map(x => x * 2),
    catchError(err => of('error'))
  )
  .subscribe({
    next: value => console.log(value),
    error: error => console.error(error),
    complete: () => console.log('Complete')
  });
```

### Subjects

- `Subject`
  - no current value
  - subscribers only receive values after they subscribe
  - Subject is a special type of observable that allows values to be multicasted to many observers.
  - It does not hold a current value.
  - New subscribers do not receive values emitted before they subscribe (they do not get any previous values).

Example:

```ts
import { Subject } from 'rxjs';

const subject = new Subject<number>();
subject.subscribe(v => console.log('A', v));
subject.next(1);
```

Example (multiple subscribers):

```ts
import { Subject } from 'rxjs';

const subject = new Subject<number>();

subject.subscribe(value => console.log('Subscriber 1:', value));
subject.next(1); // Subscriber 1: 1
subject.next(2); // Subscriber 1: 2

subject.subscribe(value => console.log('Subscriber 2:', value));
subject.next(3); // Subscriber 1: 3, Subscriber 2: 3

// Subscriber 2 does NOT receive 1 or 2
```

- `BehaviorSubject`
  - holds a current value
  - emits the latest value immediately to new subscribers
  - requires an initial value

BehaviorSubject notes:

- BehaviorSubject holds a current value and emits it immediately to new subscribers.
- You must provide an initial value when creating a BehaviorSubject.
- New subscribers always receive the latest value, even if they subscribe after values have been emitted.

Example:

```ts
import { BehaviorSubject } from 'rxjs';

const behaviorSubject = new BehaviorSubject<number>(0); // initial value is 0

behaviorSubject.subscribe(value => console.log('Subscriber 1:', value)); // Subscriber 1: 0
behaviorSubject.next(1); // Subscriber 1: 1
behaviorSubject.next(2); // Subscriber 1: 2

behaviorSubject.subscribe(value => console.log('Subscriber 2:', value)); // Subscriber 2 immediately receives 2
behaviorSubject.next(3); // Subscriber 1: 3, Subscriber 2: 3
```

Summary table:

| Feature | Subject | BehaviorSubject |
| --- | --- | --- |
| Holds current value? | No | Yes |
| Initial value required? | No | Yes |
| New subscribers get | Only new events | Latest value + new events |

Tip: Use BehaviorSubject when you need to store and provide the latest value to new subscribers (e.g., for state management). Use Subject when you only care about events after subscription.

Observables vs Promises summary:

- Promises: single value, not cancellable
- Observables: stream, cancellable (unsubscribe), supports operators

---

## JavaScript Engine & Node.js

- JavaScript files are run on the browser by the browser's built-in JavaScript engine (like V8 in Chrome or SpiderMonkey in Firefox).
- V8 engine converts JS code to machine code.

---

## Module Loaders

- SystemJS
- WebPack

---

## Testing Framework

- Jasmine testing framework

---

## Class Decorators

- `@Component`
- `@Directive`
- `@Injectable`
- `@NgModule`
- `@Pipe`

---

## Route Animations

- Route animation in Angular refers to adding visual transitions when navigating between different routes (pages or components) in your application.
- This enhances user experience by smoothly animating the entry and exit of routed components.
- Use Angular's animation system with the @routeAnimations trigger and <router-outlet [@routeAnimations]>.

---

## Internationalization (i18n)

- Angular provides built-in support for internationalization (i18n) to create multi-language applications.
- Use the `i18n` attribute in templates to mark text for translation.
- Extract translations with `ng extract-i18n`.
- Build locale-specific versions with `ng build --localize`.

---
