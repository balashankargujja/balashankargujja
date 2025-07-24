
# Angular Guards

#### 1. What types of guards are available in Angular?

CanActivate

CanActivateChild

CanDeactivate

Resolve

CanLoad

CanMatch (Angular 15+)

#### 2. What types of guards are available in Angular?
Angular provides the following built-in route guards:
| Guard Type                   | Description                                                                                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **CanActivate**              | Decides if a route can be activated. Often used for authentication checks.                                                                       |
| **CanActivateChild**         | Decides if child routes of a route can be activated. Useful for nested route protection.                                                         |
| **CanDeactivate**            | Decides if the user can leave the current route. Typically used to prevent unsaved changes loss.                                                 |
| **CanLoad**                  | Controls whether a lazy-loaded module can be loaded. Prevents downloading unauthorized modules.                                                  |
| **Resolve**                  | Pre-fetches data before the route is activated. Useful for data that must be ready when the component loads.                                     |
| **CanMatch** *(Angular 15+)* | Used to control whether a route configuration should be matched. More flexible than CanLoad for standalone components or advanced routing logic. |


#### 3. What is the purpose of CanActivate guard?
Answer:
The CanActivate guard determines whether a user is allowed to navigate to a specific route.

It is commonly used for:
- Authentication: Checking if the user is logged in.
- Authorization: Checking if the user has permission (e.g., role-based access).
If the guard returns:
- true → Navigation proceeds.
- false → Navigation is cancelled.
- UrlTree → Navigation is redirected to another route (e.g., /login).
Example usage:
```ts
@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): boolean | UrlTree {
    return this.authService.isLoggedIn()
      ? true
      : this.router.parseUrl('/login');
  }
}
```
Route configuration:
```ts
{
  path: 'dashboard',
  component: DashboardComponent,
  canActivate: [AuthGuard]
}
```


#### 4. How is a CanActivate guard implemented in Angular?
Ans:
To implement a CanActivate guard in Angular, follow these steps:
- Create the guard using Angular CLI:
```bash
ng generate guard auth
```
- Implement the `CanActivate` interface in the guard:
```ts
import { Injectable } from '@angular/core';
import {
  CanActivate,
  Router,
  UrlTree,
} from '@angular/router';
import { AuthService } from './auth.service'; // your custom service

@Injectable({
  providedIn: 'root',
})
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): boolean | UrlTree {
    const isAuthenticated = this.authService.isLoggedIn();
    return isAuthenticated ? true : this.router.parseUrl('/login');
  }
}
```
- Apply it in your route:
```ts
const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    canActivate: [AuthGuard],
  },
];
```

#### 5. What is the difference between CanActivate and CanActivateChild?
Ans:
| Feature      | CanActivate                            | CanActivateChild                                                       |
| ------------ | ------------------------------------------ | -------------------------------------------------------------------------- |
| **Purpose**  | Checks if the route can be **activated**.  | Checks if the **child routes** of a route can be activated.                |
| **Used On**  | Applied directly to a route.               | Applied to a parent route that has **child routes**.                       |
| **Use Case** | Protect routes like `/dashboard`.          | Protect nested routes like `/admin/users`, `/admin/settings`.              |
| **Example**  | Guards a route before loading a component. | Guards all children of a parent route (does not affect the parent itself). |

Example:
```ts
const routes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    canActivateChild: [AdminGuard],
    children: [
      { path: 'users', component: UserListComponent },
      { path: 'settings', component: SettingsComponent }
    ]
  }
];
```
Here, AdminGuard will be invoked only when navigating to admin/users or admin/settings, not for admin/ itself.

#### 6. Where do we register a guard in Angular?
Ans:
Guards are registered in the Routes configuration using specific guard properties:

Route-level Registration Example:
```ts
const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    canActivate: [AuthGuard] // Registering CanActivate guard
  },
  {
    path: 'admin',
    component: AdminComponent,
    canActivateChild: [AdminGuard], // Registering CanActivateChild guard
    children: [
      { path: 'settings', component: SettingsComponent }
    ]
  },
  {
    path: 'reports',
    loadChildren: () => import('./reports/reports.module').then(m => m.ReportsModule),
    canLoad: [PermissionGuard] // Registering CanLoad guard for lazy-loaded module
  }
];
```
Notes:
- You don’t need to manually add guards to providers; Angular does that automatically when you use `providedIn: 'root'`.
- The guards are added in the routing module (e.g., `app-routing.module.ts` or `feature-routing.module.ts`).

#### 7. How do you pass data to a guard?
Ans: You can pass static data to a guard via the route’s data property in the route configuration. The guard can then access this data using the ActivatedRouteSnapshot object.

Step-by-step Example:
- Route configuration with data:
```ts
{
  path: 'admin',
  component: AdminComponent,
  canActivate: [RoleGuard],
  data: { roles: ['admin', 'superadmin'] } // Passing data
}
```
- Accessing data in the guard:
```ts
@Injectable({ providedIn: 'root' })
export class RoleGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot): boolean {
    const allowedRoles = route.data['roles'] as string[];
    const userRole = this.authService.getUserRole();

    if (allowedRoles.includes(userRole)) {
      return true;
    } else {
      this.router.navigate(['/unauthorized']);
      return false;
    }
  }
}
```
#### Use case:
 Role-based route access, feature flags, permissions, etc.

