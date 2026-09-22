# Week 10 — Filter Enforcement Writeup

## 1. The Application-Layer Filter

The first solution used an application-layer filter to separate data between PayGuard tenants.

The application receives the user's session and gets the `tenant_id`. It then performs a vector database search and filters the results so that only documents matching that tenant are returned.

The approach looks correct because the user should only see documents belonging to their own company.

The normal flow is:

User → Application → Filter → Vector Database

The application checks the tenant before showing the retrieved results. At first glance, this appears to provide tenant isolation.

## 2. When the Filter Fails

The problem is that the filter exists only inside the application.

The live PayGuard demo showed what happens when someone does not use the normal application path. The attacker can call the vector database directly instead of sending the request through the application.

This changes the attack path to:

Attacker → Vector Database

The application-layer filter is never reached.

There is another problem with this approach. The database retrieves records before the application performs its filtering. This means data belonging to other tenants has already been retrieved even if it is later removed from the response.

For a FinTech system, retrieving unauthorised data is itself a security concern.

## 3. How the Bypass Works

In the live demonstration, the attacker performed a direct vector database search using a query such as:

`outstanding invoice balance`

Instead of going through the application's tenant filter, the attacker accessed the shared vector store directly.

The result contained records belonging to multiple tenants, including different companies and their invoice information.

This demonstrated that the application filter was not a complete security boundary.

The important lesson from the demo was that a security control can look correct while still being bypassable when it is implemented at the wrong layer.

## 4. The Database-Layer Fix

The stronger solution moves tenant enforcement into the database query itself.

Instead of retrieving records for all tenants and filtering them afterward, the vector database query includes the authenticated `tenant_id`.

Conceptually, the secure flow becomes:

User Request
→ Vector Database
→ Tenant ID enforced in query
→ Only matching tenant records returned

The database therefore becomes responsible for enforcing the data boundary.

A query can apply a condition equivalent to:

`tenant_id = authenticated_tenant_id`

This means records belonging to another tenant are excluded at the database retrieval layer rather than being retrieved first and filtered later.

## 5. Why the Bypass No Longer Works

The database-layer fix works because the security control is now located where the data is stored and retrieved.

With the original application-layer filter, an attacker could avoid the control by avoiding the application.

With the database-layer control, bypassing the application does not remove the tenant restriction.

The attacker may still attempt the same direct database query, but the database will only return records belonging to the authenticated tenant.

This makes tenant isolation an enforced data-access rule rather than an application assumption.

## 6. An Analogy

I think of the application filter like a security guard at the entrance of a building.

The guard checks your ID and allows you into the correct room. That sounds secure, but if there is another unlocked door directly into the storage room, someone can simply bypass the guard.

The database-layer control is like putting the lock on the storage room itself. Even if someone gets around the security guard, the storage room still checks who is allowed to access what is inside.

That is the difference between relying only on the application layer and enforcing the security boundary where the data actually lives.

## 7. My Takeaway

The biggest lesson from the live demo was that a security control is only effective if it is enforced at a layer that cannot simply be bypassed.

The application filter looked like the right fix because it checked the tenant ID. However, seeing the direct database bypass made the weakness clear.

For systems containing sensitive financial or customer data, authorization should not depend only on application code. The database should also enforce the boundary around the data.

The PayGuard demo showed me why defence in depth matters: even if the application layer is bypassed, the database should still protect the data.
