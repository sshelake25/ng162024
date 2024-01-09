# ng162024



## Services a.	Introduction to services and their role in Angular applications 

In Angular applications, services play a crucial role in promoting modularity, reusability, and maintainability of code. Services are a fundamental part of the Angular framework, providing a way to organize and share code across different components. They facilitate the implementation of business logic, data manipulation, and communication with external APIs.

### What are Angular Services?

Angular services are singleton objects that are instantiated only once per application and can be injected into components, directives, or other services. They act as a central hub for managing data, state, and functionality that needs to be shared or reused throughout the application.

### Key Roles of Services in Angular Applications:

1. **Code Organization:**
   Services help in organizing code by keeping related functionality and data in a centralized and modularized manner. This aids in maintaining a clean and structured codebase, making it easier to understand and maintain.

2. **Reusability:**
   Services promote code reusability by allowing components to use the same service instance across different parts of the application. This avoids code duplication and ensures consistency in data and behavior.

3. **Dependency Injection:**
   Angular's dependency injection system is used to inject services into components, making it easy to manage dependencies and promote loose coupling between components. This allows for more flexible and modular code.

4. **Business Logic:**
   Services are commonly used to encapsulate business logic. They handle data manipulation, perform calculations, and manage state. This separation of concerns helps in creating more maintainable and testable code.

5. **Data Sharing:**
   Services facilitate communication between different components by acting as a centralized point for data sharing. Components can subscribe to or call methods on a service to exchange data and trigger actions.

6. **HTTP Communication:**
   Services are commonly used to handle HTTP requests and communicate with external APIs. This allows for a separation of concerns between data fetching and presentation logic, making the code more modular.

7. **State Management:**
   Services can be used for managing application state. By centralizing state in a service, it becomes easier to coordinate and share state changes across different parts of the application.

### Example:

```typescript
// Sample Service
@Injectable({
  providedIn: 'root',
})
export class DataService {
  private data: string[] = [];

  getData(): string[] {
    return this.data;
  }

  addData(item: string): void {
    this.data.push(item);
  }
}
```

In this example, the `DataService` encapsulates data-related functionality. Components can inject and use this service to share and manipulate data.

## b.	Implementing and injecting services using dependency injection

In Angular, Dependency Injection (DI) is a powerful mechanism that allows you to inject dependencies, such as services, into components, directives, or other services. This promotes modularity, reusability, and maintainability in your application. Here's a step-by-step guide on implementing and injecting services using Angular's Dependency Injection:

### 1. Create a Service:

First, create a service. You can use the Angular CLI to generate a service file. For example:

```bash
ng generate service data
```

This will generate a `data.service.ts` file.

```typescript
// data.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root',
})
export class DataService {
  private data: string[] = [];

  getData(): string[] {
    return this.data;
  }

  addData(item: string): void {
    this.data.push(item);
  }
}
```

### 2. Register the Service:

Ensure that the service is registered in the `providers` array of the `@NgModule` decorator in your `app.module.ts` file. The `providedIn: 'root'` syntax in the `@Injectable` decorator is a shorthand way of registering the service at the root level.

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { DataService } from './data.service';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [DataService], // Register the DataService here
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### 3. Inject the Service:

Now, you can inject the service into a component or another service by adding it as a parameter in the constructor.

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <div>
      <h1>Data from Service:</h1>
      <ul>
        <li *ngFor="let item of dataService.getData()">{{ item }}</li>
      </ul>
    </div>
  `,
})
export class AppComponent {
  constructor(private dataService: DataService) {}
}
```

### 4. Use the Service:

Now, you can use the service methods and properties within your component.

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <div>
      <h1>Data from Service:</h1>
      <ul>
        <li *ngFor="let item of dataService.getData()">{{ item }}</li>
      </ul>
      <button (click)="addItem()">Add Item</button>
    </div>
  `,
})
export class AppComponent {
  constructor(private dataService: DataService) {}

  addItem(): void {
    this.dataService.addData('New Item');
  }
}
```
**Usage:**
### 1. Create a new Angular project:

```bash
ng new angular-service-example
cd angular-service-example
```

### 2. Generate components and service:

```bash
ng generate component data-display
ng generate component data-add
ng generate service data
```

### 3. Update the `data.service.ts` file:

```typescript
// src/app/data.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/todos';
  private data: string[] = [];

  constructor(private http: HttpClient) {}

  getData(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }

  addData(item: string): void {
    this.data.push(item);
  }

  performBusinessLogic(): void {
    console.log('Business logic executed.');
  }
}
```

### 4. Update the `data-display.component.ts` file:

```typescript
// src/app/data-display/data-display.component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-display',
  template: `
    <div>
      <h2>Data Display Component:</h2>
      <ul>
        <li *ngFor="let item of data">{{ item.title }}</li>
      </ul>
    </div>
  `,
})
export class DataDisplayComponent implements OnInit {
  data: any[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.getData().subscribe((result) => {
      this.data = result;
    });
  }
}
```

### 5. Update the `data-add.component.ts` file:

```typescript
// src/app/data-add/data-add.component.ts
import { Component } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-add',
  template: `
    <div>
      <h2>Data Add Component:</h2>
      <button (click)="addItem()">Add Item</button>
      <button (click)="performBusinessLogic()">Perform Business Logic</button>
    </div>
  `,
})
export class DataAddComponent {
  newItem: string = '';

  constructor(private dataService: DataService) {}

  addItem(): void {
    const newItem = { userId: 1, id: 201, title: 'New Item', completed: false };
    this.dataService.addData(newItem);
  }

  performBusinessLogic(): void {
    this.dataService.performBusinessLogic();
  }
}
```

### 6. Update the `app.module.ts` file:

```typescript
// src/app/app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { DataDisplayComponent } from './data-display/data-display.component';
import { DataAddComponent } from './data-add/data-add.component';

@NgModule({
  declarations: [AppComponent, DataDisplayComponent, DataAddComponent],
  imports: [BrowserModule, HttpClientModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### 7. Update the `app.component.ts` file:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <div>
      <app-data-display></app-data-display>
      <app-data-add></app-data-add>
    </div>
  `,
})
export class AppComponent {}
```

