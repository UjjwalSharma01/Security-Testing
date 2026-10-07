## Authenticaton - What it is?

Authentication is the process of verifying the identity of a user or client

### Types of Authentication
1 . Knowledge Factors - This is something you __know__ - passwords
2. Posession Factors - Something Physical you __have__ - Security token
3. Inherance Factor  - Something you __are__ -  biometrics
We can use multiple technologies to verify these factors

## Difference Between Authentication and Authorisation 

Authentication verifies the user who they claim to be whereas authorization talks about what they are allowed to do or the permissions they have


##  How do authentication vulnerabilities arise?


There are two main reasons they arise
**1. Weak against brute force: the door works, but you can try keys forever**

The authentication system works as designed. The attacker still has to _get past_ it by supplying valid credentials. The weakness is that nothing stops them from guessing repeatedly. Examples:

-   No rate limiting or account lockout, so they can try 100,000 passwords
-   Weak password policy, so "password123" is allowed
-   Username enumeration (different error messages for "user doesn't exist" vs. "wrong password"), which lets them narrow down valid usernames first

So the attacker is still going _through_ the login, just with guesswork until something works.

**2. Broken authentication (logic flaws): the door has a bug, so you walk around it**

Here the attacker doesn't need valid credentials at all. A coding or logic mistake lets them skip or trick the check. Examples:

-   **Skipping a step:** a site asks for password, then a 2FA code on a second page. If you can go straight to `/account` after the password step without ever entering the code, 2FA is bypassed.
-   **Trusting user-controlled data:** the server relies on a cookie or parameter like `role=user` or `verified=true`, and you just change it.
-   **Flawed password reset:** the reset link checks nothing about _whose_ account it is, so you change the username in the request and reset someone else's password.
-   **Weird edge cases:** for example, the server accepts an empty or deleted-parameter value and treats it as valid.



<img width="958" height="404" alt="image" src="https://github.com/user-attachments/assets/8a3aa719-721d-49bc-992f-321db62b26dd" />
