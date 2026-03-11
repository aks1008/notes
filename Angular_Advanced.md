# Angular Advanced Topics

## Table of Contents

- [Microservices (Reference Links)](#microservices-reference-links)
- [Change Detection](#change-detection)
- [Performance Optimization](#performance-optimization)
- [Security](#security)
- [State Management](#state-management)
- [Zones, Change Detection, and Signals](#zones-change-detection-and-signals)
- [SSR (Angular Universal) and SEO](#ssr-angular-universal-and-seo)
- [Misc Notes](#misc-notes)

---

## Microservices (Reference Links)

- https://www.atlassian.com/microservices/microservices-architecture/microservices-vs-monolith
- https://12factor.net/

---

## Change Detection

- Change detection checks for model changes and updates the view.
- You can configure strategies per component.

`OnPush` change detection strategy:

- Runs checks only when:
  - An input reference changes
  - An event originates from the component or its children
  - You manually trigger change detection

```ts
@Component({
  selector: 'app-example',
  templateUrl: './example.component.html',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ExampleComponent {}
```

---

## Performance Optimization

- AOT compilation
- Tree shaking
- Code splitting (lazy loading)
- `trackBy` in `*ngFor`
- Memoization for expensive computations

---

## Security

- XSS
- CSRF
- Sanitization
- Best practices

---

## State Management

### Why State Management

- State management refers to how we handle and maintain data across components in our application. This data could include:
  - User information
  - Application settings
  - Form data
  - API responses
  - UI state (loading indicators, toggle states)

- We can pass data from one parent to child, but when it comes to child to another child communication, it's very hectic to pass data from one child to parent and then from parent to another child, which is very error prone, inefficient way, hence state management helps us with:
  - Single source of truth
  - Decoupling of components
  - Predictability & debugging
  - Performance improvement
  - Easier testing

### Real-World Use Cases That Demand State Management

- **User Authentication & Profile**: You log in once; multiple components—headers, sidebars, dashboards—need to know who you are, what permissions you have, and maybe refresh your token behind scenes.

- **Shopping Cart**: Items get added on a product page, updated on a cart preview widget, and finally processed in a checkout form. All of these live in different parts of app.

- **Multi-Step Forms or Wizards**: You collect information across several screens. Later steps depend on answers from earlier steps—and you need to validate or even roll back changes.

- **UI Theme & Preferences**: A user picks a dark theme or a language setting once. That choice should immediately apply everywhere without drilling the value through every component.

- **Notifications & Global Alerts**: Error or success messages triggered deep in your app need to surface in a top-level notification component.

- **Real-Time Data Streams** (e.g., Chat, Live Feeds): Incoming WebSocket messages or SSE events must update multiple widgets: chat windows, unread-count badges, activity logs.

### Approaches to State Management

1. **Simple Service Based state management**

```ts
// user-state.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

interface User {
  id: number;
  name: string;
  email: string;
}

@Injectable({
  providedIn: 'root'
})
export class UserStateService {
  private userSubject = new BehaviorSubject<User | null>(null);
  user$ = this.userSubject.asObservable();

  setUser(user: User) {
    this.userSubject.next(user);
  }

  clearUser() {
    this.userSubject.next(null);
  }

  getCurrentUser(): User | null {
    return this.userSubject.getValue();
  }
}
```

```ts
// user-profile.component.ts
import { Component, OnInit } from '@angular/core';
import { UserStateService } from './user-state.service';

@Component({
  selector: 'app-user-profile',
  template: `
    <div *ngIf="user$ | async as user">
      <h2>Welcome, {{ user.name }}!</h2>
      <p>Email: {{ user.email }}</p>
    </div>
  `
})
export class UserProfileComponent implements OnInit {
  user$ = this.userState.user$;

  constructor(private userState: UserStateService) {}

  ngOnInit() {
    // Simulating user login
    this.userState.setUser({
      id: 1,
      name: 'John Doe',
      email: 'john@example.com'
    });
  }
}
```

2. **Angular Signals: The New State Management Approach**

```ts
// counter.service.ts
import { Injectable, signal, computed } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CounterService {
  // Create a signal with initial value
  private count = signal(0);

  // Create a computed signal
  doubleCount = computed(() => this.count() * 2);

  increment() {
    // Update signal value
    this.count.update(current => current + 1);
  }

  decrement() {
    this.count.update(current => current - 1);
  }

  getCount() {
    return this.count;
  }
}
```

```ts
// counter.component.ts
import { Component } from '@angular/core';
import { CounterService } from './counter.service';

@Component({
  selector: 'app-counter',
  template: `
    <div>
      <h2>Counter: {{ counterService.getCount()() }}</h2>
      <h3>Double Count: {{ counterService.doubleCount() }}</h3>
      <button (click)="counterService.increment()">Increment</button>
      <button (click)="counterService.decrement()">Decrement</button>
    </div>
  `
})
export class CounterComponent {
  constructor(public counterService: CounterService) {}
}
```

3. **NgRx**

Set up NgRx in a fresh Angular app: `ng add @ngrx/store @ngrx/effects`

Define a simple feature:
- Model: (e.g., Task with id/title)
- Actions: (add, update, delete)
- Reducer: to handle actions
- Selector: to query state

---

## Zones, Change Detection, and Signals

### Zones (Zone.js)

- Angular uses Zone.js to track async operations and trigger change detection.
- `NgZone` is the main zone Angular uses.

More details:

- Zones refer to a mechanism provided by the Zone.js library that helps Angular automatically detect and respond to asynchronous operations, such as user events, HTTP requests, or timers.
- Zones allow Angular to know when to update the UI by tracking when asynchronous tasks start and finish. When an async operation completes, Zone.js notifies Angular, which then triggers change detection to update the view if necessary.
- Zone.js patches async APIs (e.g., `setTimeout`, Promises, XHR) so Angular can detect when async work starts/finishes.
- When async work completes, Angular can run change detection to update the view.
- You can run work *outside* Angular's zone to reduce change detection frequency (performance), and re-enter when UI updates are needed.
- Key points:
  - Zone.js patches async APIs (like `setTimeout`, Promises, XHR) to track when they run
  - Angular uses zones to know when to run change detection automatically
  - The main zone used by Angular is called `NgZone`
  - You can run code inside or outside Angular's zone to control when change detection happens (for performance improvement)
  - In summary, zones are essential for Angular's automatic UI updates in response to async events

### Signals

- Signals are synchronous reactivity primitives (a value container that notifies dependents when it changes).
- Benefits:
  - Fine-grained updates
  - Predictable state changes
  - Less boilerplate

More details:

- In Angular, signals are a new reactivity primitive introduced to make state management and change detection more predictable and efficient.
- What are signals?
  - A signal is a special object that holds a value and notifies subscribers when that value changes
  - Signals are similar to observables but are synchronous and have a simpler API
  - You can read a signal's value directly, and when you update it, Angular can automatically update the UI or trigger dependent computations
- Why use signals?
  - Fine-grained reactivity: Only the parts of the UI that depend on a signal are updated when that value changes, improving performance
  - Predictable updates: Signals make it easier to track and reason about state changes
  - Simpler code: Signals reduce the need for manual change detection and boilerplate code
  - Integration: Signals work seamlessly with Angular's new reactive features and can be used alongside existing code
  - In summary, signals provide a modern, efficient, and easy-to-use way to manage reactive state in Angular applications.
- A signal holds a value and notifies subscribers when the value changes.
- Similar to Observables conceptually, but synchronous with a simpler API.
- Updating a signal can automatically update dependent computations/UI.

---

## SSR (Angular Universal) and SEO

Angular Universe:

- "Angular Universe" refers to the broader Angular ecosystem (official packages like Angular Material/CLI/Universal and community tooling).
- Angular Universe is a term used to describe the ecosystem of tools, libraries, and features that extend Angular's core capabilities. It includes official Angular packages like Angular Material, Angular CLI, Angular Universal, etc., and community solutions.

### SSR (Angular Universal)

- Server-side rendering improves initial load and SEO.
- SSR renders HTML on the server for the requested route, then Angular takes over in the browser (hydration) to make the app interactive.
- SSR stands for Server-Side Rendering. In Angular, SSR is implemented using Angular Universal.
- Key points about SSR (Angular Universal):
  - SSR allows Angular applications to be rendered on the server instead of only in the browser
  - The server generates the HTML for a requested route and sends it to the client, improving initial load performance and SEO
  - After the initial HTML is loaded, Angular takes over on the client side (hydration), making the app interactive
  - SSR is especially useful for public-facing sites that need fast load times and good search engine indexing
- In summary:
  - Angular Universe refers to the broader Angular ecosystem
  - SSR (via Angular Universal) enables server-side rendering for better performance and SEO
- Setup (from notes):
  - `ng add @nguniversal/express-engine`
  - Build/serve scripts: `npm run build:ssr`, `npm run serve:ssr`

### SEO checklist

- Use SSR where needed
- Set title/meta tags per route using `Title` and `Meta`
- Add structured data (`application/ld+json`)
- Add `sitemap.xml`
- Add `robots.txt`
- Prefer descriptive URLs
- Use semantic HTML (`<header>`, `<main>`, `<article>`, `<footer>`)
- Optimize performance (lazy loading, image optimization, prod builds)

To make your Angular app SEO-friendly, you should focus on the following key areas:

Enable Server-Side Rendering (SSR) with Angular Universal

- Use Angular Universal to render your app on the server
- This improves SEO by allowing search engines to crawl your content
- Implementation:
  - Run `ng add @nguniversal/express-engine` in your app directory
  - This sets up SSR with Express and updates your build scripts
  - Build and serve with `npm run build:ssr` and `npm run serve:ssr`

Use Meta Tags and Structured Data

- Add dynamic meta tags for each route/page
- Implementation:
  - Import `Title` and `Meta` from `angular/platform-browser` in your components
  - Use `this.titleService.setTitle('Page Title')` and `this.metaService.updateTag({ name: 'description', content: 'Page description' })`
  - For structured data, add `<script type="application/ld+json">` in your component templates or `index.html`

Create an XML Sitemap

- Helps search engines discover all your pages
- Implementation:
  - Manually create a `sitemap.xml` in your public assets folder, or use a tool to generate it
  - List all important routes
  - Reference it in your `robots.txt`

Use Descriptive URLs and Routing

- Ensure routes are clean and keyword-rich
- Implementation:
  - Define routes in your `app.routes.ts` with meaningful path names (e.g., `path: 'advisor-reviews/:id'`)
  - Avoid query params for main content

Optimize Content for Crawlers

- Use semantic HTML and alt attributes
- Implementation:
  - Use `<header>`, `<main>`, `<article>`, `<footer>`, etc.
  - Add `alt` attributes to all `<img>` tags
  - Avoid hiding important content behind JavaScript

Improve Performance

- Fast-loading pages are favored by search engines
- Implementation:
  - Use Angular lazy loading for modules (`loadChildren` in routes)
  - Optimize images in `assets/images`
  - Minimize bundle size using production builds (`ng build --prod`)

Robots.txt

- Guides search engine crawlers
- Implementation:
  - Create a `robots.txt` file in your assets or root directory
  - Example:
    - `User-agent: *`
    - `Allow: /`
    - `Sitemap: https://yourdomain.com/sitemap.xml`

Example `robots.txt`:

```text
User-agent: *
Allow: /
Sitemap: https://yourdomain.com/sitemap.xml
```

Meta tags implementation example:

```ts
import { Title, Meta } from '@angular/platform-browser';

constructor(private titleService: Title, private metaService: Meta) {}

ngOnInit() {
  this.titleService.setTitle('Page Title');
  this.metaService.updateTag({
    name: 'description',
    content: 'Page description'
  });
}
```

Note:

- Consider setting a canonical URL per page/route when applicable.

---

## Misc Notes

### Containers (high level)

`container image` -> `container instance` -> `container host` -> `OS`

### C# quick notes

- Latest version (2024): C# 13

Access modifiers:

- `public`: accessible inside/outside assembly
- `private`: accessible only inside the class
- `internal`: accessible within the assembly
- `protected`: accessible in the class and its derived classes

Types:

- value type
- reference type
- pointer type

Explicit vs implicit typing:

```csharp
int i = 100;   // explicitly typed variable
var j = 100;   // implicitly typed local variable
```

Implicitly-typed variables must be initialized at declaration.

Anonymous type:

```csharp
var anony_object = new {
    s_id = 134,
    s_name = "Siya",
    language = "Ruby"
};
```

`default` literal (C# 7.1+):

```csharp
int i = default;       // 0
float f = default;     // 0
decimal d = default;   // 0
bool b = default;      // false
char c = default;      // '\0'
```

`DateTime` helpers:

```csharp
DateTime currentDateTime = DateTime.Now;      // current date & time
DateTime todaysDate = DateTime.Today;         // today's date
DateTime currentDateTimeUTC = DateTime.UtcNow;// current UTC date & time

DateTime maxDateTimeValue = DateTime.MaxValue;
DateTime minDateTimeValue = DateTime.MinValue;
```

### JavaScript Engine Details

- JavaScript files are run on the browser by the browser's built-in JavaScript engine (like V8 in Chrome or SpiderMonkey in Firefox).
- V8 engine converts JS code to machine code.

### Module Loaders Comparison

- **SystemJS**: Universal module loader that supports ES modules, AMD, CommonJS, and global scripts
- **Webpack**: Module bundler that scans the application looking for JavaScript files and merges them into one (or more) big files
- Webpack has the ability to bundle any kind of file like JavaScript, CSS, SASS, LESS, images, HTML, & fonts, etc.
- The Angular CLI uses Webpack as a module bundler. Webpack needs a lot of configuration options to work correctly. The Angular CLI sets up all these configuration options behind the scene.
- main-es2015.js -- es2015 [es6] -- is for new modern browser
- main-es5.js - older browser
- Webpack automates the process of combining, transforming, and optimizing your project's files for efficient delivery to the browser.
- When we call `ng build` command, then webpack will be called, and dist folder will be created

On ng build command, following files gets created in dist folder:

- main.js → Your app's code → your components, services, modules, etc
- vendor.js → Third-party libraries
- polyfills.js → Browser compatibility scripts
- runtime.js → Webpack runtime/module loader → this small file helps load and execute the other JavaScripts bundles efficiently
- styles.css → Global CSS styles
- index.html → Main starting html file which contain src tag for main.js, vendor.js files
- other map files [to map actual js works with minified js words]
