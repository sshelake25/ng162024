

### 2. Create a Caching Interceptor:

Create an `http-cache.interceptor.ts` file:

```bash
ng generate service http-cache
```

Update the `http-cache.service.ts` file:

```typescript
// src/app/http-cache.service.ts
import { Injectable } from '@angular/core';
import { HttpRequest, HttpResponse } from '@angular/common/http';

@Injectable({
  providedIn: 'root',
})
export class HttpCacheService {
  private cache = new Map<string, HttpResponse<any>>();

  get(request: HttpRequest<any>): HttpResponse<any> | null {
    const url = request.urlWithParams;
    return this.cache.has(url) ? this.cache.get(url)! : null;
  }

  put(request: HttpRequest<any>, response: HttpResponse<any>): void {
    const url = request.urlWithParams;
    this.cache.set(url, response);
  }
}
```

Update the `http-cache.interceptor.ts` file:

```typescript
// src/app/http-cache.interceptor.ts
import { Injectable } from '@angular/core';
import {
  HttpEvent,
  HttpRequest,
  HttpResponse,
  HttpHandler,
  HttpInterceptor,
  HttpHeaders,
} from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { tap } from 'rxjs/operators';
import { HttpCacheService } from './http-cache.service';

@Injectable()
export class HttpCacheInterceptor implements HttpInterceptor {
  constructor(private cache: HttpCacheService) {}

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (request.method !== 'GET') {
      // Skip non-GET requests
      return next.handle(request);
    }

    const cachedResponse = this.cache.get(request);
    if (cachedResponse) {
      // Return cached response if available
      console.log('Cache hit:', request.url);
      return of(cachedResponse);
    }

    // Continue with the request and cache the response if successful
    return next.handle(request).pipe(
      tap((event) => {
        if (event instanceof HttpResponse) {
          this.cache.put(request, event);
        }
      })
    );
  }
}
```

### 3. Provide the Interceptor in the App Module:

Update the `app.module.ts` file:

```typescript
// src/app/app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { HttpCacheInterceptor } from './http-cache.interceptor';
import { HttpCacheService } from './http-cache.service';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, AppRoutingModule],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: HttpCacheInterceptor,
      multi: true,
    },
    HttpCacheService,
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### 4. Create a Data Service:

Create a `data.service.ts` file:

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
  private apiUrl = 'https://jsonplaceholder.typicode.com/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }
}
```

### 5. Update App Component:

Update the `app.component.ts` file:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <h1>Angular HTTP Cache Interceptor Example</h1>
    <button (click)="getUsers()">Get Users</button>
    <div *ngIf="users">{{ users | json }}</div>
  `,
})
export class AppComponent {
  users: any[] | undefined;

  constructor(private dataService: DataService) {}

  getUsers(): void {
    this.dataService.getUsers().subscribe((result) => {
      this.users = result;
    });
  }
}
```

### 6. Run the Application:

```bash
ng serve
```

Visit `http://localhost:4200/` in your browser. Click the "Get Users" button, and you should see data fetched from the JSONPlaceholder API. Subsequent requests for the same data will be served from the cache.

