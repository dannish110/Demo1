Of course!  
Here’s the **full clean code** you are working with:

---

# 📄 `app.component.html`
```html
<nav class="navbar">
  <div class="logo">
    <a routerLink="/">Smart Event Management</a>
  </div>

  <div class="nav-links">
    <a routerLink="/courses">Courses</a>
    <a *ngIf="isAdmin()" routerLink="/add-course">Add Course</a>

    <a routerLink="/lectures">Lectures</a>
    <a *ngIf="isAdmin() || isTeacher()" routerLink="/add-lecture">Add Lecture</a>

    <a routerLink="/profile">Profile</a>
    <a (click)="logout()">Logout</a>
  </div>
</nav>

<router-outlet></router-outlet>
```

---

# 📄 `app.component.ts`
```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  constructor(private router: Router) {}

  isAdmin(): boolean {
    return localStorage.getItem('role') === 'admin';
  }

  isTeacher(): boolean {
    return localStorage.getItem('role') === 'teacher';
  }

  logout(): void {
    localStorage.clear();
    this.router.navigate(['/login']);
  }
}
```

---

# 📄 `app.component.css`
```css
.navbar {
  background-color: #CAF0F8;
  padding: 1rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo a {
  font-size: 1.5rem;
  font-weight: bold;
  color: #03045E;
  text-decoration: none;
}

.nav-links {
  display: flex;
  gap: 1.5rem;
}

.nav-links a {
  text-decoration: none;
  color: #0077B6;
  font-weight: 500;
  transition: color 0.3s;
}

.nav-links a:hover {
  color: #00B4D8;
}
```

---

# ✅ What this code does:

- **Everyone** can see: Courses, Lectures, Profile, Logout.
- **Only Admin** can see: Add Course.
- **Admin and Teacher** can see: Add Lecture.
- **Logout** clears everything and moves back to login page.

---
# 📌 Remember
- We are using `localStorage.getItem('role')` to **know who is logged in**.
- After logging in, you must have stored `role` in localStorage.

Example in Login:
```typescript
localStorage.setItem('role', response.role);
```

---

Would you also like me to give you the **Login Component (`login.component.ts`)** how it should look properly with `localStorage`? 🚀 (If yes, just say!)

Great catch! Those methods (`isAdmin()`, `isTeacher()`, and `logout()`) need to be defined in your **`app.component.ts`**. Here's the complete example for that file:

---

### **`app.component.ts`**

```ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'e-learning';

  constructor(private router: Router) {}

  getRole(): string | null {
    return localStorage.getItem('role'); // assuming you store role in localStorage
  }

  isAdmin(): boolean {
    return this.getRole() === 'admin';
  }

  isTeacher(): boolean {
    return this.getRole() === 'teacher';
  }

  logout(): void {
    localStorage.clear();
    this.router.navigate(['/login']);
  }
}
```

---

This assumes that you store the user's `role` in `localStorage` after login. For example:

```ts
localStorage.setItem('role', response.role);
```

Let me know if you're storing it differently (e.g., in a service or JWT), and I’ll adjust it accordingly.
