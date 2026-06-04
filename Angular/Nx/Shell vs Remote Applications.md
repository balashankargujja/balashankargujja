# Shell vs Remote Applications in Angular NX Module Federation

## Introduction

When working with Module Federation in Angular NX, you will frequently hear two terms:

1. Shell Application
2. Remote Application

Understanding these two concepts is the foundation of Micro Frontend Architecture.

---

# Real-World Analogy

Imagine a shopping mall.

The mall itself is the **Shell**.

Inside the mall, there are many independent stores:

```text
Clothing Store
Electronics Store
Food Court
Book Store
```

These stores are the **Remotes**.

The mall provides:

* Navigation
* Security
* Entry and Exit
* Common Infrastructure

Each store manages:

* Its own products
* Its own employees
* Its own deployment

The same idea applies to Angular Module Federation.

---

# What is a Shell Application?

A Shell Application is the main Angular application that users access first.

It acts as the host application.

Responsibilities include:

* Application layout
* Navigation menu
* Authentication
* Routing
* Loading remote applications
* Shared UI

---

## Example

```text
https://mycompany.com
```

User opens:

```text
https://mycompany.com
```

The Shell application loads first.

---

## Shell Application Structure

```text
apps/
|
└── shell
```

---

## Shell Configuration

```typescript
import { ModuleFederationConfig } from '@nx/module-federation';

const config: ModuleFederationConfig =
{
  remotes:
  [
    'customer',
    'admin',
    'reports'
  ]
};

export default config;
```

---

## What Does the Shell Do?

### Step 1

User opens application.

```text
https://mycompany.com
```

---

### Step 2

Shell loads.

```text
Navigation Bar
Header
Footer
Authentication
```

---

### Step 3

User clicks:

```text
Customers
```

---

### Step 4

Shell loads Customer Remote.

---

# What is a Remote Application?

A Remote Application is an independent Angular application that exposes functionality to other applications.

A Remote does not usually run as the main entry point for users.

Instead, it is loaded by the Shell.

---

## Example

```text
apps/
|
├── customer
├── admin
└── reports
```

Each application is a Remote.

---

## Customer Remote Configuration

```typescript
import { ModuleFederationConfig } from '@nx/module-federation';

const config: ModuleFederationConfig =
{
  name: 'customer',

  exposes:
  {
    './Routes':
      'apps/customer/src/app/remote-entry/entry.routes.ts'
  }
};

export default config;
```

---

## What Does the Remote Do?

The Customer Remote may contain:

```text
Customer List
Customer Details
Customer Create
Customer Update
```

The Admin Remote may contain:

```text
User Management
Role Management
Permissions
```

The Reports Remote may contain:

```text
Sales Reports
Revenue Reports
Dashboard Reports
```

Each Remote owns its own business functionality.

---

# Visual Architecture

```text
                     User
                       |
                       v

                 +-----------+
                 |   Shell   |
                 +-----------+
                  /    |    \
                 /     |     \
                v      v      v

        +---------+ +---------+ +---------+
        |Customer | | Admin   | | Reports |
        | Remote  | | Remote  | | Remote  |
        +---------+ +---------+ +---------+
```

---

# How Routing Works

## Shell Routes

```typescript
export const appRoutes =
[
  {
    path: 'customers',

    loadChildren: () =>
      import('customer/Routes')
        .then((module) => module.remoteRoutes)
  },

  {
    path: 'admin',

    loadChildren: () =>
      import('admin/Routes')
        .then((module) => module.remoteRoutes)
  }
];
```

---

## Customer Remote Routes

```typescript
export const remoteRoutes =
[
  {
    path: '',
    component: CustomerListComponent
  }
];
```

---

# Who Owns What?

## Shell Owns

```text
Application Layout
Header
Footer
Navigation Menu
Authentication
Authorization
Global State
Remote Loading
```

---

## Remote Owns

```text
Business Logic
Components
Pages
Services
Feature Modules
Feature Routing
```

---

# Deployment Example

Without Module Federation:

```text
One Application

Build Everything
Deploy Everything
```

Any small change requires redeploying the whole application.

---

With Module Federation:

```text
Shell
Customer
Admin
Reports
```

Each application can be deployed independently.

---

## Example

Customer team fixes a bug.

Only Customer Remote is deployed.

```text
Customer Remote -> Deploy
```

No need to redeploy:

```text
Shell
Admin
Reports
```

---

# Benefits of Shell and Remote Architecture

## Independent Teams

```text
Team A -> Customer
Team B -> Admin
Team C -> Reports
```

Teams work independently.

---

## Faster Builds

Only the changed application is built.

---

## Independent Deployments

Each application can be released separately.

---

## Better Scalability

Large applications become easier to manage.

---

## Smaller Bundles

Features load only when required.

---

# Example NX Workspace

```text
apps/
|
├── shell
|
├── customer
|
├── admin
|
└── reports

libs/
|
├── ui
├── shared-services
└── shared-models
```

---

# Interview Question

## What is the difference between Shell and Remote applications?

### Answer

A Shell Application is the host application responsible for navigation, authentication, layout, and loading remote applications.

A Remote Application is an independent Angular application that exposes functionality such as routes, components, services, or features that can be loaded by the Shell at runtime.

The Shell coordinates the overall user experience, while Remotes provide specific business capabilities.
