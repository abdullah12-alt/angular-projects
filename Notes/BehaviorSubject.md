## **📌 BehaviorSubject in RxJS (Angular)**  

### **🔹 What is BehaviorSubject?**
A `BehaviorSubject` is a special type of **Observable** that:  
✅ Stores the **latest emitted value**.  
✅ **Requires an initial value**.  
✅ **New subscribers instantly receive the last emitted value**.  
✅ Supports **multiple subscribers**.  

---

### **🔹 How `BehaviorSubject` Works Internally?**
1️⃣ Stores the last emitted value.  
2️⃣ If a new subscriber joins, it **immediately receives** the last emitted value.  
3️⃣ Works **like a normal Observable** but retains state.  
4️⃣ Can be **manually updated** using `.next(value)`.  

---

### **🔥 Example: Using BehaviorSubject**
```typescript
import { BehaviorSubject } from 'rxjs';

// Create a BehaviorSubject with an initial value
const subject = new BehaviorSubject<number>(0);

// First Subscriber
subject.subscribe(value => console.log(`Subscriber 1: ${value}`));

subject.next(1); // Emits value 1
subject.next(2); // Emits value 2

// Second Subscriber joins later
subject.subscribe(value => console.log(`Subscriber 2: ${value}`));

subject.next(3); // Emits value 3
```

**🔹 Output:**  
```
Subscriber 1: 0  // First subscriber receives initial value
Subscriber 1: 1  // First subscriber gets updated value
Subscriber 1: 2  // First subscriber gets another updated value
Subscriber 2: 2  // New subscriber instantly gets the latest value (2)
Subscriber 1: 3  // Both subscribers get the new emitted value (3)
Subscriber 2: 3
```

---

### **🔹 Key Differences Between `BehaviorSubject` and `Subject`**
| Feature | `Subject` | `BehaviorSubject` |
|---------|------------|----------------|
| **Initial Value** | ❌ No initial value | ✅ Requires an initial value |
| **New Subscriber Behavior** | Gets **only future values** | Gets **latest emitted value** immediately |
| **State Retention** | ❌ No state | ✅ Retains the last emitted value |

---

### **🔹 Use Cases of BehaviorSubject**
✅ **Global State Management (e.g., User Authentication, Themes, Language settings)**  
✅ **Real-time updates (e.g., Dashboard, Notification Count)**  
✅ **Data Sharing between Components (e.g., Shopping Cart)**  

---

### **🔥 Example: Sharing Data with BehaviorSubject in Angular Service**
**1️⃣ Create a Service (`data.service.ts`)**
```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private messageSource = new BehaviorSubject<string>('Default Message');
  currentMessage = this.messageSource.asObservable();

  changeMessage(message: string) {
    this.messageSource.next(message);
  }
}
```

**2️⃣ Use in Component A (Sender)**
```typescript
import { Component } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-sender',
  template: `<button (click)="sendMessage()">Send Message</button>`
})
export class SenderComponent {
  constructor(private dataService: DataService) {}

  sendMessage() {
    this.dataService.changeMessage('Hello from Sender');
  }
}
```

**3️⃣ Use in Component B (Receiver)**
```typescript
import { Component, OnInit } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-receiver',
  template: `<h1>{{message}}</h1>`
})
export class ReceiverComponent implements OnInit {
  message: string = '';

  constructor(private dataService: DataService) {}

  ngOnInit() {
    this.dataService.currentMessage.subscribe(msg => {
      this.message = msg;
    });
  }
}
```

**🎯 How It Works?**  
✔ `SenderComponent` updates the **BehaviorSubject** using `.next()`.  
✔ `ReceiverComponent` instantly gets the updated value because it's subscribed.  

---

## **📌 Summary**
✅ **BehaviorSubject stores the last emitted value** and sends it to new subscribers.  
✅ **Useful for managing shared state** between Angular components.  
✅ **Ensures new subscribers always get the latest value**.  
✅ **Best for reactive programming & real-time data sharing**.  

