# 🔷 `forkJoin` 

> **Note:** `forkJoin` should **not** be used with `BehaviorSubject` Observables exposed by a data service. `forkJoin` waits for all source Observables to **complete**, but a `BehaviorSubject` is designed to remain active and typically **never completes**, so `forkJoin` will never emit.

---

# 🧠 What is `forkJoin`?

> `forkJoin` is an RxJS operator that waits for **all Observables to complete**, then emits **one combined result**.

It is primarily used for **parallel HTTP API calls**.

---

# 📦 What Problem Does `forkJoin` Solve?

Without `forkJoin`:

- Multiple API calls require multiple subscriptions.
- Difficult to know when **all APIs have finished**.
- More complex and harder-to-maintain code.

With `forkJoin`:

- Executes multiple API calls in parallel.
- Waits until **every API call completes**.
- Returns all responses together in a single subscription.

---

# ❌ Without `forkJoin`

```typescript
this.userService.getUsers().subscribe(users => {
    ...
});

this.orderService.getOrders().subscribe(orders => {
    ...
});

this.productService.getProducts().subscribe(products => {
    ...
});
```

Three independent subscriptions.

---

# ✅ With `forkJoin`

```typescript
forkJoin([
    this.userService.getUsers(),
    this.orderService.getOrders(),
    this.productService.getProducts()
]).subscribe(([users, orders, products]) => {

    console.log(users);
    console.log(orders);
    console.log(products);

});
```

---

# 🧠 Recommended Architecture with BehaviorSubject

Instead of exposing API calls directly to every component, let the service own:

- API call
- BehaviorSubject
- Read-only Observable

---

## UserService

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  // Private state
  private usersSubject = new BehaviorSubject<User[]>([]);

  // Public read-only stream
  users$ = this.usersSubject.asObservable();

  constructor(private http: HttpClient) {}

  // Returns HTTP Observable
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }

  // Updates shared state
  setUsers(users: User[]): void {
    this.usersSubject.next(users);
  }

}
```

---

## Component

```typescript
forkJoin([
    this.userService.getUsers(),
    this.orderService.getOrders()
]).subscribe(([users, orders]) => {

    this.userService.setUsers(users);
    this.orderService.setOrders(orders);

});
```

Now every component subscribed to:

```typescript
this.userService.users$
```

will automatically receive the latest data.

---

# ❌ Do NOT do this

```typescript
forkJoin([
    this.userService.users$,
    this.orderService.orders$
])
```

### Why?

Because:

```text
BehaviorSubject
      │
Never completes
      │
forkJoin waits forever
      │
Nothing is emitted
```

`forkJoin` only works with Observables that **complete**, such as `HttpClient` requests.

---

# 🧠 How Everything Works Together

```text
Component
    │
    ▼
forkJoin
    │
    ├───────────────┐
    ▼               ▼
getUsers()     getOrders()
    │               │
HTTP API       HTTP API
    │               │
Users          Orders
    │               │
    ▼               ▼
setUsers()    setOrders()
    │               │
BehaviorSubject.next()
    │
    ▼
Observable (users$ / orders$)
    │
    ▼
Subscribed Components receive updates
```

---

# 📊 `forkJoin` vs `combineLatest`

| Feature | `forkJoin` | `combineLatest` |
|----------|------------|-----------------|
| Emits | Once | Multiple times |
| Waits for Completion | ✅ Yes | ❌ No |
| Reacts to New Values | ❌ No | ✅ Yes |
| Best For | Parallel HTTP API calls | Combining live Observable streams |
| Works with BehaviorSubject | ❌ No | ✅ Yes |

---

# 💡 Memory Trick

```text
forkJoin
---------
✔ Parallel API calls
✔ Waits for completion
✔ Emits once

combineLatest
--------------
✔ Live streams
✔ Latest values
✔ Emits whenever any source changes
```

---

# 🎯 Interview Answer

> **When do you use `forkJoin`?**

Use `forkJoin` when you need to execute **multiple independent HTTP API calls in parallel** and continue only after **all of them have completed**.

> **Can `forkJoin` be used with `BehaviorSubject`?**

No. A `BehaviorSubject` does not complete by default, so `forkJoin` will wait forever and never emit. Instead, use `forkJoin` with HTTP Observables, then update the `BehaviorSubject` using `.next()` and expose the data through a read-only Observable.
