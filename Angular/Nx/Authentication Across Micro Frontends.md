# Authentication Across Micro Frontends in Angular NX Module Federation

## Introduction

Authentication is one of the most important challenges in a Micro Frontend architecture.

A common question is:

> If a user logs in through the Shell application, how do all Remote applications know that the user is authenticated?

Without proper authentication sharing:

```text
Shell      -> User Logged In
Customer   -> User Logged Out
Admin      -> User Logged Out
Reports    -> User Logged Out
```

This creates a poor user experience.

The goal is:

```text
One Login
One Authentication State
All Applications Authenticated
```

---

# The Problem

Suppose we have:

```text
Shell
├── Customer Remote
├── Admin Remote
└── Reports Remote
```

User logs in through:

```text
Shell Application
```

Shell receives:

```text
JWT Token
Refresh Token
User Details
Roles
Permissions
```

Now Customer Remote loads.

Question:

```text
How does Customer Remote know who the user is?
```

---

# Wrong Approach

Each Remote manages authentication independently.

```text
Customer AuthService
Admin AuthService
Reports AuthService
```

Result:

```text
Multiple Login States
Multiple Tokens
Repeated API Calls
Synchronization Problems
```

Avoid this approach.

---

# Recommended Architecture

Authentication should be owned by the Shell.

```text
Shell
|
├── Login
├── Logout
├── Token Management
├── Refresh Token
└── User Session
```

Remotes should consume authentication information.

---

# Visual Architecture

```text
                User
                  |
                  v

             +---------+
             |  Shell  |
             +---------+
                  |
                  |
                  v

          Shared AuthService
                  |
      -------------------------
      |           |           |
      v           v           v

 Customer     Admin      Reports
  Remote      Remote      Remote
```

---

# Authentication Flow

## Step 1

User opens application.

```text
https://mycompany.com
```

---

## Step 2

Shell displays Login Page.

---

## Step 3

User enters:

```text
Username
Password
```

---

## Step 4

Shell calls backend API.

Example:

```text
POST /api/auth/login
```

---

## Step 5

Backend returns:

```json
{
  "token": "jwt-token",
  "refreshToken": "refresh-token",
  "userName": "Balu",
  "roles": [
    "Admin"
  ]
}
```

---

## Step 6

Shell stores authentication data.

---

## Step 7

All Remote applications access the same authentication state.

No additional login is required.

---

# Shared Auth Library

Recommended structure:

```text
libs/
|
└── shared-auth
    |
    ├── auth.service.ts
    ├── auth.models.ts
    ├── auth.guard.ts
    └── index.ts
```

---

# Auth Service Example

```typescript
@Injectable(
{
  providedIn: 'root'
})
export class AuthService
{
  private currentUser = signal<User | null>(
    null
  );

  private token = signal<string | null>(
    null
  );

  setAuthentication(
    user: User,
    jwtToken: string
  ): void
  {
    this.currentUser.set(user);
    this.token.set(jwtToken);
  }

  getCurrentUser(): User | null
  {
    return this.currentUser();
  }

  getToken(): string | null
  {
    return this.token();
  }
}
```

---

# Share the Library Through Module Federation

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

# Why singleton: true?

Without singleton:

```text
Shell
  └── AuthService Instance #1

Customer
  └── AuthService Instance #2

Admin
  └── AuthService Instance #3
```

Different login states.

---

With singleton:

```text
Shell
Customer
Admin
Reports
        |
        v

One Shared AuthService
```

All applications see the same authentication data.

---

# Route Protection

Authentication is often combined with route guards.

Example:

```typescript
export const authGuard: CanActivateFn =
(
  route,
  state
) =>
{
  const authService = inject(AuthService);

  return authService.getToken() !== null;
};
```

---

# Shell Route Protection

```typescript
{
  path: 'customers',

  canActivate:
  [
    authGuard
  ],

  loadChildren: () =>
    import('customer/Routes')
      .then((module) => module.remoteRoutes)
}
```

---

# Remote Route Protection

```typescript
{
  path: '',

  canActivate:
  [
    authGuard
  ],

  component: CustomerListComponent
}
```

Both Shell and Remote use the same guard.

---

# Passing JWT Token to APIs

Typically an HTTP interceptor is used.

```typescript
@Injectable()
export class AuthInterceptor
  implements HttpInterceptor
{
  intercept(
    request: HttpRequest<any>,
    next: HttpHandler
  )
  {
    const token =
      this.authService.getToken();

    if (token)
    {
      request =
        request.clone(
        {
          setHeaders:
          {
            Authorization:
              `Bearer ${token}`
          }
        });
    }

    return next.handle(request);
  }
}
```

---

# Token Storage Options

## Option 1 - Memory

```text
Signal
BehaviorSubject
Service Property
```

Most secure.

But user loses session on refresh.

---

## Option 2 - Session Storage

```typescript
sessionStorage.setItem(
  'token',
  token
);
```

Session survives page refresh.

Cleared when browser closes.

---

## Option 3 - Local Storage

```typescript
localStorage.setItem(
  'token',
  token
);
```

Survives browser restart.

Commonly used.

---

# Enterprise Recommendation

Many organizations use:

```text
Access Token
+
Refresh Token
```

Flow:

```text
Login
   |
   v

Access Token Expires
   |
   v

Refresh Token Used
   |
   v

New Access Token Issued
```

User remains logged in.

---

# Authentication vs Authorization

These are different concepts.

---

## Authentication

```text
Who are you?
```

Example:

```text
Username
Password
JWT Token
```

---

## Authorization

```text
What are you allowed to do?
```

Example:

```text
Admin
Manager
Employee
```

Permissions:

```text
Create User
Delete User
View Reports
```

---

# Role-Based Access Example

```json
{
  "userName": "Balu",
  "roles":
  [
    "Admin"
  ]
}
```

---

# Role Check Service

```typescript
hasRole(
  role: string
): boolean
{
  const user =
    this.getCurrentUser();

  return (
    user?.roles.includes(role)
    ?? false
  );
}
```

---

# Example Usage

```typescript
if (
  this.authService.hasRole(
    'Admin'
  )
)
{
  // Show Admin Menu
}
```

---

# Logout Flow

## Step 1

User clicks Logout.

---

## Step 2

Shell clears:

```text
JWT Token
Refresh Token
User Information
```

---

## Step 3

Shared AuthService updates.

---

## Step 4

All Remotes immediately become unauthenticated.

---

# Recommended Enterprise Structure

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

Authentication belongs inside:

```text
libs/shared-auth
```

and is shared across all applications.

---

# Common Mistakes

## Mistake 1

Creating AuthService inside every Remote.

Result:

```text
Multiple Login States
```

---

## Mistake 2

Duplicating JWT storage.

Result:

```text
Synchronization Problems
```

---

## Mistake 3

Each Remote performs its own login.

Result:

```text
Repeated Authentication
Poor User Experience
```

---

# Interview Question

## How is authentication handled in Micro Frontends?

### Answer

Authentication is typically managed by the Shell application. Authentication logic is placed inside a shared library and exposed through a shared singleton AuthService. Remote applications consume the same AuthService instance to access user information, JWT tokens, roles, and permissions. This ensures a single authentication state across all Micro Frontends.

---

# Quick Summary

```text
Shell Owns Authentication
            |
            v

Shared AuthService
            |
            v

Customer Remote
Admin Remote
Reports Remote
```

---

# Rule to Remember

```text
One Login
One AuthService
One JWT Token
One Authentication State
```

Across:

```text
Shell
Customer
Admin
Reports
```

All Micro Frontends remain synchronized.
