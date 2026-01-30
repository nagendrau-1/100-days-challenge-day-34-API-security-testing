# 100-days-challenge-day-34-API-security-testing

API security testing exposed broken authentication and authorization.

 API Security Testing – Practical VAPT Guide

A hands-on repository for learning and demonstrating **API Security Testing** techniques using real-world vulnerability assessment and penetration testing (VAPT) methodology. This project focuses on identifying, exploiting, and documenting security flaws in modern REST APIs based on the **OWASP API Security Top 10**.

 Objectives
 
Understand how APIs work in real-world applications (Web & Mobile)

 Identify common API vulnerabilities
 
 Perform manual and automated security testing
 
Build professional VAPT reports and proof-of-concept (PoC)

 Strengthen offensive and defensive API security skills

 Tools & Technologies
 
Burp Suite Community / Pro

Postman

OWASP ZAP

JWT.io

Nuclei

Kiterunner

cURL

Swagger / OpenAPI

 Testing Methodology

 1. API Recon & Discovery
    
 Identify API endpoints via
 
Browser DevTools (Network → XHR / Fetch)

Burp Suite Proxy & HTTP History

Swagger / OpenAPI Docs (`/swagger`, `/v3/api-docs`)

Enumerate:

Methods (GET, POST, PUT, DELETE)

   Headers
   
   Authentication tokens

 2. Authentication Testing
    
 Missing Authentication
 
 Broken Token Validation (JWT tampering)
 
 Expired Token Handling
 
Weak API Keys

 3. Authorization Testing (BOLA / IDOR)
    
Test access control by modifying object identifiers:

http

GET /api/users/1001

GET /api/users/1002

Check for unauthorized data access.

4. Input Validation & Injection
   
•	SQL Injection

•	Command Injection

•	XSS

•	JSON Parameter Tampering

5. Rate Limiting & Brute Force
   
•	High-frequency request testing

•	OTP & Login endpoint abuse

6. Mass Assignment
   
Inject unauthorized parameters:

{

  "username": "tester",
  
  "role": "admin",
  
  "isVerified": true
  
}

7. Business Logic Testing
   
•	Price manipulation

•	Coupon reuse

•	Skipping verification steps

•	Workflow bypass

OWASP API Security Top 10 Coverage

Vulnerability
  
1	Broken Object Level Authorization (BOLA)

2	Broken Authentication

3	Excessive Data Exposure

4	Lack of Rate Limiting

5	Mass Assignment

6	Security Misconfiguration

7	Server-Side Request Forgery (SSRF)

8	Injection

9	Improper Assets Management

10	Unsafe Consumption of APIs

Proof of Concept (PoC)

Each vulnerability includes:

•	Request & Response screenshots

•	Step-by-step reproduction

•	Impact analysis

•	Risk rating (Low / Medium / High / Critical)

•	Mitigation recommendations

 Sample Burp Workflow
 
1.	Capture API request via Proxy
   
2.	Send to Repeater
   
3.	Modify token / parameters
   
4.	Analyze server response
   
5.	Document findings
   
Learning Outcomes

•	Hands-on API penetration testing experience

•	Strong understanding of OWASP API Top 10

•	Professional security reporting skills

•	Real-world bug bounty & VAPT readiness

 Broken Authentication – API Security Testing Guide

**Broken Authentication** occurs when an application improperly implements authentication mechanisms, allowing attackers to:

 Access accounts without valid credentials
 
 Hijack active sessions
 
 Bypass login controls
 
 Escalate privileges

In APIs, this usually involves **JWT tokens, API keys, OAuth tokens, or session IDs**.

 Impact
 
Account takeover (ATO)

 Unauthorized data access
 
 Privilege escalation (user → admin)
 
 Compliance violations (GDPR, PCI-DSS, HIPAA)
 
 Common Causes
 
 Missing authentication on sensitive endpoints
 
 Weak password policies
 
 Predictable or reusable tokens
 
 Improper JWT validation
 
 Long token expiration times
 
 No rate limiting on login / OTP endpoints
 
 Insecure token storage (localStorage / hardcoded API keys)
 
 What to Test (Checklist)

 1 Missing Authentication
 
Test endpoints without tokens:

http

GET /api/profile

GET /api/orders

If response returns data →  Vulnerability

2️ Weak Login Mechanism

Test for:

•	Default credentials (admin:admin)

•	Password brute force

•	Credential stuffing

Use tools:

•	Burp Intruder

•	Hydra

•	FFUF

3️ JWT Token Tampering

Decode token using:

•	https://jwt.io

Modify:

•	user_id

•	role

•	isAdmin

Re-sign or remove signature and resend:

Authorization: Bearer <modified_token>

If accepted →  Critical issue

4️ Token Expiry Handling

Test with:

•	Expired tokens

•	Logged-out tokens

•	Reused tokens

If still valid →  Broken session management

5️ Session Fixation

•	Login

•	Capture token

•	Logout

•	Reuse same token

If access works →  Vulnerability

6️ OAuth Misconfiguration (If Applicable)

Test:

•	Missing state parameter

