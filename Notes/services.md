### **📌 Structure of an Angular Service & Its Parts**  

An Angular service is typically a **TypeScript class** that contains logic to perform specific tasks, such as fetching data, handling business logic, or sharing data between components. It follows a modular structure to promote reusability.

---

## **🔹 1. Basic Structure of a Service**
Every Angular service consists of:
1. **`@Injectable()` Decorator** → Marks the class as a service that can be injected.
2. **Service Class** → A TypeScript class that contains the logic.
3. **Methods** → Functions that perform actions (fetch data, update state, etc.).
4. **Dependency Injection** → Injecting required dependencies such as `HttpClient`.

---

## **🔹 2. Parts of a Service**
Here’s a breakdown of an Angular service with each part explained:

### **📌 `my-service.service.ts`**
```ts
import { Injectable } from '@angular/core';  // 1️⃣ Import Injectable
import { HttpClient } from '@angular/common/http';  // 2️⃣ Import HttpClient for API calls
import { Observable } from 'rxjs';  // 3️⃣ Import Observable for handling async data

@Injectable({
  providedIn: 'root'  // 4️⃣ Service is provided at the root level
})
export class MyService {
  private apiUrl = 'https://api.example.com/data'; // 5️⃣ API URL

  constructor(private http: HttpClient) {} // 6️⃣ Inject HttpClient

  getData(): Observable<any> {  
    return this.http.get<any>(this.apiUrl); // 7️⃣ API call method
  }
}
```

---

### **🔹 Explanation of Each Part**
| Part | Description |
|------|------------|
| **1️⃣ `@Injectable()`** | Makes the class an Angular service so it can be injected. |
| **2️⃣ Import Dependencies** | `HttpClient` is used for HTTP requests. |
| **3️⃣ Import Observables** | `Observable` is used for asynchronous data handling. |
| **4️⃣ `providedIn: 'root'`** | Registers the service at the **root level** (available throughout the app). |
| **5️⃣ Private Variables** | Used to store API URLs or shared data. |
| **6️⃣ Constructor (Dependency Injection)** | Injects dependencies like `HttpClient`. |
| **7️⃣ Methods (Business Logic)** | Defines functions to fetch data, update state, or handle logic. |

---

### **📌 Alternative: Providing Service in a Specific Module**
Instead of `providedIn: 'root'`, you can provide a service inside a specific module:
1. Remove `providedIn: 'root'` from `@Injectable()`.
2. Add the service to the `providers` array in the module.

#### **`app.module.ts`**
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { MyService } from './my-service.service';  // ✅ Import Service

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [MyService],  // ✅ Provide service here
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

### **🔹 Types of Methods in a Service**
1. **Data Fetching Methods** → Fetch API data using `HttpClient`.
2. **State Management Methods** → Store and share data across components.
3. **Business Logic Methods** → Perform calculations or process user input.

#### **Example: Service with Multiple Methods**
```ts
@Injectable({
  providedIn: 'root'
})
export class UserService {
  private users = [
    { id: 1, name: 'Ali' },
    { id: 2, name: 'Ayesha' }
  ];

  constructor() {}

  getUsers() {  
    return this.users;  
  }

  addUser(user: any) {
    this.users.push(user);
  }
}
```

---

### **📌 Injecting & Using a Service in a Component**
After creating a service, you need to **inject and use it** inside a component.

#### **📝 `app.component.ts`**
```ts
import { Component } from '@angular/core';
import { UserService } from './user.service';  // ✅ Import Service

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  users: any[] = [];

  constructor(private userService: UserService) {}  // ✅ Inject Service

  ngOnInit() {
    this.users = this.userService.getUsers();  // ✅ Call service method
  }
}
```

#### **📝 `app.component.html`**
```html
<h2>User List</h2>
<ul>
  <li *ngFor="let user of users">
    {{ user.name }}
  </li>
</ul>
```

---

## **🔄 Summary of Angular Services**
| Feature | Description |
|---------|------------|
| **Definition** | Services are reusable classes that contain business logic or data handling. |
| **Decorator** | `@Injectable({ providedIn: 'root' })` makes it injectable. |
| **Methods** | Services contain methods like fetching data, modifying state, etc. |
| **Dependency Injection** | Inject the service into components using `constructor(private service: ServiceName) {}` |
| **State Management** | Services can store and share data between multiple components. |
| **Providing in Module** | If not provided at root, register in `providers` of `app.module.ts`. |
