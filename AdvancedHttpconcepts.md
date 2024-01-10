
### 2. Create an HTTP Interceptor:

Create an `http.interceptor.ts` file:

```bash
ng generate service http-interceptor
```

Update the `http-interceptor.service.ts` file:

```typescript
// src/app/http-interceptor.service.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent, HttpResponse } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { tap, catchError } from 'rxjs/operators';

@Injectable()
export class HttpInterceptorService implements HttpInterceptor {
  private cache = new Map<string, HttpResponse<any>>();

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Check if the request is a GET request
    if (request.method === 'GET') {
      // Check if the response is already in the cache
      const cachedResponse = this.cache.get(request.url);
      if (cachedResponse) {
        console.log('Cache hit:', request.url);
        return of(cachedResponse.clone());
      }
    }

    // If not in the cache, continue with the request
    return next.handle(request).pipe(
      tap((event) => {
        if (event instanceof HttpResponse && request.method === 'GET') {
          console.log('Caching response:', request.url);
          this.cache.set(request.url, event.clone());
        }
      }),
      catchError((error) => {
        console.error('Error:', error);
        throw error;
      })
    );
  }
}
```

### 3. Provide the Interceptor in the App Module:

Update the `app.module.ts` file to provide the interceptor:

```typescript
// src/app/app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { HttpInterceptorService } from './http-interceptor.service';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, AppRoutingModule],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: HttpInterceptorService,
      multi: true,
    },
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### 4. Update App Component and Service:

#### a. App Component (`app.component.ts`):

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <h1>Angular HTTP Advanced Example</h1>
    <button (click)="getData()">Get Data</button>
    <div *ngIf="data">{{ data | json }}</div>
  `,
})
export class AppComponent {
  data: any | undefined;

  constructor(private dataService: DataService) {}

  getData(): void {
    this.dataService.getData().subscribe((result) => {
      this.data = result;
    });
  }
}
```

#### b. Data Service (`data.service.ts`):

```typescript
// src/app/data.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/todos/1';

  constructor(private http: HttpClient) {}

  getData(): Observable<any> {
    return this.http.get<any>(this.apiUrl);
  }
}
```

### 5. Run the Application:

```bash
ng serve
```

Visit `http://localhost:4200/` in your browser. Clicking the "Get Data" button will fetch data from the API, and subsequent requests will be intercepted and served from the cache.

### 6. Explaining Key Concepts:

#### a. Defining and Providing HTTP Interceptors:

- The `HttpInterceptorService` class implements the `HttpInterceptor` interface.
- The interceptor checks if the request is a GET request and if the response is already in the cache. If so, it returns the cached response; otherwise, it continues with the request.
- The interceptor logs cache hits and misses.

#### b. Intercepting Responses and Using Multiple Interceptors:

- The `HttpInterceptorService` intercepts responses using the `tap` operator and caches GET responses.
- You can have multiple interceptors in an application by providing them in the `HTTP_INTERCEPTORS` token. The `multi: true` configuration allows multiple interceptors.

#### c. Caching HTTP Requests with Interceptors:

- The interceptor caches GET responses using a `Map` to store responses with URLs as keys.
- Cached responses are cloned to prevent side effects.

#### d. Benefits and Types of Caching:

- **Benefits of Caching:**
  - Reduces server load by serving cached responses.
  - Improves application performance by reducing network requests.

- **Types of Caching:**
  - **Memory Cache:** In-memory storage for quick access.
  - **Browser Cache:** Cached data stored by the browser.
  - **HTTP Caching Headers:** Cache control headers (e.g., `Cache-Control`, `Expires`) defined in HTTP responses.

This example demonstrates a basic implementation of an HTTP interceptor for caching in Angular. Depending on your application's needs, you may implement more sophisticated caching strategies, handle cache expiration, and consider other types of caching.

#========================#


### 2. Create Authentication Service:

Create an `auth.service.ts` file:

```bash
ng generate service auth
```

Update the `auth.service.ts` file:

