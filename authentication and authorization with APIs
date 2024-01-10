
### 1. Set Up a New Angular Project:

```bash
ng new angular-auth-example
cd angular-auth-example
```

### 2. Install Dependencies:

```bash
ng add @auth0/angular-jwt
```

This package (`@auth0/angular-jwt`) helps with decoding and verifying JSON Web Tokens (JWTs) in Angular applications.

### 3. Create an Authentication Service:

```bash
ng generate service auth
```

Update the `auth.service.ts` file:

```typescript
// src/app/auth.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { catchError, map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  private apiUrl = 'https://api.example.com/auth';

  constructor(private http: HttpClient) {}

  login(credentials: { username: string; password: string }): Observable<string> {
    // Mock authentication API
    return this.http.post<any>(`${this.apiUrl}/login`, credentials).pipe(
      map((response) => response.token),
      catchError((error) => {
        console.error('Authentication error:', error);
        return of(null);
      })
    );
  }

  logout(): void {
    // Implement logout logic (if needed)
  }

  isAuthenticated(): boolean {
    // Check if user is authenticated (e.g., check if token exists)
    const token = localStorage.getItem('token');
    return !!token;
  }

  getAuthToken(): string | null {
    // Get the authentication token from local storage
    return localStorage.getItem('token');
  }
}
```

### 4. Update the Authentication Flow in Components:

#### a. Login Component (`login.component.ts`):

```typescript
// src/app/login/login.component.ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from '../auth.service';

@Component({
  selector: 'app-login',
  template: `
    <h2>Login</h2>
    <button (click)="login()">Login</button>
  `,
})
export class LoginComponent {
  constructor(private router: Router, private authService: AuthService) {}

  login(): void {
    const credentials = { username: 'demo', password: 'password' };
    this.authService.login(credentials).subscribe((token) => {
      if (token) {
        localStorage.setItem('token', token);
        this.router.navigate(['/dashboard']);
      } else {
        // Handle authentication error
      }
    });
  }
}
```

#### b. Dashboard Component (`dashboard.component.ts`):

```typescript
// src/app/dashboard/dashboard.component.ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from '../auth.service';

@Component({
  selector: 'app-dashboard',
  template: `
    <h2>Dashboard</h2>
    <p *ngIf="isAuthenticated()">Welcome to the Dashboard!</p>
    <button (click)="logout()">Logout</button>
  `,
})
export class DashboardComponent {
  constructor(private router: Router, private authService: AuthService) {}

  isAuthenticated(): boolean {
    return this.authService.isAuthenticated();
  }

  logout(): void {
    this.authService.logout();
    localStorage.removeItem('token');
    this.router.navigate(['/login']);
  }
}
```

### 5. Set Up Routing:

Update the `app-routing.module.ts` file to include the routes for the components:

```typescript
// src/app/app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { LoginComponent } from './login/login.component';
import { DashboardComponent } from './dashboard/dashboard.component';

const routes: Routes = [
  { path: 'login', component: LoginComponent },
  { path: 'dashboard', component: DashboardComponent },
  { path: '', redirectTo: '/login', pathMatch: 'full' },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

### 6. Secure Routes with Guards:

Create an `auth.guard.ts` file:

```bash
ng generate guard auth
```

Update the `auth.guard.ts` file:

```typescript
// src/app/auth.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root',
})
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): boolean {
    if (this.authService.isAuthenticated()) {
      return true;
    } else {
      this.router.navigate(['/login']);
      return false;
    }
  }
}
```

### 7. Use the Guard in Routing:

Update the `app-routing.module.ts` file to use the `AuthGuard`:

```typescript
// src/app/app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { LoginComponent } from './login/login.component';
import { DashboardComponent } from './dashboard/dashboard.component';
import { AuthGuard } from './auth.guard';

const routes: Routes = [
  { path: 'login', component: LoginComponent },
  { path: 'dashboard', component: DashboardComponent, canActivate: [AuthGuard] },
  { path: '', redirectTo: '/login', pathMatch: 'full' },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

### 8. Use JWT Interceptor for Token Handling:

Create a `jwt.interceptor.ts` file:

```bash
ng generate service jwt
```

Update the `jwt.interceptor.ts` file:

```typescript
// src/app/jwt.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable } from 'rxjs';
import { AuthService } from './auth.service';

@Injectable()
export class JwtInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = this.authService.getAuthToken();

    if (token) {
      request = request.clone({
        setHeaders: {
          Authorization: `Bearer ${token}`,
        },
      });
    }

    return next.handle(request);
  }
}
```

### 9. Provide the Interceptor in the App Module:

Update the `app.module.ts` file to provide the interceptor:

```typescript
// src/app/app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { FormsModule } from '@angular/forms';
import { JwtInterceptor } from './jwt.interceptor';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './login/login.component';
import { DashboardComponent } from './dashboard/dashboard.component';
import { AuthGuard } from './auth.guard';

@NgModule({
  declarations: [AppComponent, LoginComponent,