•	Open redirect in redirect_uri

•	Token reuse across apps

Practical Burp Suite Workflow

Step-by-Step

1.	Open Burp → Proxy → Intercept ON
   
2.	Login in application
   
3.	Capture API request
	
4.	Send to Repeater
	
5.	Remove or modify:
	
o	Authorization header

o	JWT payload

6.	Analyze server response
	
Risk Rating

Severity	Risk

Low	Info endpoints accessible

Medium	User data exposed

High	Account takeover

Critical	Admin access gained

Mitigation & Best Practices

•	Enforce authentication on all endpoints

•	Use strong password policy & MFA

•	Implement short-lived JWT tokens

•	Validate JWT signature & claims (iss, aud, exp)

•	Enable rate limiting & CAPTCHA

•	Secure token storage (HTTPOnly, Secure cookies)

•	Rotate API keys regularly

Sample VAPT Report Format

Title:

Broken Authentication in User Profile API

Endpoint:

GET /api/user/profile

Description:

The endpoint does not validate authentication tokens, allowing unauthorized access to user data.

Impact:

An attacker can access sensitive user information without logging in.

Proof of Concept:

1.	Send request without Authorization header
   
2.	Observe 200 OK response with user data
   
Severity:

High

Recommendation:

Implement token validation middleware on all protected endpoints.

Learning Outcome

•	Understand authentication flows in REST APIs

•	Perform JWT and session testing

•	Identify real-world account takeover risks

•	Write professional security reports

 Authorization Testing – BOLA / IDOR (API Security Guide)

**Authorization vulnerabilities** occur when an application fails to properly verify whether an authenticated user is allowed to access a specific resource or perform an action.

In APIs, this is commonly known as:  

BOLA (Broken Object Level Authorization)  

IDOR (Insecure Direct Object Reference)

This is the **#1 vulnerability in OWASP API Security Top 10** and often leads to massive data breaches.

 Impact
 
 Unauthorized access to other users’ data
 
 Account takeover via privilege escalation
 
 Data manipulation or deletion
 
 Compliance & legal risk
 
 Common Causes
 
 No ownership checks on object IDs
 
 Relying only on frontend validation
 
 Predictable identifiers (user_id, order_id, invoice_id)
 
 Missing role-based access control (RBAC)
 
 Hardcoded admin endpoints
 
 What to Test (Checklist)

1️⃣ Object ID Manipulation (BOLA / IDOR)
Test by changing IDs in requests:

http

GET /api/users/1001

GET /api/users/1002

GET /api/orders/5678

GET /api/orders/5679

If another user’s data is returned →  Critical Vulnerability

2️⃣ Horizontal Privilege Escalation

User A tries to access User B’s resources:

•	Profiles

•	Orders

•	Payments

•	Tickets

•	Messages

3️⃣ Vertical Privilege Escalation

Normal user tries admin functions:

POST /api/admin/create-user

DELETE /api/admin/delete-user

If successful →  Critical

4️⃣ Method-Based Authorization

Test different HTTP methods:

GET /api/users/1001

PUT /api/users/1001

DELETE /api/users/1001

Sometimes GET is protected, but PUT/DELETE is not.


5️⃣ Parameter-Based Authorization

Test role-based parameters:

{

  "user_id": "1001",
  
  "role": "admin"
  
}


 Practical Burp Suite Workflow
 
Step-by-Step

1.	Login as normal user
   
2.	Capture API request in Burp Proxy

3.	Send to Repeater

4.	Modify:

o	Object ID

o	HTTP Method

o	Endpoint path

5.Observe response

6.Document unauthorized access

 Risk Rating
 
Severity	Risk

Low	Limited data exposure

Medium	User data modification

High	Full account access

Critical	Admin-level access

 Mitigation & Best Practices
 
•	Enforce object-level authorization checks

•	Implement RBAC / ABAC

•	Use UUIDs instead of sequential IDs

•	Validate user ownership server-side

•	Apply access checks for every HTTP method

•	Log and monitor authorization failures

•	Conduct regular API security testing

 Sample VAPT Report Format
 
Title:

Broken Object Level Authorization in Order API

Endpoint:

GET /api/orders/{order_id}

Description:

The API does not validate ownership of the requested order, allowing any authenticated user to access other users’ order details.

Impact:

An attacker can view sensitive order and payment data of other users.

Proof of Concept:

1.	Login as User A
   
2.	Request /api/orders/1021

3.	Change to /api/orders/1022
	
4.	Receive User B’s order details
   
Severity:

Critical

Recommendation:

Implement server-side ownership validation for all object-level API endpoints.

#APISecurity

#BrokenAuthentication

#AuthorizationTesting 

#BOLA

#IDOR 

#CyberSecurity

#VAPT

#PenetrationTesting

#EthicalHacking 

#OWASP

#OWASPTop10

#BugBounty

#WebSecurity 

#InfoSec

#RedTeam

#BlueTeam 

#SecurityResearch

 #APIHacking 


 Academy:SKILLSUPRISE


 Mentor: Manojkumar Koravangi

