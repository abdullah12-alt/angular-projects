### **JWT (JSON Web Token) - A Complete Guide**

JWT (JSON Web Token) is a widely used authentication mechanism that securely transmits information between parties as a JSON object. It is commonly used for authentication and authorization in web applications.

---

## **1. What is a JWT?**
A **JWT (JSON Web Token)** is a compact, URL-safe token used to securely transfer data between two parties. It consists of three parts:
- **Header** (Algorithm & Token Type)
- **Payload** (Claims)
- **Signature** (Security)

Example of a JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

---

## **2. JWT Structure**
A JWT consists of **three base64-encoded parts**, separated by dots (`.`):

### **(1) Header**
The header contains the token type (`JWT`) and the signing algorithm used.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- `alg` (Algorithm) → Specifies the signing algorithm (e.g., HS256, RS256)
- `typ` (Type) → Defines the token type as JWT

---

### **(2) Payload (Claims)**
The payload contains **claims**, which are statements about the user or data.

Example payload:
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022,
  "role": "admin"
}
```
Common claims:
- **Registered Claims** (Predefined):  
  - `sub` (Subject, e.g., user ID)  
  - `iat` (Issued at, timestamp)  
  - `exp` (Expiration time)  
  - `iss` (Issuer)  
  - `aud` (Audience)  
- **Custom Claims** (Application-specific, e.g., `role`, `email`)

---

### **(3) Signature**
The signature ensures the token is not altered. It is created using the header, payload, and a secret key.

Signature formula:
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

---

## **3. How JWT Works?**
JWT is commonly used in **stateless authentication**. Here’s how it works:

### **Step 1: User Logs In**
- The client sends a login request (username/password).
- The server verifies the credentials.
- If valid, the server generates a JWT and sends it to the client.

### **Step 2: Client Stores Token**
- The client (browser, mobile app) stores the token (e.g., in `localStorage` or `sessionStorage`).

### **Step 3: Token Sent with Requests**
- The client includes the JWT in every request **(usually in the HTTP `Authorization` header)**:
  ```
  Authorization: Bearer <JWT>
  ```

### **Step 4: Server Verifies Token**
- The server extracts the token, verifies its signature, and extracts the payload.
- If valid, the request proceeds.
- If invalid/expired, the request is denied.



## **5. Implementing JWT in ASP.NET Core**
### **Installation**
Install `Microsoft.AspNetCore.Authentication.JwtBearer`:
```powershell
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### **Configuring JWT Authentication**
Modify `Program.cs`:
```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

var key = Encoding.ASCII.GetBytes("my_secret_key"); // Secret Key

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    });

var app = builder.Build();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

---

## **6. When to Use JWT?**
✅ **Good for:**
- Stateless authentication (e.g., REST APIs)
- Single Sign-On (SSO)
- Securely transferring data

❌ **Not ideal for:**
- Sessions that need to be revoked immediately (JWT is self-contained)
- Storing sensitive user data (tokens are readable)

---

## **7. JWT vs Session-based Authentication**
| Feature | JWT (Stateless) | Session (Stateful) |
|---------|---------------|----------------|
| Storage | Client-side | Server-side |
| Scalability | ✅ (No need for centralized session storage) | ❌ (Requires session storage) |
| Security | ✅ Secure if signed properly | ✅ More control over sessions |
| Revocation | ❌ Hard to revoke | ✅ Easy to revoke |

---

## **8. Best Practices**
✔️ Use **short expiry times** for access tokens  
✔️ Store JWTs **securely** (e.g., HTTP-only cookies, not local storage)  
✔️ Use **refresh tokens** to get new access tokens  
✔️ **Do not store sensitive data** inside JWTs  
✔️ Prefer **asymmetric signing (RS256)** for extra security  

---

## **Conclusion**
- JWT is a **compact**, **self-contained** token used for authentication.
- It is commonly used in **REST APIs** and **modern web applications**.
- **Security considerations** (e.g., token storage, revocation) are important when implementing JWT.

