
# Race conditions

Race conditions are a common type of vulnerability closely related to business logic flaws. They occur when websites process requests concurrently without adequate safeguards. This can lead to multiple distinct threads interacting with the same data at the same time, resulting in a "collision" that causes unintended behavior in the application. A race condition attack uses carefully timed requests to cause intentional collisions and exploit this unintended behavior for malicious purposes.

## What is a “temporary sub-state”?

A **temporary sub-state** is a short-lived condition the application enters **while handling a request**, before it finishes all the necessary updates.

In this case:

- The application starts processing your request
    
- It _has not yet_ marked the discount code as “used”
    
- The request is still “in progress”
    

During this moment, the system believes:

> “This code has NOT been used yet”


## The sequence of events (important)

Here’s what the server does internally:

1. **Request arrives**  
    You submit a discount code.
    
2. **Validation happens**  
    The server checks:
    
    > “Is this discount code already used?”
    
    - Database says: ❌ Not used
        
3. **Temporary sub-state begins**
    
    - The server accepts the discount
        
    - **But the database is NOT updated yet**
        
4. **Database update happens later**
    
    - Server marks the code as “used”
        
    - Sub-state ends
        

 That gap between steps **3 and 4** is the **race window**

## What is the “race window”?

The **race window** is a very small time gap where:

- The discount code is **still valid**
    
- The system **has not yet recorded** that it was used
    

If you send **multiple requests at the same time**, they can all:

- Pass the “is this used?” check
    
- Get accepted **before the database update happens**
    

Result:

>  You redeem the same discount **multiple times**

------------------------

## What are “hidden multi-step sequences”?

From the outside, you see **one HTTP request**:

`POST /login`

But **inside the server**, that one request triggers **multiple internal steps**, for example:

1. Verify username/password
    
2. Create a session
    
3. Set user ID in session
    
4. Check if MFA is enabled
    
5. Generate MFA code
    
6. Redirect to MFA page
    

These steps happen **sequentially** on the server, but **before the request finishes**.

Each step creates a **temporary internal state**.

---

## 2️⃣ What is a “sub-state”?

A **sub-state** is:

> A short-lived internal condition that exists **only during request processing**.

In this example, one dangerous sub-state is:

> “User is logged in, but MFA is not yet enforced”

This state:

- Exists for **milliseconds**
    
- Is never visible in the UI
    
- But **does exist in server memory**
    

---

> **Admin access is gained by sending a privileged request during a tiny moment where the server believes you are logged in but has not yet enforced MFA.**

That moment exists **inside the same login request**.

---

## 1️⃣ What the server _thinks_ login looks like

From the developer’s perspective:

`User logs in → Password verified → MFA required → User must pass MFA → THEN admin access is allowed`

They assume this flow is **strict and linear**.

---

## 2️⃣ What actually happens internally (real flow)

Inside the server, the login request does this:

`STEP 1: session['userid'] = admin_userid   ✅ STEP 2: check if MFA is enabled STEP 3: set session['enforce_mfa'] = true STEP 4: redirect to MFA page`

🚨 **Between STEP 1 and STEP 3**, the user:

- **IS authenticated**
    
- **IS NOT blocked by MFA yet**
    

This is the **dangerous sub-state**.

---

## 3️⃣ Why admin endpoints trust you during this moment

Most admin endpoints check **only one thing**:

`if session['userid'] exists:     allow access`

They **do NOT check MFA again**, because developers assume:

> “MFA is already enforced by login.”

That assumption is wrong.

---

## 4️⃣ The exact attack sequence (timeline)

### ⏱️ Timeline view

`t0  ── Login request starts t1  ── session['userid'] set   ← 🔥 YOU ARE ADMIN NOW t2  ── MFA enforcement not active yet t3  ── MFA flag set t4  ── Redirect to MFA page`

You attack **between t1 and t2**.

---

## 5️⃣ What the attacker sends (important)

The attacker sends **two requests at the same time**:

### 🔹 Request A — Login

`POST /login username=admin password=correct`

### 🔹 Request B — Admin endpoint

`GET /admin Cookie: session=<same session>`