# Angular Zero to Intermediate: The Complete Practical Guide

Welcome to Angular! This guide is designed to take you from **absolute zero** (no prior Angular experience) to an **intermediate level**, using modern Angular (standalone components, signals, and new template control flow).

No useless gimmicks or calculator projects here—only real-world concepts explained simply with practical code you can test directly in this repository.

---

## 🗺️ Learning Roadmap

```text
Level 1: The Core Mental Model (How Angular Works)
   ↓
Level 2: Data Binding & Interaction (Connecting TypeScript to HTML)
   ↓
Level 3: Modern Reactivity with Signals (signal, computed, update)
   ↓
Level 4: Control Flow (@if, @for, @switch)
   ↓
Level 5: Multi-Component Architecture (Parent ↔ Child Communication)
   ↓
Level 6: Services & Dependency Injection (Sharing State & Logic)
   ↓
Level 7: Handling Forms & User Input (Validation & Two-way Binding)
   ↓
Level 8: Routing & Navigation (Multi-Page Experience)
   ↓
Level 9: HTTP & Working with APIs (Connecting to a Backend)
```

---

## Level 1: The Core Mental Model

### 1. What is Angular?
Angular is a TypeScript-based framework for building web applications. In plain HTML/JS, you write scripts that manually find elements (`document.getElementById`) and update them. Angular automates this: when your data changes in TypeScript, the UI updates automatically.

### 2. How an Angular App Starts
When you open an Angular application in the browser:

```text
index.html  -->  src/main.ts  -->  src/app/app.config.ts  -->  src/app/app.ts  -->  Screen
```

1. **`src/index.html`**: The single HTML page. It has a custom tag: `<app-root></app-root>`.
2. **`src/main.ts`**: The entry JavaScript/TypeScript file. It bootstraps (starts) `AppComponent`.
3. **`src/app/app.ts`**: The main root component class.
4. **`src/app/app.html`**: The root component's template (what actually gets injected into `<app-root>`).

### 3. What is a Component?
Everything you see on screen is built with **Components**. Think of a component like a custom LEGO block.

A component consists of 3 pieces:
- **Logic (`.ts`)**: Defines data, variables, and functions.
- **Template (`.html`)**: Defines the HTML structure.
- **Styles (`.css`)**: Defines styling scoped only to this component.

Here is the simplest modern Angular component:

```ts
// src/app/app.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',      // The HTML tag name: <app-root></app-root>
  standalone: true,          // Modern Angular: no NgModule required
  templateUrl: './app.html', // Path to HTML
  styleUrl: './app.css'      // Path to CSS
})
export class AppComponent {
  userName = 'Alex';
  role = 'Developer';
}
```

---

## Level 2: Data Binding & User Interaction

Data binding is the bridge between your **TypeScript class** and your **HTML template**. There are 4 fundamental types:

### 1. Interpolation: `{{ expression }}`
Displays data from TypeScript inside the HTML.

**TypeScript (`app.ts`):**
```ts
export class AppComponent {
  courseTitle = 'Angular Masterclass';
  studentsCount = 42;
}
```

**HTML (`app.html`):**
```html
<h1>{{ courseTitle }}</h1>
<p>Total Students: {{ studentsCount }}</p>
```

---

### 2. Property Binding: `[property]="value"`
Sets an HTML attribute or property dynamically from TypeScript.

**TypeScript (`app.ts`):**
```ts
export class AppComponent {
  profileImageUrl = 'https://picsum.photos/200';
  isSubmitDisabled = true;
}
```

**HTML (`app.html`):**
```html
<img [src]="profileImageUrl" alt="Profile" />
<button [disabled]="isSubmitDisabled">Submit</button>
```

---

### 3. Event Binding: `(event)="handler()"`
Listens to user interactions (clicks, keypresses, input changes) and triggers a TypeScript method.

**TypeScript (`app.ts`):**
```ts
export class AppComponent {
  message = '';

  onSaveClick() {
    this.message = 'Saved successfully at ' + new Date().toLocaleTimeString();
  }
}
```

**HTML (`app.html`):**
```html
<button (click)="onSaveClick()">Save Changes</button>
<p>{{ message }}</p>
```

---

### 4. Two-Way Binding: `[(ngModel)]="value"`
Synchronizes an `<input>` field directly with a TypeScript variable in real-time. If the user types, the variable changes; if the variable changes, the input updates.

