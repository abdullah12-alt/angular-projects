## **🔐 JWT (JSON Web Token) in Angular**  

### **📌 What is JWT?**
JWT (**JSON Web Token**) is a **compact, self-contained** token used for authentication and authorization. It allows users to securely transfer data between parties using a **signed token**.

---

### **🔹 Structure of a JWT**
A JWT consists of **three parts**, separated by dots (`.`):  
`Header.Payload.Signature`

📌 Example JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJ1c2VySWQiOjEsIm5hbWUiOiJKb2huIERvZSIsImlhdCI6MTYxNjE1MTUyMH0
.
hLKhFlhNFZBrZ5JxNqBKwTzWFn-2wpzNIMlH8FQmQcI
```

| Part | Description | Example |
|------|------------|---------|
| **Header** | Contains metadata about the token (e.g., algorithm, type) | `{"alg": "HS256", "typ": "JWT"}` |
| **Payload** | Contains user data (claims) | `{"userId":1, "name":"John Doe", "iat": 1616151520}` |
| **Signature** | Ensures the token is **not tampered** | `HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)` |

---

## **📌 How JWT Works in Angular?**
1️⃣ **User Logs In** → Sends credentials to the backend.  
2️⃣ **Backend Validates Credentials** → Generates a JWT token.  
3️⃣ **Frontend Stores Token** → Usually in **localStorage** or **sessionStorage**.  
4️⃣ **Token is Sent with API Requests** → Sent via the **Authorization header**.  
5️⃣ **Backend Verifies Token** → Allows or denies access.  

---

## **🚀 Implementing JWT Authentication in Angular**
### **1️⃣ Install Dependencies**
Run the following command to install `@auth0/angular-jwt` for token management:
```sh
npm install @auth0/angular-jwt
```

---

### **2️⃣ Create Auth Service (`auth.service.ts`)**
Manages login, logout, and token storage.

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private apiUrl = 'https://yourapi.com/auth'; // Backend API URL
  private tokenKey = 'jwt_token';
  private authStatus = new BehaviorSubject<boolean>(this.isAuthenticated());

  constructor(private http: HttpClient) {}

  // User login and receive JWT
  login(credentials: { email: string; password: string }): Observable<any> {
    return this.http.post<{ token: string }>(`${this.apiUrl}/login`, credentials).pipe(
      tap(response => {
        localStorage.setItem(this.tokenKey, response.token);
        this.authStatus.next(true);
      })
    );
  }

  // Logout user
  logout() {
    localStorage.removeItem(this.tokenKey);
    this.authStatus.next(false);
  }

  // Get JWT token
  getToken(): string | null {
    return localStorage.getItem(this.tokenKey);
  }

  // Check if user is authenticated
  isAuthenticated(): boolean {
    return !!this.getToken();
  }

  // Observable for auth status
  getAuthStatus(): Observable<boolean> {
    return this.authStatus.asObservable();
  }
}
```

---

### **3️⃣ Attach Token to HTTP Requests (`auth.interceptor.ts`)**
Intercepts outgoing HTTP requests and attaches the JWT token.

```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable } from 'rxjs';
import { AuthService } from './auth.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = this.authService.getToken();
    if (token) {
      const cloned = req.clone({
        setHeaders: {
          Authorization: `Bearer ${token}`
        }
      });
      return next.handle(cloned);
    }
    return next.handle(req);
  }
}
```

---

### **4️⃣ Register the Interceptor (`app.module.ts`)**
```typescript
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthInterceptor } from './auth.interceptor';

@NgModule({
  imports: [HttpClientModule],
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
  ]
})
export class AppModule {}
```

---

### **5️⃣ Protect Routes (`auth.guard.ts`)**
Prevents unauthorized access to certain routes.

```typescript
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root'
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

---

### **6️⃣ Apply Guard to Routes (`app-routing.module.ts`)**
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { DashboardComponent } from './dashboard/dashboard.component';
import { LoginComponent } from './login/login.component';
import { AuthGuard } from './auth.guard';

const routes: Routes = [
  { path: 'dashboard', component: DashboardComponent, canActivate: [AuthGuard] },
  { path: 'login', component: LoginComponent },
  { path: '**', redirectTo: 'login' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

---

## **📌 Where to Store JWT Tokens?**
| **Storage Method** | **Security** | **Use Case** |
|--------------------|-------------|--------------|
| `localStorage` | ❌ **Risky** (Token can be stolen via XSS) | Persistent login |
| `sessionStorage` | ✅ Safer, cleared on tab close | Temporary login |
| `HttpOnly Cookies` | ✅ **Best practice** (protected from XSS) | Secure authentication |

**✅ Best Practice:** Use **HttpOnly Cookies** (handled by backend).

---

## **📌 Refresh Token Strategy**
- **JWT expires quickly** to improve security.  
- Store **refresh token** separately to request a **new JWT**.  
- Backend provides a **new JWT when refresh token is sent**.  

---

## **🔹 Summary**
✅ **JWT is used for secure authentication** in Angular apps.  
✅ **Stored in `localStorage`/`sessionStorage`/Cookies** for API requests.  
✅ **Attach JWT token in `Authorization` header** via an **HTTP interceptor**.  
✅ **Use `AuthGuard` to protect routes** in Angular.  
✅ **Implement refresh token strategy** for better security.  

