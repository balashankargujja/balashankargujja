# NX Module Federation Architecture

## Introduction

NX Module Federation Architecture is a way of building large Angular applications using:

```text
NX Workspace
+
Webpack Module Federation
+
Micro Frontend Architecture
```

Instead of creating one huge Angular application, we split the system into multiple smaller applications that can be:

```text
Developed Independently
Built Independently
Tested Independently
Deployed Independently
```

---

# Why Do We Need It?

Imagine an enterprise application:

```text
Customer Management
Order Management
Reports
Administration
Notifications
Settings
```

If everything is placed inside one Angular application:

```text
Huge Code Base
Long Build Times
Large Bundles
Deployment Bottlenecks
Team Conflicts
```

As the application grows, maintenance becomes difficult.

---

# Traditional Monolithic Architecture

```text
Angular Application
|
├── Customer Module
├── Orders Module
├── Reports Module
├── Admin Module
└── Settings Module
```

### Problems

```text
One Build
One Deployment
One Failure Point
Large Bundle Size
```

Any small change requires:

```text
Build Entire Application
Deploy Entire Application
```

---

# NX Module Federation Architecture

Instead of one application:

```text
Shell
Customer Remote
Orders Remote
Reports Remote
Admin Remote
```

Each application becomes independent.

---

# High-Level Architecture

```text
                    User
                      |
                      v

                +-----------+
                |   Shell   |
                +-----------+
                      |
      -------------------------------------
      |          |          |            |
      v          v          v            v

+-----------+ +---------+ +---------+ +---------+
| Customer  | | Orders  | | Reports | | Admin   |
|  Remote   | | Remote  | | Remote  | | Remote  |
+-----------+ +---------+ +---------+ +---------+
```

---

# Main Building Blocks

NX Module Federation consists of:

```text
1. Shell Application
2. Remote Applications
3. Shared Libraries
4. Module Federation Configuration
5. remoteEntry.js
```

---

# 1. Shell Application

The Shell is the host application.

Responsibilities:

```text
Authentication
Navigation
Layout
Global State
Loading Remotes
```

Example:

```text
apps/
|
└── shell
```

---

## Shell Configuration

```typescript
import { ModuleFederationConfig }
  from '@nx/module-federation';

const config: ModuleFederationConfig =
{
  remotes:
  [
    'customer',
    'orders',
    'reports',
    'admin'
  ]
};

export default config;
```

---

# 2. Remote Applications

Remote applications contain business functionality.

Example:

```text
apps/
|
├── customer
├── orders
├── reports
└── admin
```

---

## Customer Remote

Responsibilities:

```text
Customer List
Customer Details
Customer Create
Customer Update
```

---

## Remote Configuration

```typescript
import { ModuleFederationConfig }
  from '@nx/module-federation';

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

# 3. Shared Libraries

Shared code should live inside:

```text
libs/
```

---

## Example Structure

```text
libs/
|
├── shared-auth
├── shared-ui
├── shared-config
├── shared-models
└── shared-utils
```

---

## Benefits

```text
Code Reuse
Consistency
Single Source of Truth
Reduced Duplication
```

---

# 4. Module Federation Configuration

Each application contains:

```text
module-federation.config.ts
```

This file defines:

```text
Remotes
Exposes
Shared Libraries
Singleton Services
Version Rules
```

---

# Example

```typescript
const config: ModuleFederationConfig =
{
  name: 'customer',

  exposes:
  {
    './Routes':
      'apps/customer/src/app/remote-entry/entry.routes.ts'
  },

  shared:
  {
    '@my-org/shared-auth':
    {
      singleton: true
    }
  }
};
```

---

# 5. remoteEntry.js

Webpack automatically generates:

```text
remoteEntry.js
```

Example:

```text
http://localhost:4201/remoteEntry.js
```

---

## Purpose

It tells the Shell:

```text
What Modules Exist
What Components Exist
What Routes Exist
How to Load Them
```

---

# Request Flow

## Step 1

User opens:

```text
http://localhost:4200
```

Shell loads.

---

## Step 2

User clicks:

```text
Customers
```

---

## Step 3

Shell requests:

```text
http://localhost:4201/remoteEntry.js
```

---

## Step 4

Customer Remote responds.

---

## Step 5

Shell loads Customer routes.

---

# Full Request Flow Diagram

```text
User
 |
 v