> **Requirement:** To use `[(ngModel)]`, import `FormsModule` in your component's `imports` array.

**TypeScript (`app.ts`):**
```ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-root',
  imports: [FormsModule],
  templateUrl: './app.html'
})
export class AppComponent {
  searchQuery = 'Angular signals';
}
```

**HTML (`app.html`):**
```html
<input [(ngModel)]="searchQuery" placeholder="Search..." />
<p>You are searching for: {{ searchQuery }}</p>
```

---

## Level 3: Modern Reactivity with Signals

In modern Angular (v16+ and v17/18/19/20+), **Signals** are the primary way to manage reactive state.

A Signal is a wrapper around a value that notifies Angular whenever it changes, so only the exact part of the page that uses that value updates.

### 1. Creating and Reading Signals
```ts
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.html'
})
export class AppComponent {
  // 1. Declare a signal with initial value
  userName = signal('Sarah');
  likesCount = signal(0);

  // 2. Modify signals
  giveLike() {
    // update() takes previous value and returns new value
    this.likesCount.update(prev => prev + 1);
  }

  resetName() {
    // set() directly overwrites the value
    this.userName.set('Guest User');
  }
}
```

**In HTML template:**
Call the signal like a function: `userName()` or `likesCount()`.

```html
<h2>User: {{ userName() }}</h2>
<p>Likes: {{ likesCount() }}</p>

<button (click)="giveLike()">👍 Like</button>
<button (click)="resetName()">Reset</button>
```

---

### 2. Computed Signals (`computed`)
A computed signal calculates its value automatically from other signals. It is read-only and cached.

```ts
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.html'
})
export class AppComponent {
  price = signal(100);
  quantity = signal(3);

  // Automatically recalculates when price() or quantity() changes
  totalPrice = computed(() => this.price() * this.quantity());
}
```

```html
<p>Price: ${{ price() }}</p>
<p>Qty: {{ quantity() }}</p>
<strong>Total: ${{ totalPrice() }}</strong>
```

---

## Level 4: Modern Control Flow (`@if`, `@for`, `@switch`)

Angular uses built-in template syntax that is clean and readable (no need to import `CommonModule` or `*ngIf`/`*ngFor`).

### 1. Conditional Rendering: `@if` and `@else`

**TypeScript:**
```ts
export class AppComponent {
  isLoggedIn = signal(false);

  toggleLogin() {
    this.isLoggedIn.update(v => !v);
  }
}
```

**HTML:**
```html
<button (click)="toggleLogin()">
  {{ isLoggedIn() ? 'Log Out' : 'Log In' }}
</button>

@if (isLoggedIn()) {
  <div class="welcome-box">
    <h3>Welcome back to your dashboard!</h3>
  </div>
} @else {
  <div class="guest-box">
    <p>Please log in to continue.</p>
  </div>
}
```

---

### 2. Looping over Collections: `@for` with `track`
The `track` keyword is required—it tells Angular how to uniquely identify items to ensure high performance when items are added, removed, or reordered.

**TypeScript:**
```ts
export class AppComponent {
  courses = signal([
    { id: 101, title: 'TypeScript Foundations', level: 'Beginner' },
    { id: 102, title: 'Angular Core Concepts', level: 'Intermediate' },
    { id: 103, title: 'RxJS & State Management', level: 'Advanced' }
  ]);
}
```

**HTML:**
```html
<ul>
  @for (course of courses(); track course.id; let i = $index) {
    <li>
      #{{ i + 1 }} - <strong>{{ course.title }}</strong> ({{ course.level }})
    </li>
  } @empty {
    <li>No courses available right now.</li>
  }
</ul>
```

> **Notice `@empty`**: If the array is empty, Angular automatically displays the `@empty` block.

---

### 3. Switch Statements: `@switch`
```html
@switch (userRole()) {
  @case ('admin') {
    <p>Admin Control Panel</p>
  }
  @case ('editor') {
    <p>Content Editor Panel</p>
  }
  @default {
    <p>Standard Viewer Panel</p>
  }
}
```

---

## Level 5: Multi-Component Architecture

Real apps are never built in one single file. You break your user interface into smaller, reusable components.

Let's say we want to create a `UserCardComponent` that receives user data from `AppComponent`.

### 1. Creating the Child Component (`src/app/user-card/user-card.ts`)

