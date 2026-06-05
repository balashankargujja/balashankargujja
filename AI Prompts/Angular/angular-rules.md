# Angular Best Practices

## General Rules

* Use clear, meaningful, and intention-revealing method names.
* Method names should explain **what the method does**.
* Always use **camelCase**.
* Use **verb-first naming** for all methods.
* Avoid short or unclear names like:

  * `doWork()`
  * `handle()`
  * `data()`
  * `temp()`
  * `click()`

Prefer:

* `loadCustomerDetails()`
* `calculateTotalAmount()`
* `validateFormInputs()`

---

## Naming Based on Method Purpose

### Event Methods

Prefix with:

* `on`
* `handle`

Examples:

```ts
onSaveClick()
onSubmit()
onUserSelect()
handleDropdownChange()
handleSearchInput()
```

Use `on` mainly for UI-triggered events.

---

## Data Loading Methods

Prefix with:

* `load`
* `fetch`
* `get`

Examples:

```ts
loadProducts()
fetchUsers()
getCustomerById()
loadDashboardData()
```

Guidelines:

* `load` → component-level loading
* `fetch` → API/server call
* `get` → returns value/data

---

## Save / Update / Delete Operations

Use clear action names.

Examples:

```ts
saveCustomer()
updateCampaign()
deleteCoupon()
createTemplate()
submitForm()
```

Avoid:

```ts
save()
update()
delete()
```

because they are too generic.

---

## Validation Methods

Prefix with:

* `validate`
* `is`
* `has`
* `can`

Examples:

```ts
validateEmail()
isFormValid()
hasPermission()
canSubmitForm()
```

Guidelines:

* `is`, `has`, `can` should return boolean values.

---

## Calculation Methods

Prefix with:

* `calculate`
* `compute`

Examples:

```ts
calculateDiscount()
calculateTotalPrice()
computeTaxAmount()
```

---

## State / UI Methods

Use action-based names.

Examples:

```ts
openDialog()
closePopup()
toggleSidebar()
showLoader()
hideSpinner()
resetFilters()
clearSelection()
```

---

## Navigation Methods

Prefix with:

* `navigate`
* `redirect`

Examples:

```ts
navigateToDashboard()
redirectToLogin()
navigateToDetailsPage()
```

---

# Async Method Naming

Suffix async methods with `Async` only if your project follows that convention consistently.

Examples:

```ts
loadUsersAsync()
saveOrderAsync()
```

Otherwise:

```ts
loadUsers()
saveOrder()
```

Consistency is more important.

---

# Boolean Method Naming

Always make boolean methods read like sentences.

Correct:

```ts
isAdminUser()
hasActiveSubscription()
canEditRecord()
```

Wrong:

```ts
checkAdmin()
subscription()
editAccess()
```
---

## Avoid Method Overloading Behavior

One method should perform only one logical action.

Bad:

```ts
saveAndNavigate()
loadAndFilterData()
```

Prefer:

```ts
saveCustomer()
navigateToDashboard()
filterProducts()
```

Keep methods single-purpose.

---

# Observable Stream Naming

For observable variables:

* suffix with `$`

Examples:

```ts
users$
products$
isLoading$
selectedCustomer$
```

This immediately tells developers it is a stream.

---

# Subject Naming

For subjects:

* suffix with `Subject`

Examples:

```ts
destroySubject
refreshSubject
searchTriggerSubject
```

---

# Output Event Naming

For `@Output()` events:

* use action/event naming
* avoid UI-specific naming

Good:

```ts
saved
submitted
closed
selectionChanged
```

Avoid:

```ts
buttonClicked
submitButtonClicked
```

---

# Form Method Naming

Examples:

```ts
buildForm()
patchFormValues()
resetForm()
initializeForm()
populateFormData()
```

Avoid:

```ts
loadForm()
doForm()
```

---

# Initialization Methods

Use:

```ts
initializeData()
initializePage()
initializeSubscriptions()
initializeComponent()
```

Avoid:

```ts
start()
setup()
```

unless context is extremely clear.

---

# Subscription Methods

Examples:

```ts
subscribeToRouteChanges()
listenToSearchInput()
watchFormChanges()
```

This improves readability immediately.

---

# Cleanup Methods

Use:

```ts
unsubscribeSubscriptions()
disposeResources()
clearTimers()
```

---

# Avoid Prefix Noise

Avoid unnecessary prefixes:

```ts
fnLoadData()
methodSave()
btnClickHandler()
```