Shell
 |
 | Request remoteEntry.js
 v

Customer Remote
 |
 | Return metadata
 v

Shell
 |
 | Load Routes
 v

Customer Pages
```

---

# Workspace Structure

A typical NX workspace:

```text
apps/
|
├── shell
├── customer
├── orders
├── reports
└── admin

libs/
|
├── shared-auth
├── shared-ui
├── shared-config
├── shared-models
└── shared-utils
```

---

# Shared Authentication Architecture

```text
Shell
Customer
Orders
Reports
Admin
      |
      v

Shared AuthService
```

Configuration:

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

# Communication Architecture

Recommended approach:

```text
Shared Services
+
Shared State
+
Backend APIs
```

---

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

## Business Data

```text
Backend APIs
```

---

# Deployment Architecture

Development:

```text
Shell     -> localhost:4200
Customer  -> localhost:4201
Orders    -> localhost:4202
Reports   -> localhost:4203
Admin     -> localhost:4204
```

---

Production:

```text
portal.company.com
customer.company.com
orders.company.com
reports.company.com
admin.company.com
```

---

# Independent Deployment

Customer Team deploys:

```text
Customer Remote
```

Only.

No deployment required for:

```text
Shell
Orders
Reports
Admin
```

---

# Team Ownership Model

```text
Platform Team
   |
   └── Shell

Customer Team
   |
   └── Customer Remote

Orders Team
   |
   └── Orders Remote

Reports Team
   |
   └── Reports Remote

Admin Team
   |
   └── Admin Remote
```

---

# Benefits of NX Module Federation

## Faster Builds

```text
Build Only What Changed
```

---

## Smaller Bundles

```text
Load Features On Demand
```

---

## Independent Deployments

```text
Deploy One Remote
Without Deploying Everything
```

---

## Team Independence

```text
Multiple Teams
Working Simultaneously
```

---

## Better Scalability

Large applications become easier to manage.

---

# Common Enterprise Structure

```text
apps/
|
├── shell
├── customer
├── orders
├── reports
└── admin

libs/
|
├── shared-auth
├── shared-ui
├── shared-config
├── shared-models
├── shared-services
└── shared-utils
```

This is the structure commonly found in large Angular NX projects.

---

# Architecture Summary

```text
                    User
                      |
                      v

                 +---------+
                 | Shell   |
                 +---------+
                      |
      -----------------------------------
      |          |         |           |
      v          v         v           v

 Customer     Orders    Reports     Admin
  Remote      Remote     Remote     Remote

      \          |         |          /
       \         |         |         /
        \        |         |        /
         ---------------------------
                     |
                     v

               Shared Libraries

         Auth | UI | Config | Models
```

---

# Interview Question

## What is NX Module Federation Architecture?

### Answer

NX Module Federation Architecture is a Micro Frontend architecture that combines NX Workspace and Webpack Module Federation. It consists of a Shell application that dynamically loads multiple Remote applications at runtime. Shared libraries are used for common functionality, and each Remote can be developed, built, tested, and deployed independently.

---

# Quick Summary

## Shell

```text
Host Application
```

Responsibilities:

```text
Authentication
Navigation
Layout
Remote Loading
```

---

## Remotes

```text
Feature Applications
```

Responsibilities:

```text
Business Features
Pages
Services
Components
```

---

## Shared Libraries

```text
Reusable Code
```

Examples:

```text
Auth
UI
Models
Config
```

---

## remoteEntry.js

```text
Remote Manifest
```

Allows Shell to discover and load Remote modules.

---

# Rule to Remember

```text
Shell
   |
   v

Loads Remotes
   |
   v

Remotes Use Shared Libraries
   |
   v

Each Remote Can Be Deployed Independently
```

This is the core idea behind NX Module Federation Architecture.
