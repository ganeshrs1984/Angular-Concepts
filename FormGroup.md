# 🔷 Angular Reactive Forms - How FormGroup Works

---

## 🔄 Data Flow in Reactive Forms

```
User Input
    ↓
FormControl changes
    ↓
FormGroup updates state
    ↓
valueChanges (Observable emits new value)
    ↓
Subscribers react
```

---

## 🧠 Key Concept

`FormGroup` is a **state container** that holds the form’s data.

When anything changes inside it, Angular provides reactive streams like:

- `valueChanges`
- `statusChanges`

These are **Observables** that emit updates whenever the internal state changes.

---

## 🎯 Simple Analogy

- FormGroup → “database row (state)”
- valueChanges → “change listener on that row”
- Observable → “event stream”

---

## 🧩 Detailed Mapping

| Angular Concept | Meaning |
|----------------|--------|
| FormGroup | Holds the complete form state |
| FormControl | Individual field inside the form |
| valueChanges | Emits when form value changes |
| Observable | Stream of events over time |

---

## 💡 How It Works (Simple Explanation)

- User types in an input field
- FormControl captures the change
- FormGroup updates its internal state
- `valueChanges` Observable emits the new value
- Any subscribed code reacts immediately

---

## 🎯 One-Line Memory Trick

> ⚡ **FormGroup does NOT emit — its `valueChanges` Observable emits when FormGroup changes.**

---

## 🚀 Summary

- FormGroup = holds form state
- FormControl = individual field
- valueChanges = reactive stream of updates
- Everything is event-driven using Observables
