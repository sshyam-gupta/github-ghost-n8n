React is a very popular and growing JavaScript library. When we think in the Context of MVC architecture, so React comprises of **View** in MVC architecture. For managing application state we may need to rely on other JavaScript state management libraries like **Redux** or if you are using React v16+, so you can use React's [Context API](https://reactjs.org/docs/context.html) in combination with [useReducer hook](https://reactjs.org/docs/hooks-reference.html#usereducer) to serve our state management purpose.

> Good architecture makes the system easy to understand, easy to develop, easy to maintain, and easy to deploy. The ultimate goal is to minimize the lifetime cost of the system and to maximize programmer productivity. - Robert C. Martin

React is evolving yet still lacks guidelines when it comes to architecting the applications. But when we have to design enterprise-level applications, so the most important thing that should be covered is clean architecture, so that it's easy for developers to understand and collaborate.

Let's take an example of the application, where every user will have multiple roles and permissions to access the application. Of course, such applications can have conditional-based logic for different roles and permissions.
Any React application can start well until we start adding condition-based logic on top of it. Soon it can start getting worse upon adding granular checks for roles and permissions for user access.
Holding all this logic and keeping track of roles and permissions is a cumbersome task when used with normal `conditional checks` or `switch statements`.

## How did we architected this flow?

In one of our projects, we've also dealt with lots of roles and permissions-based user flow. In addition, we've token-based authorization in our application so we were storing the token in `local storage` just to keep track of the authorized user. And all the user details like `permissions`, `roles`, `email` and all basic info were stored in `context (user context)`. 

![Per1-2](https://images.unsplash.com/photo-1503387837-b154d5074bd2?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1331&q=80)

To verify whether the logged-in user has required **role/permission** to access any specific `route/action` item we've implemented several hooks like `useHasPermissions`, `useHasRoles` which were returning the boolean (**true** or **false**) if the current user has those specific `roles/permissions`. 

To check for specific permissions/roles, the hooks `useHasPermissions` or `useHasRoles` need the current user context. So here we have two approaches, either we can directly access the `user context` into these hooks or we can create a separate hook as `useUser` which will act as a consumer for `user context`.  

The `useUser` hook will return the logged-in user. So from now onwards, throughout the lifetime of our application, whenever we have to access the logged-in user, instead of accessing context directly, we can simply call this hook and get the current user details.


<iframe src="https://codesandbox.io/embed/permissions-with-hooks-ykfpd?fontsize=14&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="Permissions with HOOKS"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

&nbsp;
Refer to the 👆🏼 above example

Here we are having 4 different types of Roles `SuperAdmin`, `Admin`, `Customer`, `Employee`.
Also, there are a few sets of permissions that have been assigned to specific roles, based on permissions we've action buttons enabled on the landing screen post-login flow.

You can even change permissions in `getPermissions` util located in *`utils/roles.js`* file and observed the behavior for different role users.


## Working of `useHasPermissions` hook:

![useHasPermissions-1](https://unsplash.com/photos/yNvVnPcurD8)

The main task of `useHasPermissions` hook is to verify and return the boolean flag if the currently logged-in user has the permission which the app needs. We can either pass permission as `string` or an `array of permissions`. 
This hook gets the current user context with the help of `useUser` hook. And checks for the `permission/s` needed with the permissions from `usercontext`.

### How to use `useHasPermissions` hook ?

```
const canComment = useHasPermissions(['CAN_READ','CAN_ADD_COMMENT']);  //checks for multiple permissions
const canRead = useHasPermissions('CAN_READ'); //checks for single permission
```

## Working of `useHasRoles` hook:
The working of `useHasRoles` is similar to `useHasPermissions`, it will just verify the `Roles` instead of `Permissions`.

```
const hasAdminRole = useHasRoles(['ADMIN']);  // checks for multiple roles
const hasRoleSuperAdmin = useHasRoles('SUPER_ADMIN'); //checks for single role
```

### Using  `useHasRole` as AuthGuard

In the above example, instead of verifying the `role` to enable/disable the action button, we are checking whether a user has access to a specific route or not. Here, we've implemented `ProtectedRoute` as HOC which will act as **AuthGuard** for our application. It will only allow the authorized users to access the specific 
route.

Along with acting as `Auth Guard`, it helps in claiming a specific route. There might be few routes in the application that should be accessed by users having specific roles. To claim route for specific roles, we just need to pass prop as `claimWithRole` as a `role` or an array of `roles` to `ProtectedRoute`.

As in the above example, we have the `/admin` route which can be accessed by the user having roles `ADMIN/SUPER_ADMIN`. 

So if you try login using customer and click on the `Go to Admin` link, then it will show the message as `You do not have specific roles/permissions to access this route`.

> As here we've added claiming routes by Roles only, you can even claim a route by permissions. Just need to add few lines of code to check for desired permissions.

Hope this article was able to throw light on setting up roles and permissions based architecture in React applications.

Thank you for reading!