```typescript
// src/app/auth.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/users';
  private token: string | null = null;

  constructor(private http: HttpClient) {}

  login(credentials: { username: string; password: string }): Observable<any> {
    return this.http.post<any>('https://jsonplaceholder.typicode.com/users', credentials);
  }

  setToken(token: string): void {
    this.token = token;
  }

  getToken(): string | null {
    return this.token;
  }

  logout(): void {
    this.token = null;
  }
}
```

### 3. Create HTTP Interceptor:

Create an `http-interceptor.service.ts` file:

```bash
ng generate service http-interceptor
```

Update the `http-interceptor.service.ts` file:

```typescript
// src/app/http-interceptor.service.ts
import { Injectable } from '@angular/core';
import {
  HttpInterceptor,
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpResponse,
  HttpErrorResponse,
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, tap, switchMap } from 'rxjs/operators';
import { AuthService } from './auth.service';

@Injectable()
export class HttpInterceptorService implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = this.authService.getToken();

    if (token) {
      request = this.addToken(request, token);
    }

    return next.handle(request).pipe(
      tap((event) => {
        if (event instanceof HttpResponse) {
          // Handle successful responses (e.g., log or handle refresh token)
        }
      }),
      catchError((error) => {
        if (error instanceof HttpErrorResponse && error.status === 401) {
          // Unauthorized error, attempt to refresh token
          return this.handle401Error(request, next);
        } else {
          // Handle other errors
          return throwError(error);
        }
      })
    );
  }

  private addToken(request: HttpRequest<any>, token: string): HttpRequest<any> {
    return request.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`,
      },
    });
  }

  private handle401Error(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (!this.isRefreshingToken) {
      this.isRefreshingToken = true;
      this.authService.logout(); // Clear the existing token and refresh token

      return this.authService.login({ username: 'refresh', password: 'token' }).pipe(
        switchMap((newToken) => {
          this.isRefreshingToken = false;
          this.authService.setToken(newToken);
          return next.handle(this.addToken(request, newToken));
        }),
        catchError((error) => {
          this.isRefreshingToken = false;
          this.authService.logout();
          return throwError(error);
        })
      );
    } else {
      // Wait for the token refresh and then retry the request
      return this.refreshTokenSubject.pipe(
        switchMap(() => {
          return next.handle(this.addToken(request, this.authService.getToken()!));
        })
      );
    }
  }

  private isRefreshingToken = false;
  private refreshTokenSubject: Observable<any> | undefined;
}
```

### 4. Provide Interceptor in the App Module:

Update the `app.module.ts` file:

```typescript
// src/app/app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { HttpInterceptorService } from './http-interceptor.service';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { AuthService } from './auth.service';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, AppRoutingModule],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: HttpInterceptorService,
      multi: true,
    },
    AuthService,
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

### 5. Update App Component and HTML:

Update the `app.component.ts` file:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { HttpClient }

 from '@angular/common/http';
import { AuthService } from './auth.service';

@Component({
  selector: 'app-root',
  template: `
    <h1>Angular HTTP Interceptor Example</h1>
    <button (click)="login()">Login</button>
    <button (click)="getData()">Get Data</button>
    <button (click)="logout()">Logout</button>
    <div *ngIf="data">{{ data | json }}</div>
  `,
})
export class AppComponent {
  data: any | undefined;

  constructor(private http: HttpClient, private authService: AuthService) {}

  login(): void {
    this.authService.login({ username: 'test', password: 'test' }).subscribe((result) => {
      this.authService.setToken(result.token);
    });
  }

  getData(): void {
    this.http.get('https://jsonplaceholder.typicode.com/todos/1').subscribe((result) => {
      this.data = result;
    });
  }

  logout(): void {
    this.authService.logout();
  }
}
```

### 6. Run the Application:

```bash
ng serve
```

Visit `http://localhost:4200/` in your browser. Click the "Login" button, then the "Get Data" button to demonstrate handling unauthorized requests and token refreshing.

This example illustrates how an HTTP interceptor can handle authentication, token refreshing, and error handling in an Angular application. The `HttpInterceptorService` intercepts HTTP requests and responses, handles 401 errors by refreshing the token, and ensures a seamless user experience. Keep in mind that this is a simplified example for demonstration purposes, and a production implementation may require additional considerations and security measures.
