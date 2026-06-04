# Communication Between Micro Frontends in Angular NX Module Federation

## Introduction

One of the most common questions in Micro Frontend Architecture is:

> How does one Micro Frontend communicate with another Micro Frontend?

Example:

```text
Shell
├── Customer Remote
├── Admin Remote
└── Reports Remote
```

Suppose:

```text
Customer Remote
```

updates customer information.

How can:

```text
Reports Remote
```

know about that change?

This is called **Micro Frontend Communication**.

---

# Why Communication is Needed

Consider a real-world application:

```text
Shell
├── Authentication
├── Customer
├── Orders
├── Reports
└── Notifications
```

When a user logs in:

```text
Authentication Module
```

needs to notify:

```text
Customer
Orders
Reports
Notifications
```

about the authenticated user.

Without communication, each application becomes isolated.

---

# Communication Approaches

There are several ways Micro Frontends communicate.

```text
1. Shared Angular Services (Recommended)
2. Shared State Management
3. Browser Custom Events
4. URL / Route Parameters
5. Local Storage / Session Storage
6. Backend Communication
```

---

# Approach 1: Shared Angular Services (Recommended)

This is the most common enterprise approach.

---

## Architecture

```text
Shell
Customer
Admin
Reports
       |
       v

Shared AuthService
```

All applications consume the same service instance.

---

## Shared Library

```text
libs/
|
└── shared-auth
    |
    └── auth.service.ts
```

---

## AuthService Example

```typescript
@Injectable(
{
  providedIn: 'root'
})
export class AuthService
{
  private currentUser = signal<string | null>(
    null
  );

  setUser(
    userName: string
  ): void
  {
    this.currentUser.set(
      userName
    );
  }

  getUser(): string | null
  {
    return this.currentUser();
  }
}
```

---

## Share Through Module Federation

```typescript
shared:
{
  '@my-org/shared-auth':
  {
    singleton: true
  }
}
```

---

## Flow

### Step 1

Customer Remote:

```typescript
this.authService.setUser(
  'Balu'
);
```

---

### Step 2

Reports Remote:

```typescript
const user =
  this.authService.getUser();
```

Receives:

```text
Balu
```

---

# Approach 2: Shared State Management

Used in larger applications.

Examples:

```text
NgRx
Signal Store
Akita
NGXS
```

---

## Architecture

```text
Shell
Customer
Admin
Reports
       |
       v

Shared Store
```

---

## Example State

```typescript
export interface AppState
{
  currentUser: string;
  isLoggedIn: boolean;
}
```

---

## Update State

Customer Remote:

```typescript
store.dispatch(
  loginSuccess(
  {
    userName: 'Balu'
  })
);
```

---

## Read State

Reports Remote:

```typescript
store.select(
  selectCurrentUser
);
```

---

## Best For

```text
Authentication
User Profile
Notifications
Application Settings
```

---

# Approach 3: Browser Custom Events

Useful when applications should remain loosely coupled.

---

## Publish Event

Customer Remote:

```typescript
window.dispatchEvent(
  new CustomEvent(
    'customer-created',
    {
      detail:
      {
        customerId: 101
      }
    }
  )
);
```

---

## Listen for Event

Reports Remote:

```typescript
window.addEventListener(
  'customer-created',
  (event: any) =>
  {
    console.log(
      event.detail.customerId
    );
  }
);
```

---

## Flow

```text
Customer Remote
       |
       v

Custom Event
       |
       v

Reports Remote
```

---

## Benefits

```text
No Direct Dependency
Simple
Flexible
```

---

## Drawbacks

```text
Harder to Debug
No Type Safety
Event Naming Challenges
```

---

# Approach 4: Route Parameters

Sometimes communication can happen through navigation.

---

## Navigate

Customer Remote:

```typescript
this.router.navigate(
[
  '/reports',
  customerId
]);
```

---

## Read Parameter

Reports Remote:

```typescript
const customerId =
  this.route.snapshot.params[
    'customerId'
  ];
```

---

## Example URL

```text
/reports/101
```

---

## Best For

```text
Navigation Context
Record Details
Search Criteria
```

---

# Approach 5: Local Storage

Simple but not ideal for large-scale communication.

---

## Save Data

Customer Remote:

```typescript
localStorage.setItem(
  'customerId',
  '101'
);
```

---

## Read Data

Reports Remote:

```typescript
const customerId =
  localStorage.getItem(
    'customerId'
  );
```

---

## Benefits

```text
Simple
Persistent
```

---

## Drawbacks

```text
Not Reactive
Manual Synchronization
Security Concerns
```

---

# Approach 6: Backend Communication

Many enterprises avoid frontend-to-frontend communication.

Instead:

```text
Customer Remote
       |
       v

Backend API
       |
       v

Reports Remote
```

---

## Example

Customer Remote:

```text
POST /api/customers
```

---

Reports Remote:

```text
GET /api/customers
```

---

Both applications communicate through the backend.

---

## Benefits

```text
Loose Coupling
Highly Scalable
Reliable
```

---

# Enterprise Recommendation

Most enterprise Angular NX projects use:

```text
Shared Services
+
Shared State
+
Backend APIs
```

---

# Example Enterprise Architecture

```text
Shell
|
├── AuthService
├── NotificationService
└── Global State

Customer Remote
|
├── Customer Features
└── Customer APIs

Admin Remote
|
├── User Features
└── Admin APIs

Reports Remote
|
├── Reporting Features
└── Reporting APIs
```

Communication occurs through:

```text
Shared AuthService
Shared NotificationService
Shared State Store
Backend APIs
```

---

# Communication Patterns Comparison

| Approach         | Recommended | Enterprise Usage |
| ---------------- | ----------- | ---------------- |
| Shared Services  | ✅ Yes       | Very Common      |
| Shared State     | ✅ Yes       | Very Common      |
| Custom Events    | ⚠ Sometimes | Moderate         |
| Route Parameters | ⚠ Limited   | Common           |
| Local Storage    | ❌ Avoid     | Rare             |
| Backend APIs     | ✅ Yes       | Very Common      |

---

# Recommended Strategy

## Authentication

```text
Shared AuthService
```

---

## Notifications

```text
Shared NotificationService
```

---

## Global User Information

```text
NgRx / Signal Store
```

---

## Business Data

```text
Backend APIs
```

---

# Common Mistake

Directly calling another Remote.

Example:

```text
Customer Remote
      |
      v

Admin Remote
```

This creates:

```text
Tight Coupling
Deployment Problems
Maintenance Challenges
```

Avoid this pattern.

---

# Interview Question

## How do Micro Frontends communicate with each other?

### Answer

Micro Frontends typically communicate using shared services, shared state management, browser events, route parameters, or backend APIs. In Angular NX Module Federation, the most common enterprise approach is to use shared libraries with singleton services for application-wide concerns and backend APIs for business data communication.

---

# Quick Summary

```text
Authentication
    |
    v

Shared AuthService

---------------------

Notifications
    |
    v

Shared NotificationService

---------------------

Global State
    |
    v

NgRx / Signal Store

---------------------

Business Data
    |
    v

Backend APIs
```

---

# Rule to Remember

For Angular NX Micro Frontends:

```text
Shared Services
        +
Shared State
        +
Backend APIs
        =
Recommended Enterprise Communication Strategy
```

Avoid direct communication between Remotes whenever possible.
