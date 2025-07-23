## JwtRegisteredClaimNames
`JwtRegisteredClaimNames` is a class provided by the `System.IdentityModel.Tokens.Jwt` namespace in .NET. It contains constant string values that represent standard JWT (JSON Web Token) claim names as registered in the JWT specification (RFC 7519).

Here are the most commonly available properties in `JwtRegisteredClaimNames`:

| Property Name       | Claim Name           | Description                                                                 |
| ------------------- | -------------------- | --------------------------------------------------------------------------- |
| `Actort`            | `actort`             | The actor claim. Not part of the official spec, but sometimes used.         |
| `Aud`               | `aud`                | Audience — identifies the recipients that the JWT is intended for.          |
| `AuthTime`          | `auth_time`          | Time when the authentication occurred.                                      |
| `Azp`               | `azp`                | Authorized party — the party to which the ID token was issued.              |
| `Birthdate`         | `birthdate`          | End-user’s birthdate.                                                       |
| `CHash`             | `c_hash`             | Access token hash value.                                                    |
| `Email`             | `email`              | End-user’s email address.                                                   |
| `EmailVerified`     | `email_verified`     | Whether the email is verified.                                              |
| `Exp`               | `exp`                | Expiration time — when the token expires.                                   |
| `FamilyName`        | `family_name`        | Surname or last name.                                                       |
| `GivenName`         | `given_name`         | First name.                                                                 |
| `Iat`               | `iat`                | Issued at — the time the token was issued.                                  |
| `Iss`               | `iss`                | Issuer — who issued the JWT.                                                |
| `Jti`               | `jti`                | JWT ID — unique identifier for the JWT.                                     |
| `Name`              | `name`               | Full name of the end-user.                                                  |
| `Nbf`               | `nbf`                | Not before — identifies the time before which the JWT must not be accepted. |
| `Nonce`             | `nonce`              | A string to associate a client session with an ID token.                    |
| `PreferredUserName` | `preferred_username` | Shorthand name by which the end-user wants to be referred to.               |
| `Sub`               | `sub`                | Subject — identifies the principal that is the subject of the JWT.          |
| `Typ`               | `typ`                | Media type of the token.                                                    |
| `UniqueName`        | `unique_name`        | Unique name claim, sometimes used in ASP.NET.                               |
| `Website`           | `website`            | End-user’s website URL.                                                     |
| `Zoneinfo`          | `zoneinfo`           | Time zone.                                                                  |
