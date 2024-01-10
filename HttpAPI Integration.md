
### 1. Set Up a New Angular Project:

```bash
ng new angular-crud-example
cd angular-crud-example
```

### 2. Create a Service for API Interaction:

```bash
ng generate service data
```

Update the `data.service.ts` file:

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

  constructor(private http: HttpClient) {}

  getAllData(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }

  getDataById(id: number): Observable<any> {
    const url = `${this.apiUrl}/${id}`;
    return this.http.get<any>(url);
  }

  createData(data: any): Observable<any> {
    return this.http.post<any>(this.apiUrl, data);
  }

  updateData(id: number, data: any): Observable<any> {
    const url = `${this.apiUrl}/${id}`;
    return this.http.put<any>(url, data);
  }

  deleteData(id: number): Observable<any> {
    const url = `${this.apiUrl}/${id}`;
    return this.http.delete<any>(url);
  }
}
```

### 3. Create Components for CRUD Operations:

```bash
ng generate component data-list
ng generate component data-detail
ng generate component data-create
ng generate component data-edit
```

### 4. Implement CRUD Operations in Components:

#### a. Data List Component (`data-list.component.ts`):

```typescript
// src/app/data-list/data-list.component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-list',
  template: `
    <h2>Data List</h2>
    <ul>
      <li *ngFor="let item of data">{{ item.title }}</li>
    </ul>
  `,
})
export class DataListComponent implements OnInit {
  data: any[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.getAllData().subscribe((result) => {
      this.data = result;
    });
  }
}
```

#### b. Data Detail Component (`data-detail.component.ts`):

```typescript
// src/app/data-detail/data-detail.component.ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-detail',
  template: `
    <h2>Data Detail</h2>
    <div *ngIf="data">
      <p>ID: {{ data.id }}</p>
      <p>Title: {{ data.title }}</p>
      <p>Completed: {{ data.completed }}</p>
    </div>
  `,
})
export class DataDetailComponent implements OnInit {
  data: any | undefined;

  constructor(private route: ActivatedRoute, private dataService: DataService) {}

  ngOnInit(): void {
    const id = this.route.snapshot.paramMap.get('id');
    if (id) {
      this.dataService.getDataById(parseInt(id)).subscribe((result) => {
        this.data = result;
      });
    }
  }
}
```

#### c. Data Create Component (`data-create.component.ts`):

```typescript
// src/app/data-create/data-create.component.ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-create',
  template: `
    <h2>Data Create</h2>
    <button (click)="createData()">Create Data</button>
  `,
})
export class DataCreateComponent {
  constructor(private router: Router, private dataService: DataService) {}

  createData(): void {
    const newData = { title: 'New Item', completed: false };
    this.dataService.createData(newData).subscribe(() => {
      this.router.navigate(['/list']);
    });
  }
}
```

#### d. Data Edit Component (`data-edit.component.ts`):

```typescript
// src/app/data-edit/data-edit.component.ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { DataService } from '../data.service';

@Component({
  selector: 'app-data-edit',
  template: `
    <h2>Data Edit</h2>
    <button (click)="updateData()">Update Data</button>
  `,
})
export class DataEditComponent implements OnInit {
  data: any | undefined;

  constructor(private route: ActivatedRoute, private router: Router, private dataService: DataService) {}

  ngOnInit(): void {
    const id = this.route.snapshot.paramMap.get('id');
    if (id) {
      this.dataService.getDataById(parseInt(id)).subscribe((result) => {
        this.data = result;
      });
    }
  }

  updateData(): void {
    if (this.data) {
      this.dataService.updateData(this.data.id, this.data).subscribe(() => {
        this.router.navigate(['/list']);
      });
    }
  }
}
```

### 5. Set Up Routing:

Update the `app-routing.module.ts` file to include the routes for the components:

```typescript
// src/app/app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { DataListComponent } from './data-list/data-list.component';
import { DataDetailComponent } from './data-detail/data-detail.component';
import { DataCreateComponent } from './data-create/data-create.component';
import { DataEditComponent } from './data-edit/data-edit.component';

const routes: Routes = [
  { path: 'list', component: DataListComponent },
  { path: 'detail/:id', component: DataDetailComponent },
  { path: 'create', component: DataCreateComponent },
  { path: 'edit/:id', component: DataEditComponent },
  { path: '', redirectTo: '/list', pathMatch: 'full' },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

### 6. Update the App Component and HTML:

Update the `app.component.ts` file:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <h1>Angular CRUD Example</h1>
    <router-outlet></router-outlet>
  `,
})
export class AppComponent {}
```

### 7. Run the Application:

```bash
ng serve
```

Visit `http://localhost:4200/` in your browser. You should be able to navigate between the list, detail, create, and edit components, performing CRUD operations with the mocked API. Please note that JSONPlaceholder is used here for demonstration purposes, and changes made are not persistent.

This example provides a basic setup for implementing CRUD operations with Angular and RESTful APIs. Depending on your use case and requirements, you may need