```ts
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <div class="card">
      <h4>{{ name() }}</h4>
      <p>Role: {{ role() }}</p>
      <button (click)="notifyParent()">Promote User</button>
    </div>
  `,
  styles: [`
    .card { border: 1px solid #ccc; padding: 16px; border-radius: 8px; margin: 8px 0; }
  `]
})
export class UserCardComponent {
  // 1. Inputs (Data from Parent) using modern input signals:
  name = input.required<string>();
  role = input<string>('Staff'); // default value

  // 2. Output (Events sent to Parent):
  promote = output<string>();

  notifyParent() {
    this.promote.emit(this.name());
  }
}
```

### 2. Using the Child Component in the Parent (`app.ts` & `app.html`)

**Parent TypeScript (`app.ts`):**
```ts
import { Component } from '@angular/core';
import { UserCardComponent } from './user-card/user-card';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [UserCardComponent], // <-- Import the child component here!
  templateUrl: './app.html'
})
export class AppComponent {
  team = [
    { id: 1, name: 'Maya', role: 'Team Lead' },
    { id: 2, name: 'Kiran', role: 'UI Engineer' }
  ];

  handlePromotion(name: string) {
    alert(`${name} has been promoted!`);
  }
}
```

**Parent HTML (`app.html`):**
```html
<h2>Our Team</h2>

@for (member of team; track member.id) {
  <app-user-card 
    [name]="member.name" 
    [role]="member.role"
    (promote)="handlePromotion($event)">
  </app-user-card>
}
```

---

## Level 6: Services & Dependency Injection

Components should focus on the **view (UI)**. Reusable business logic, data storage, and external API calls belong in a **Service**.

### 1. Creating a Service (`src/app/services/task.service.ts`)
```ts
import { Injectable, signal } from '@angular/core';

export interface Task {
  id: number;
  text: string;
  completed: boolean;
}

@Injectable({
  providedIn: 'root' // Available application-wide as a single shared instance (Singleton)
})
export class TaskService {
  private tasksSignal = signal<Task[]>([
    { id: 1, text: 'Learn Angular components', completed: true },
    { id: 2, text: 'Master signals & state', completed: false }
  ]);

  // Expose read-only view of tasks
  tasks = this.tasksSignal.asReadonly();

  addTask(text: string) {
    const newTask: Task = {
      id: Date.now(),
      text,
      completed: false
    };
    this.tasksSignal.update(tasks => [...tasks, newTask]);
  }

  toggleTask(id: number) {
    this.tasksSignal.update(tasks =>
      tasks.map(t => (t.id === id ? { ...t, completed: !t.completed } : t))
    );
  }

  deleteTask(id: number) {
    this.tasksSignal.update(tasks => tasks.filter(t => t.id !== id));
  }
}
```

### 2. Consuming the Service in a Component with `inject()`
Modern Angular uses the `inject()` function:

```ts
import { Component, inject, signal } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { TaskService } from './services/task.service';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [FormsModule],
  template: `
    <h2>Task Manager</h2>

    <div>
      <input [(ngModel)]="newText" placeholder="Enter task..." />
      <button (click)="submitTask()">Add Task</button>
    </div>

    <ul>
      @for (task of taskService.tasks(); track task.id) {
        <li>
          <span [style.textDecoration]="task.completed ? 'line-through' : 'none'">
            {{ task.text }}
          </span>
          <button (click)="taskService.toggleTask(task.id)">
            {{ task.completed ? 'Undo' : 'Done' }}
          </button>
          <button (click)="taskService.deleteTask(task.id)">Delete</button>
        </li>
      } @empty {
        <p>No tasks yet!</p>
      }
    </ul>
  `
})
export class AppComponent {
  taskService = inject(TaskService); // Inject the service
  newText = '';

