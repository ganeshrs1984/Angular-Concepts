# 🔷 BehaviorSubject + Observable Pattern (Before combineLatest)

Before understanding `combineLatest`, you should understand the **BehaviorSubject + Observable design pattern** in Angular.

---

## 🧠 Basic Design Pattern

```text
API / Events
     ↓
BehaviorSubject (holds latest value)
     ↓
Observable (read-only stream exposed to components)
     ↓
Components subscribe and react
```

---

## 📦 Example

```typescript
private searchResultsSubject = new BehaviorSubject<any[]>([]);
searchResults$ = this.searchResultsSubject.asObservable();

private categoryResultsSubject = new BehaviorSubject<any[]>([]);
categoryResults$ = this.categoryResultsSubject.asObservable();
```

👉 Services push data using `.next()`  
👉 Components only subscribe using `Observable`

---

# 🔷 What is `combineLatest`?

## 🧠 Definition

> `combineLatest` is an RxJS operator that combines multiple Observables and emits a new value whenever any one of them changes, using the latest values from all streams.

---

## 📦 Example

```typescript
combineLatest([
  this.searchResults$,
  this.categoryResults$
]).subscribe(([searchResults, categoryResults]) => {

  const combined = {
    search: searchResults,
    category: categoryResults
  };

});
```

---

# 🔥 What problem does `combineLatest` solve?

## ❌ Without combineLatest

- You need multiple subscriptions
- Manual synchronization of values
- Risk of stale or mismatched data
- Repetitive code

```text
Search stream  → independent handling
Category stream → independent handling
```

---

## ❌ Problems

- Data inconsistency
- Multiple subscriptions
- Hard to maintain shared state
- Complex coordination between streams

---

## ✅ With combineLatest

- Automatically synchronizes multiple streams
- Always provides latest values from all sources
- Single subscription
- Clean and reactive design

---

# 🚀 Key Benefit

> It helps you build a **single combined state object from multiple async data sources**

---

# 🧠 Final Flow

```text
searchResults$     ───A────B────C──
categoryResults$   ──X────Y────Z──

combineLatest emits:

(A,X)
(B,X)
(B,Y)
(C,Y)
(C,Z)
```

---

# 🎯 Summary

- BehaviorSubject → stores and emits state
- Observable → exposes read-only stream
- combineLatest → merges multiple Observables into one combined state

---

# 💡 One-line Memory Trick

> combineLatest = “Always give me the latest values from all streams whenever any one changes”
