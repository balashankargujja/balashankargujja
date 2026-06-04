# Sharing Angular Services in NX Module Federation

## Introduction

One of the most important topics in Module Federation is sharing Angular services.

A common question is:

> If the Shell application and Remote applications both use the same service, will there be one instance or multiple instances?

The answer depends on how the service is configured and shared.

---

# Why Service Sharing Matters

Suppose your application has:

```text
Shell
├── Customer Remote
├── Admin Remote
└── Reports Remote
```

And all applications use:

```text
AuthService
```

to manage:

```text
User Login
User Information
JWT Token
Permissions
```

If services are not shared properly:

```text
Shell      -> AuthService Instance #1
Customer   -> AuthService Instance #2
Admin      -> AuthService Instance #3
```

Each application has its own state.

This can cause unexpected behavior.

---

# Problem Example

## User Logs In

Shell stores:

```text
Current User = Balu
JWT Token = abc123
```

inside:

```typescript
AuthService
```

---

## Customer Remote Loads

Customer Remote creates a new AuthService.

Result:

```text
Current User = null
JWT Token = null
```

The user appears logged out.

---

# Desired Behavior

We want:

```text
Shell
Customer
Admin
Reports
```

to use the same AuthService instance.

```text
One Service
One State
One Authentication Context
```

---

# Example AuthService

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

  setUser(userName: string): void
  {
    this.currentUser.set(userName);
  }

  getUser(): string | null
  {
    return this.currentUser();
  }
}
```

---

# Service Sharing Through Shared Libraries

The recommended approach is:

```text
libs/
|
└── shared-auth
    |
    └── auth.service.ts
```

Instead of creating AuthService in every application.

---

# Example NX Library

```text
libs/
|
└── shared-auth
    |
    ├── auth.service.ts
    ├── auth.models.ts
    └── index.ts
```

---

# Why Use a Shared Library?

Benefits:

```text
Single Source of Truth
Reusable
Easy Maintenance
No Duplicate Code
```

---

# Module Federation Sharing

In module-federation.config.ts

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

# What Does singleton: true Mean?

```typescript
shared:
{
  '@my-org/shared-auth':
  {
    singleton: true
  }
}
```

This tells Module Federation:

```text
Create only one instance
Use it everywhere
```

---

# Without Singleton

```text
Shell
  |
  └── AuthService Instance #1

Customer
  |
  └── AuthService Instance #2

Admin
  |
  └── AuthService Instance #3
```

Three different services.

Three different states.

---

# With Singleton

```text
Shell
Customer
Admin
Reports
      |
      v

Shared AuthService Instance
```

Only one service exists.

---

# Sharing Through Angular Root Injector

Angular services using:

```typescript
@Injectable(
{
  providedIn: 'root'
})
```

are already singleton within a single Angular application.

Example:

```typescript
@Injectable(
{
  providedIn: 'root'
})
export class CustomerService
{
}
```

Within that application:

```text
One Instance
```

---

# Important Difference

## Inside One Angular Application

```typescript
providedIn: 'root'
```

creates:

```text
One Service Instance
```

---

## Across Multiple Federated Applications

```typescript
providedIn: 'root'
```

alone is not enough.

Module Federation must also share the library.

Example:

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

# Sharing State Between Applications

Suppose AuthService contains:

```typescript
private token: string;
private currentUser: User;
```

When Customer Remote updates:

```typescript
this.authService.setUser('Balu');
```

Admin Remote immediately sees:

```typescript
this.authService.getUser();
```

because both applications use the same instance.

---

# Common Services to Share

## Authentication Service

```text
Login
Logout
JWT Token
Current User
```

---

## User Service

```text
Profile
Preferences
Settings
```

---

## Notification Service

```text
Toast Messages
Alerts
Snackbars
```

---

## Theme Service

```text
Dark Mode
Light Mode
Brand Colors
```

---

## Configuration Service

```text
API URLs
Feature Flags
Application Settings
```

---

# Services Usually NOT Shared

Feature-specific services are often not shared.

Example:

```text
CustomerService
CustomerRepository
CustomerApiService
```

These belong only to the Customer Remote.

---

# Recommended Architecture

```text
apps/
|
├── shell
├── customer
├── admin
└── reports

libs/
|
├── shared-auth
├── shared-ui
├── shared-config
└── shared-models
```

Shared services live inside:

```text
libs/
```

and are configured as singletons.

---

# Real Enterprise Example

```text
Shell
├── Authentication
├── Navigation
└── Global Layout

Customer Remote
├── Customer Pages
└── Customer Services

Admin Remote
├── User Management
└── Admin Services

Shared Libraries
├── AuthService
├── NotificationService
├── ConfigService
└── Shared Models
```

---

# Common Mistake

Creating AuthService separately inside:

```text
apps/customer
apps/admin
apps/reports
```

Result:

```text
Multiple Auth States
Multiple Tokens
Synchronization Problems
```

Instead:

```text
libs/shared-auth
```

and share it using Module Federation.

---

# Interview Question

## How do you share Angular services in Module Federation?

### Answer

Angular services should be placed inside a shared NX library and configured in Module Federation using:

```typescript
shared:
{
  '@my-org/shared-auth':
  {
    singleton: true
  }
}
```

This ensures that Shell and Remote applications use the same service instance, allowing shared state such as authentication, user information, notifications, and configuration.

---

# Quick Summary

## Good Candidates for Sharing

```text
AuthService
UserService
NotificationService
ThemeService
ConfigService
```

---

## Usually Not Shared

```text
CustomerService
OrderService
ReportService
AdminService
```

because they belong to specific business domains.

---

# Rule to Remember

```text
Shared Service
        +
singleton: true
        =
One Instance Across All Micro Frontends
```

Without sharing:

```text
Multiple Services
Multiple States
```

With sharing:

```text
One Service
One Shared State
```

#### Key Interview Takeaway

The most important sentence to remember is:


providedIn: 'root' creates a singleton within one Angular application, but to share that singleton across Shell and Remote applications, the service must be placed in a shared library and configured with singleton: true in Module Federation.