  submitTask() {
    if (this.newText.trim()) {
      this.taskService.addTask(this.newText.trim());
      this.newText = '';
    }
  }
}
```

---

## Level 7: Forms in Angular (Reactive Forms)

For professional applications, **Reactive Forms** give you full control over validation, error messaging, and form state.

### 1. Setup Reactive Forms
Add `ReactiveFormsModule` to component imports:

```ts
import { Component } from '@angular/core';
import { ReactiveFormsModule, FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './app.html'
})
export class AppComponent {
  // Define form group and form controls with validators
  userForm = new FormGroup({
    fullName: new FormControl('', [Validators.required, Validators.minLength(3)]),
    email: new FormControl('', [Validators.required, Validators.email])
  });

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form data:', this.userForm.value);
    } else {
      this.userForm.markAllAsTouched(); // Trigger error messages
    }
  }
}
```

### 2. Form Template with Error Validation (`app.html`)
```html
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <div>
    <label>Full Name:</label>
    <input formControlName="fullName" placeholder="Your name" />
    @if (userForm.get('fullName')?.touched && userForm.get('fullName')?.invalid) {
      <small style="color: red;">Name must be at least 3 characters.</small>
    }
  </div>

  <div>
    <label>Email:</label>
    <input formControlName="email" type="email" placeholder="you@domain.com" />
    @if (userForm.get('email')?.touched && userForm.get('email')?.invalid) {
      <small style="color: red;">Please provide a valid email.</small>
    }
  </div>

  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

---

## Level 8: Routing & Navigation

Routing lets you switch views without refreshing the page (Single Page Application).

### 1. Enable Routing in `app.config.ts`
```ts
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes)]
};
```

### 2. Configure Routes (`app.routes.ts`)
```ts
import { Routes } from '@angular/router';
import { HomeComponent } from './pages/home/home';
import { AboutComponent } from './pages/about/about';
import { ProductDetailComponent } from './pages/product-detail/product-detail';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'product/:id', component: ProductDetailComponent },
  { path: '**', redirectTo: '' } // fallback route
];
```

### 3. Display Routes in Template (`app.html`)
```html
<nav>
  <a routerLink="/">Home</a> |
  <a routerLink="/about">About</a>
</nav>

<!-- This is where the routed component appears -->
<router-outlet></router-outlet>
```

---

## Level 9: HTTP & Connecting to a REST API

To communicate with backend APIs (fetch data, save records):

### 1. Provide HTTP Client in `app.config.ts`
```ts
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient } from '@angular/common/http';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient() // <-- Enable HTTP client
  ]
};
```

### 2. Fetch Data from an API in a Service
```ts
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';

export interface Post {
  id: number;
  title: string;
  body: string;
}

@Injectable({
  providedIn: 'root'
})
export class BlogService {
  private http = inject(HttpClient);
  posts = signal<Post[]>([]);
  isLoading = signal<boolean>(false);

  fetchPosts() {
    this.isLoading.set(true);
    this.http.get<Post[]>('https://jsonplaceholder.typicode.com/posts')
      .subscribe({
        next: (data) => {
          this.posts.set(data.slice(0, 5)); // Take first 5 posts
          this.isLoading.set(false);
        },
        error: (err) => {
          console.error('Fetch error:', err);
          this.isLoading.set(false);
        }
      });
  }
}
```

---

## 🛠️ Step-by-Step Practical Project: "Item Catalog & Tracker"

To put everything into practice, you can build a clean **Item Catalog & Tracker** inside this project:

| Milestone | Concepts Covered |
|---|---|
| **Step 1** | Render a static list of items in `app.html` using `@for`. |
| **Step 2** | Add a search bar with `[(ngModel)]` and filter items reactively using `computed()`. |
| **Step 3** | Convert each item card into a reusable `ItemCardComponent` using `input()` and `output()`. |
| **Step 4** | Move state management to an `ItemService` with `signal()` so data is cleanly isolated. |
| **Step 5** | Create an "Add Item" form with `ReactiveFormsModule` that validates inputs (name required, price > 0). |
| **Step 6** | Connect the service to an external mock API using `HttpClient`. |

---

## Quick Reference Summary

| Concept | Syntax / Tool | Example / Purpose |
|---|---|---|
| **Interpolation** | `{{ value }}` | Show dynamic text |
| **Property Binding** | `[attribute]="variable"` | Bind HTML attributes |
| **Event Binding** | `(click)="method()"` | Handle user clicks/events |
| **Two-Way Binding** | `[(ngModel)]="variable"` | Sync input with variable |
| **Reactive State** | `count = signal(0)` | Reactive data container |
| **Derived State** | `double = computed(() => count() * 2)` | Read-only reactive calculation |
| **If Condition** | `@if (condition) { } @else { }` | Conditional template display |
| **Loops** | `@for (item of list(); track item.id) { }` | Loop through arrays |
| **Component Input** | `data = input.required<string>()` | Receive data from parent |
| **Component Output** | `changed = output<string>()` | Send events to parent |
| **Service Injection** | `private service = inject(MyService)` | Access shared services |
