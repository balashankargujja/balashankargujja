
# JWT Claims Reading

ASP.NET Core automatically remaps JWT claim names when validating a token.

That means:
- You write `"sub"` → using `JwtRegisteredClaimNames.Sub`
- ASP.NET Core translates it to `"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier"`

So when you read claims later, "sub" no longer exists.

### 🧠 Full Explanation

#### ■ 1. What you wrote into the JWT
You wrote while setting claims:
```c#
new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString())

```
So inside the raw JWT, the payload looks like:
```c#
{
  "sub": "3",
  ...
}
```
Correct so far.

#### ■ 2. What ASP.NET Core does automatically (the important hidden step)

When the JWT middleware reads/validates the token, it maps standard JWT claim names to Microsoft-style claim types:
| JWT claim | Mapped to C# ClaimType      |
| --------- | --------------------------- |
| `"sub"`   | `ClaimTypes.NameIdentifier` |
| `"name"`  | `ClaimTypes.Name`           |
| `"email"` | `ClaimTypes.Email`          |

This behavior is built into:
```c#
JwtSecurityTokenHandler.DefaultInboundClaimTypeMap
```
Microsoft does this for backwards compatibility with ASP.NET Identity.

#### ■ 3. That means this happens internally
Your claim:
```c#
"sub": "3"
```
Gets transformed into:
```c#
"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "3"
```
It returns null because "sub" is already transformed.

### 🧪 Want Proof? (Real example)

Add this debug code:
```c#
foreach (var claim in principal.Claims)
{
    Console.WriteLine($"{claim.Type}: {claim.Value}");
}
```
You will see something like:
```c#
http://schemas.xmlsoap.org/.../nameidentifier: 3
http://schemas.xmlsoap.org/.../email: balu@example.com
http://schemas.xmlsoap.org/.../name: balu
jti: ...
```
Notice:

✔ there is NO "sub"

✔ user Id is under ClaimTypes.NameIdentifier

### 🎯 FINAL ANSWER
✔ Use JwtRegisteredClaimNames when creating the token

(so your JWT follows official JWT standards)

✔ Use ClaimTypes when reading the token

(because ASP.NET Core remaps and stores them under different keys)

Therefore:
```c#
// writing
new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString())

// reading (method 1)
principal.FindFirst(ClaimTypes.NameIdentifier)?.Value;
// reading (method 2)
User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
```

⭐ Best Practice (what all production apps use)

- `sub` = stored internally as `ClaimTypes.NameIdentifier`
- `name` = stored internally as `ClaimTypes.Name`
- `email` = stored internally as `ClaimTypes.Email`

### Simple example
```c#
[HttpPost]
[Authorize]
[Route("revoke")]
public IActionResult Revoke()
{
    // Extract the userId from JWT ("sub" → ClaimTypes.NameIdentifier)
    string userIdString = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;

    if (string.IsNullOrEmpty(userIdString))
        return BadRequest("User ID not found in token");

    int userId = int.Parse(userIdString);

    return Ok(userId);
}

```
