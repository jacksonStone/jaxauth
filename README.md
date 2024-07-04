# jaxauth
simple password login for an app

## Example
Given some DB with a user record that has a "salt" and "hashedPassword" field:

```typescript
import { JaxAuth, JaxAuthBuilder } from "./JaxAuth";
import { getUserById } from "./dbMethods";
import { User } from "./types";

const auth: JaxAuth<User> = new JaxAuthBuilder<User>()
    .setUserGetter((getUserById))
    .setUserPasswordSaltField(user => user.salt)
    .setUserHashPasswordField(user => user.hashedPassword)
    .setHMACKey(process.env.HMAC_KEY as string)
    .setEncryptionSecret(process.env.ENCRYPTION_KEY as string)
    .setStringifiedCookieContents(user => JSON.stringify({userId: user.userId, userEmail: user.userEmail}))
    .setCookieName('exampleauth')
    .build();

export default auth;
```
Additionally, in the above example ENCRYPTION_KEY needs to be a 16byte string (length 16, utf-8), and HMAC_KEY needs a 32 byte string (length 32, utf-8)

An example of creating the hashedPassword and salt during initial user creation

```typescript
const password = "somePlainTextPassword";
const salt = JaxAuth.generateSalt();
const hashedPassword = JaxAuth.hashPassword(password, salt);
// Now you can persist the salt and hashedPassword fields into the user record.
```
