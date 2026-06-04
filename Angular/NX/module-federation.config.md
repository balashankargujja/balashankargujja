# Module Federation Configuration in Angular NX

## What is `module-federation.config.ts`?

In an Angular NX workspace, the `module-federation.config.ts` file is used to configure **Webpack Module Federation**, which enables a **Micro Frontend Architecture**.

Module Federation allows multiple Angular applications to work together while being developed, built, and deployed independently.

---

# Simple Example

Suppose we have three Angular applications:

```text
Shell Application
Customer Application
Admin Application
```

Instead of deploying everything together, each application can be deployed independently.

The Shell application can load Customer and Admin applications at runtime.

---

# Typical NX Structure

```text
apps/
|
├── shell
│   └── module-federation.config.ts
|
├── customer
│   └── module-federation.config.ts
|
└── admin
    └── module-federation.config.ts
```

---

# Shell Application Configuration

```typescript
import { ModuleFederationConfig } from '@nx/module-federation';

const config: ModuleFederationConfig =
{
  remotes:
  [
    'customer',
    'admin'
  ]
};

export default config;
```

## Meaning

The Shell application knows that it can load:

```text
customer application
admin application
```

at runtime.

---

# Remote Application Configuration

Example: Customer Application

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

## Meaning

The Customer application exposes its routes so that other applications can load them.

---

# How Module Federation Works

## Step 1

User opens:

```text
http://localhost:4200
```

Shell application starts.

## Step 2

User clicks:

```text
Customers
```

## Step 3

Shell application requests Customer application.

```text
http://localhost:4201
```

## Step 4

Customer application's routes and components are downloaded dynamically.

No rebuild of the Shell application is required.

---

# Important Properties

## 1. name

Unique name of the remote application.

```typescript
name: 'customer'
```

---

## 2. remotes

Used in Shell applications.

```typescript
remotes:
[
  'customer',
  'admin'
]
```

Defines which remote applications can be loaded.

---

## 3. exposes

Used in Remote applications.

```typescript
exposes:
{
  './Routes':
    'apps/customer/src/app/remote-entry/entry.routes.ts'
}
```

Defines what files can be consumed by other applications.

---

## 4. shared

Defines libraries shared between applications.

```typescript
shared:
{
  '@angular/core':
  {
    singleton: true,
    strictVersion: true
  }
}
```

This prevents duplicate downloads.

---

# Sharing Angular Libraries

```typescript
shared:
{
  '@angular/core':
  {
    singleton: true,
    strictVersion: true
  },

  '@angular/common':
  {
    singleton: true,
    strictVersion: true
  },

  '@angular/router':
  {
    singleton: true,
    strictVersion: true
  }
}
```

## Benefit

Without sharing:

```text
Shell downloads Angular
Customer downloads Angular
Admin downloads Angular
```

With sharing:

```text
Angular downloaded only once
```

---

# Sharing Internal NX Libraries

Workspace example:

```text
libs/
|
├── ui
├── shared-models
├── shared-services
```

Configuration:

```typescript
shared:
{
  '@my-org/ui':
  {
    singleton: true
  },

  '@my-org/shared-models':
  {
    singleton: true
  }
}
```

## Benefit

Avoids duplicate code and bundles.

---

# Lazy Loading Remote Applications

Remote application exposes routes.

```typescript
exposes:
{
  './Routes':
    'apps/customer/src/app/remote-entry/entry.routes.ts'
}
```

Shell loads them lazily.

```typescript
{
  path: 'customers',

  loadChildren: () =>
    import('customer/Routes')
      .then((module) => module.remoteRoutes)
}
```

## Benefit

Remote application loads only when required.

---

# Dynamic Remote Loading

Static configuration:

```typescript
remotes:
[
  'customer'
]
```

Dynamic configuration allows different URLs per environment.

Example:

```text
Development:
customer -> localhost:4201

QA:
customer -> qa-customer.company.com

Production:
customer -> customer.company.com
```

## Benefit

Supports environment-specific deployments.

---

# Exposing More Than Routes

Most developers expose routes only.

However, you can expose:

## Components

```typescript
exposes:
{
  './CustomerCard':
    'apps/customer/src/app/customer-card/customer-card.component.ts'
}
```

---

## Services

```typescript
exposes:
{
  './CustomerService':
    'apps/customer/src/app/services/customer.service.ts'
}
```

---

## Utilities

```typescript
exposes:
{
  './Utils':
    'apps/customer/src/app/utils/customer-utils.ts'
}
```

---

## Models

```typescript
exposes:
{
  './Models':
    'apps/customer/src/app/models/customer.model.ts'
}
```

---

# Version Compatibility

```typescript
shared:
{
  '@angular/core':
  {
    singleton: true,
    strictVersion: true
  }
}
```

## Why?

Example:

```text
Shell -> Angular 20
Customer -> Angular 19
```

Different Angular versions can cause runtime issues.

`strictVersion: true`

ensures compatible versions are used.

---

# Singleton Services

```typescript
shared:
{
  '@angular/core':
  {
    singleton: true
  }
}
```

## Why?

Without singleton:

```text
Shell creates AuthService
Customer creates AuthService
```

Result:

```text
Two separate login states
```

With singleton:

```text
One shared AuthService instance
```

---

# State Sharing

Module Federation can help share application state.

Examples:

```text
Authentication State
User Profile
Application Settings
NgRx Store
Signals Store
```

This allows multiple micro frontends to use the same state.

---

# Environment-Specific Configuration

Different environments may require different remote URLs.

```text
Development
QA
UAT
Production
```

Example:

```text
Development:
localhost:4201

QA:
qa-customer.company.com

Production:
customer.company.com
```

---

# Debugging Module Federation

One of the first things to verify is:

```text
remoteEntry.js
```

Example:

```text
http://localhost:4201/remoteEntry.js
```

If this file is unavailable:

```text
Remote application cannot be loaded.
```

---

# What Senior Developers Usually Configure

A production-ready setup often includes:

```text
✔ Shared Angular libraries

✔ Shared internal libraries

✔ Singleton services

✔ Strict version checking

✔ Lazy loading

✔ Dynamic remotes

✔ Environment-specific URLs

✔ Shared authentication state

✔ Shared UI libraries
```

---

# When Can You Ignore This File?

If your NX workspace contains only:

```text
One Angular Application
```

and you are not implementing Micro Frontends, then `module-federation.config.ts` is generally not important for daily Angular development.

---

# Interview Question

## What is `module-federation.config.ts` in Angular NX?

Answer:

`module-federation.config.ts` is the configuration file used by NX Module Federation to implement a Micro Frontend architecture. It defines which applications act as Shells and which act as Remotes, what modules are exposed, what remotes can be loaded, and which libraries are shared between applications at runtime. This allows Angular applications to be developed and deployed independently while working together as a single system.
