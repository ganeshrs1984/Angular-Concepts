# Java Spring Boot vs Angular Singleton

## What is a Singleton?

A **Singleton** means **only one instance of an object** is created within a specific scope.

The important question is:

> **One instance for what?**

The answer is different in Spring Boot and Angular.

---

# Spring Boot Singleton

When you create a service:

```java
@Service
public class UserService {
}
```

Spring creates **one instance** of `UserService` inside the **Spring Application Context**.

```
                Spring Boot Application (JVM)
                       ┌─────────────────┐
Client 1 ─────────────►│                 │
Client 2 ─────────────►│   UserService   │
Client 3 ─────────────►│   (1 Instance)  │
                       │                 │
                       └─────────────────┘
```

### Characteristics

- One instance per Spring application.
- Shared across all users.
- Lives on the server.
- Every HTTP request uses the same service instance.

---

# Angular Singleton

When you create a service:

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
}
```

Angular creates **one instance** of `UserService` for **one Angular application**.

Usually, one Angular application runs inside one browser tab.

```
Browser Tab 1
-----------------------
Angular App
    │
    ├── HomeComponent
    ├── DashboardComponent
    └── ProfileComponent
            │
            ▼
     UserService
     (1 Instance)

Browser Tab 2
-----------------------
Angular App
    │
    ▼
UserService
(Another Instance)
```

### Characteristics

- One instance per Angular application.
- Shared only by components in the same browser tab.
- Lives in the browser.
- Every browser tab has its own singleton.

---

# Example

Suppose the service contains:

```typescript
export class UserService {
    counter = 0;
}
```

### Browser Tab 1

```
HomeComponent
counter = 5

DashboardComponent
counter = 5
```

Both components share the same service instance.

### Browser Tab 2

```
HomeComponent
counter = 0
```

This is a completely different Angular application with its own singleton.

---

# Comparison

| Feature | Spring Boot | Angular |
|---------|-------------|----------|
| Singleton Scope | Entire Spring Application | One Angular Application |
| Runs On | Server | Browser |
| Shared By | All users | Components in one browser tab |
| Number of Instances | One per JVM/Application | One per browser tab/application |
| Lifetime | Until application stops | Until browser tab is closed or refreshed |

---

# Key Takeaways

## Spring Boot

- Singleton = One object for the entire server application.
- Shared across every client and every request.

## Angular

- Singleton = One object for one Angular application.
- Shared only among components within the same browser tab.
- Every browser tab creates its own singleton.

---

# Easy Way to Remember

**Spring Boot**

> One Singleton → Entire Server

**Angular**

> One Singleton → One Browser Tab (Angular Application)