####  8. What will happen if a guard returns false or an observable that resolves to false?
Ans: If a guard (like CanActivate, CanLoad, etc.) returns:
- `false`
- Angular cancels the navigation, and the user stays on the current page.
- The new route is not activated.
- `Observable<false>` or `Promise<false>`
- Angular waits for the async result, and once it resolves to `false`, navigation is still cancelled.
```ts
canActivate(): boolean {
  return false; // navigation is blocked
}
```
If you want to redirect instead of just blocking, return a `UrlTree`:
```ts
canActivate(): UrlTree {
  return this.router.parseUrl('/login');
}
```
This navigates the user to `/login` instead of just staying put.

#### 9. Explain CanLoad guard with an example.
Ans:
The CanLoad guard determines whether a lazy-loaded module should be loaded at all.

Use case:
- Prevents unauthorized users from even downloading the lazy-loaded module's code.
- Used in scenarios where security and performance are critical.
Lazy-loaded route with `CanLoad`:
```ts
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () =>
      import('./admin/admin.module').then(m => m.AdminModule),
    canLoad: [RoleGuard]
  }
];
```
`CanLoad` guard implementation:
```ts
@Injectable({ providedIn: 'root' })
export class RoleGuard implements CanLoad {
  constructor(private authService: AuthService, private router: Router) {}

  canLoad(): boolean {
    const hasAccess = this.authService.hasAdminAccess();
    if (!hasAccess) {
      this.router.navigate(['/unauthorized']);
    }
    return hasAccess;
  }
}
```
Important Notes:
- CanLoad runs before the lazy module is fetched.
- It does not receive `ActivatedRouteSnapshot`, only Route and `UrlSegment[]`.

#### 10. How is CanLoad different from CanActivate?
Ans:
Here’s a comparison table to clearly distinguish the two:
| Feature                 | CanActivate                                      | CanLoad                                                   |
| ----------------------- | ---------------------------------------------------- | -------------------------------------------------------------- |
| **Purpose**             | Checks if a route can be **navigated to**            | Checks if a **lazy-loaded module** can be loaded               |
| **Execution Time**      | After route is matched                               | Before module is even downloaded                               |
| **Route Type**          | Works on all routes                                  | Works only on lazy-loaded modules                              |
| **Parameters**          | Gets `ActivatedRouteSnapshot`, `RouterStateSnapshot` | Gets `Route`, `UrlSegment[]`                                   |
| **Security**            | Prevents unauthorized access to **route view**       | Prevents unauthorized **loading of module files**              |
| **Redirection Allowed** | ✅ Yes, using `UrlTree`                               | ⚠️ Redirection is tricky because navigation hasn’t started yet |

```ts
// CanActivate
{ path: 'dashboard', component: DashboardComponent, canActivate: [AuthGuard] }

// CanLoad
{ path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule), canLoad: [AuthGuard] }
```
 Best Practice: Use both `CanLoad` and `CanActivate` on lazy-loaded routes for full protection.

#### 11. What is the purpose of Resolve guard?
Ans: The Resolve guard is used to pre-fetch data before a route is activated. It ensures that necessary data is available before the component loads, avoiding intermediate loading states inside the component.
#### ✅ How it works:
- Data is fetched by the resolver.
- Navigation is paused until the data is available.
- The data is injected into the route's data property.
✅ Example:

🔸 Resolver Service:
```ts
@Injectable({ providedIn: 'root' })
export class UserResolver implements Resolve<User> {
  constructor(private userService: UserService) {}

  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    const userId = route.paramMap.get('id');
    return this.userService.getUserById(userId!);
  }
}
```
🔸 Route configuration:
```ts
{
  path: 'profile/:id',
  component: UserProfileComponent,
  resolve: { user: UserResolver }
}
```
🔸 In the component:
```ts
export class UserProfileComponent implements OnInit {
  user!: User;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.user = this.route.snapshot.data['user'];
  }
}
```
✅ This improves UX by showing the full view with complete data, avoiding empty templates or spinners.

#### 12. What is the role of ActivatedRouteSnapshot and RouterStateSnapshot in guards?
Ans:
These two snapshots provide route-related data to guards at the time of navigation.

✅ `ActivatedRouteSnapshot`
- Represents the currently matched route.
- Provides access to:
    - Route parameters (`route.params`)
    - Query parameters (`route.queryParams`)
    - Route data (`route.data`)
    - Route URL segments (`route.url`)
    - The configuration of the route (`route.routeConfig`)
```ts
canActivate(route: ActivatedRouteSnapshot): boolean {
  const id = route.paramMap.get('id');
  return !!id;
}
```

✅ `RouterStateSnapshot`
- Represents the entire router state at the time of navigation.
- Provides:
    - Full URL (routerState.url)
    - The tree of ActivatedRouteSnapshots
```ts
canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
  console.log('Target URL:', state.url);
  return true;
}
```
