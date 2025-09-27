# General Programming

This section covers general programming concepts, best practices, and common interview questions that are not specific to a single language or technology.

## Q: What is the difference between a shallow copy and a deep copy?

**A:**

- **Shallow Copy:**

  - Creates a new object, but does not create copies of nested objects; instead, it copies references to them.
  - Changes to nested objects in the copy will affect the original (and vice versa).
  - Example (Python): `copy.copy(obj)`
  - Example (C#): MemberwiseClone (for reference types)

- **Deep Copy:**
  - Creates a new object and recursively copies all nested objects, so the copy is fully independent.
  - Changes to nested objects in the copy do not affect the original.
  - Example (Python): `copy.deepcopy(obj)`
  - Example (C#): Manual deep cloning or using serialization

**When to use:**

- Use shallow copy for simple objects or when you want shared references.
- Use deep copy when you need a completely independent clone of an object, including all nested data.

---

## Q: To what level of nesting does a deep copy operate?

**A:**

A deep copy operates recursively, copying all levels of nested objects and data structures. This means every object, sub-object, list, dictionary, or other container within the original object is also copied, no matter how deeply nested. The result is a fully independent clone, where changes to any part of the copy (at any depth) do not affect the original, and vice versa.

**Note:**

- In most languages, deep copy implementations will traverse and copy all reachable objects, but may have limitations with objects that reference themselves (circular references) or with certain types (e.g., file handles, threads).