Modern Angular code should remain clean.

---

# Method Order Inside Component

Recommended order:

1. lifecycle hooks
2. public methods
3. event methods
4. API methods
5. helper methods
6. private methods

This improves maintainability.

---

# Angular Services Best Practices

### Service Class Method Naming Conventions

* Service method names must clearly describe the business action being performed.
* Use **camelCase**.
* Use **verb-first naming**.
* Keep names intention-revealing.
* Avoid generic names like:

  * `process()`
  * `execute()`
  * `run()`
  * `handle()`
  * `doWork()`

Prefer:

```ts
getCustomerById()
createCampaign()
updateTemplate()
deleteCoupon()
```

---

### Service Class Naming

Always suffix service classes with `Service`.

Examples:

```ts
CustomerService
CampaignService
AuthService
ProductService
PrintTemplateService
```

Avoid:

```ts
CustomerManager
CampaignHelper
ProductUtility
```

---

### Data Retrieval Methods

Use:

```ts
getCustomerById()
getOrderDetails()
getCurrentUser()
```

For collections:

```ts
getCustomers()
getProducts()
getCampaigns()
```

For API retrieval:

```ts
fetchCustomers()
fetchUserProfile()
fetchDashboardData()
```

Guideline:

* `get` → returns data
* `fetch` → retrieves data from API/server

---

### Create Operations

Use:

```ts
createCustomer()
createCampaign()
createTemplate()
createOrder()
```

Avoid:

```ts
saveNewCustomer()
insertCustomer()
```

unless project standards specifically require them.

---

# Update Operations

Use:

```ts
updateCustomer()
updateCampaign()
updateProfile()
```

Avoid:

```ts
modifyCustomer()
changeCustomer()
```

---

### Delete Operations

Use:

```ts
deleteCustomer()
deleteCampaign()
deleteTemplate()
```

Avoid:

```ts
removeCustomer()
eraseCustomer()
```

unless business terminology requires it.

---

### Search Operations

Use:

```ts
searchCustomers()
searchProducts()
searchCampaigns()
```

Avoid:

```ts
findData()
search()
```

which are too generic.

---

### Validation Methods

Use:

```ts
validateUser()
validateTemplate()
```

Boolean validations:

```ts
isAuthenticated()
hasAccess()
canEditTemplate()
```

Boolean methods should always start with:

* is
* has
* can

---

### Authentication Service Methods

Examples:

```ts
login()
logout()
refreshToken()
getAccessToken()
isAuthenticated()
```

Avoid:

```ts
doLogin()
performLogin()
executeLogout()
```

---

### Mapping / Transformation Methods

Use:

```ts
mapCustomerResponse()
mapOrderResponse()
buildRequestPayload()
transformApiData()
```

Avoid:

```ts
convert()
processData()
```

---

### Cache Methods

Use:

```ts
cacheCustomerData()
getCachedCustomer()
clearCache()
invalidateCache()
```

These names clearly communicate intent.

---

### Important Team Standards

* Service methods should represent business actions.
* Method names should be understandable without opening the implementation.
* Avoid abbreviations.
* Avoid generic method names.
* Use consistent CRUD naming:

  * `get`
  * `create`
  * `update`
  * `delete`
* Boolean methods should start with:

  * `is`
  * `has`
  * `can`
* Observable properties should end with `$`.
* Every method name should communicate its intent immediately.

#### Golden Rule

If another developer can understand what a service method does simply by reading its name, the naming convention is good.

























Important Principle
A developer should understand:

what the method does
when it is used
whether it returns boolean
whether it calls API
whether it changes UI state
just by reading the method name.















































# Avoid These

Avoid:

* Abbreviations
* Generic words
* Ambiguous names
* Technical noise

Bad Examples:

```ts
procData()
btnClick()
usrFn()
doStuff()
tempMethod()
```

---

# Recommended Practices

* One method = one responsibility.
* Keep method names business-oriented.
* Method names should be understandable without opening the implementation.
* Prefer readability over shorter names.
* Keep naming consistent across the project.
* Use domain-specific naming when possible.

Example:

```ts
approveLoanRequest()
generateInvoicePdf()
assignCampaignTemplate()
```

---

# Important Team Standards

* Use the same naming pattern across all components.
* Event methods should always start with `on`.
* Boolean methods should always start with `is`, `has`, or `can`.
* API methods should clearly indicate server interaction.
* Never use meaningless helper names.

Consistency improves:

* readability
* maintainability
* onboarding
* debugging
* code reviews
