# Frontend

---

# Git Strategy

```
BEGIN Sprint

    merge main -> backend
    merge main -> client
    merge client -> client-**

    PUSH CHANGES

    merge backend -> main
    merge client-xx -> client
    merge client -> main

END Sprint
```

# Directory Structure

-   `index.ts` files are used for import/exports.
-   pages module should not change much in the future
-   new React components can be added to Components module or Views module
-   most changes will occur in views module for milestone #3

```
📦src
 ┣ 📂auth                       Auth module: manages user roles and login
 ┃ ┣ 📜[auth files]
 ┃ ┗ 📜index.ts
 ┣ 📂components                 Components module: manages miscellaneous/re-usable components
 ┃ ┣ 📂forms
 ┃ ┃ ┣ [form components]
 ┃ ┃ ┗ 📜index.ts
 ┃ ┣ 📜[misc components]
 ┣ 📂pages                      Pages module: manages pages. Pages render children from Components or Views module
 ┃ ┣ 📜Dashboard.tsx
 ┃ ┣ 📜Landing.tsx
 ┃ ┣ 📜SignIn.tsx
 ┃ ┣ 📜SignUp.tsx
 ┃ ┗ 📜index.ts
 ┣ 📂types                      Types module: manages shared types and schemas
 ┃ ┣ 📜[types and schemas]
 ┣ 📂views                      Views module: manages authenticated views for customer, manager, owner, and shared
 ┃ ┣ 📂customer
 ┃ ┃ ┣ 📜[customer only views]
 ┃ ┃ ┗ 📜index.ts
 ┃ ┣ 📂manager
 ┃ ┃ ┣ 📜[manager only views]
 ┃ ┃ ┗ 📜index.ts
 ┃ ┣ 📂owner
 ┃ ┃ ┣ 📜[owner only views]
 ┃ ┃ ┗ 📜index.ts
 ┃ ┣ 📂shared
 ┃ ┃ ┣ 📜[shared by all views]
 ┃ ┃ ┗ 📜index.ts
 ┃ ┗ 📜index.ts
```

# Routes

viewing newly developed components can be done by modifying App.tsx:

```typescript
{/* DEV ROUTES BEGIN*/}
// Test view by itself on localhost:3000/dev/custom_component
<Route path="/dev/custom_component">
   <CustomComponent />
</Route>
// Test view inside Dashboard on localhost:3000/dev/dashboard/custom_component
<Route path="/dev/dashboard/custom_component">
   <Dashboard>
       <CustomComponent />
   </Dashboard>
</Route>
{/* DEV ROUTES END */}
```

developing authenticated routes requires mysql database set up in dev environment. Autheticated routes are marked in App.tsx with `route` in `Routes` array coming from `Routes.tsx`:

```typescript
{
    /* AUTHENTICATED ROUTES BEGIN*/
}
{
    Routes.map((route, idx) => (
        <AuthGuard
            path={route.path}
            redirect="/"
            authRedirect="/dashboard/home"
            key={idx}
            account_type={route.account_type}
        >
            <Dashboard>
                <route.content />
            </Dashboard>
        </AuthGuard>
    ));
}
{
    /* AUTHENTICATED ROUTES END*/
}
```

# Dashboard

`Dashboard.tsx` serves as a container for all authenticated users (customer, manager, owner) that renders the current "view".

For example, a customer logged in will see links to components in the `views/customer` and `views/shared` directories on the Dashboard navigation drawer. Clicking the link 'Docks' will then render `views/customer/Docks.tsx` inside `pages/Dashboard.tsx`:

```typescript
{
<Dashboard>
    <Docks>
</Dashboard>
}
```

Currently links on `NavDrawer.tsx` are hard coded to display customer views only. Will be refactored in future to display correct links for manager and owner.

# Views

Naming and functionality of each view are subject to change. Current state of views are detailed below:

## Shared

### Home

View rendered when customer, manager, or owner first logs-in. Displays "welcome, `username`". May display summaries of other views. May be removed as redundant.

### Account

View that allows customer, manager, or owner to edit account information. Low priority on implementing

## Customer

### Docks

View that shows docks and allows customer to rent bike

### Transaction

View that currently shows confirmation for renting bike. May be refactored to show current rentals, previous rentals, outstanding fees, and/or rental due dates.

## Manager

### VerifyPurchases

View that allows manager to confirm a given customer paid for a bike or confirm a given customer returned a bike. May be refactored to multiple views.

## Owner

### PriceControl

View that allows owner to set prices at docks

### Management

View that allows owner to create/delete manager accounts

### Reports

View that shows weekly bike dock reports

# Known Issues

| Bug                                           | Steps to Recreate           | Possible Fix                                      |
| --------------------------------------------- | --------------------------- | ------------------------------------------------- |
| Landing page flashes before loading dashboard | Refresh page when signed in | Create loading context to display loading spinner |