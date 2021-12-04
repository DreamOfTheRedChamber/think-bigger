# Microsoft Teams

- [Microsoft Teams](#microsoft-teams)
  - [Tell me about yourself](#tell-me-about-yourself)
    - [Tips](#tips)
      - [Bonus points](#bonus-points)
        - [Why this company and position are good matches](#why-this-company-and-position-are-good-matches)
    - [My answer](#my-answer)
  - [A success project](#a-success-project)
    - [Tips](#tips-1)
      - [Goal](#goal)
      - [STAR method](#star-method)
      - [Go to the details](#go-to-the-details)
    - [My answer](#my-answer-1)
  - [A failure experience](#a-failure-experience)
    - [Tips](#tips-2)
    - [My answer](#my-answer-2)
  - [Conflict management](#conflict-management)
    - [Goal](#goal-1)
    - [Patterns](#patterns)
    - [My answer](#my-answer-3)
  - [Leadership](#leadership)
  - [Ownership / Responsibility](#ownership--responsibility)
- [Project deepdive](#project-deepdive)
  - [Token acquisition library migration](#token-acquisition-library-migration)
- [References](#references)

## Tell me about yourself
### Tips
1. Assume: Interviewer has already read resume. 
2. Overview: Appreciate and learn from the current position.
3. Limitations of the current position, naturally leads to why new and old role matches. 

#### Bonus points
##### Why this company and position are good matches
* Heart: Industry, project, customer
* Impact: Scope
* Money (could think out of the box but better obey the interview rule and not talk about)

### My answer
* I have been working in Microsoft Teams for the past 4.5 years. Just to give you a bit more context, Microsoft Teams is a group chat SaaS application for Office products.
* Within this position, I had worked as service owners, authentication specialist and SDK designers. I learnt how to drive things E2E and collaborate across teams / orgs. 
* I want to work closer to products and cutting edge technologies. This position within Doordash could provide me with such opportunities. 

## A success project
### Tips
#### Goal
* Behind the scene the interviewer wants to understand the scope and complexity of the project. 
* Similar to performance review / promotion discussion.

#### STAR method
* Situation/Task: Describe the situation you were in or the task that you needed to accomplish. You must describe a specific event or situation, not a generalized description of what you have done in the past.
* Action: Describe the action you took and be sure to keep the focus on you. Even if you are discussing a group project or effort, describe what you did.
* Result: What

#### Go to the details
* Interviewer wants to see What methodology and logic you use to make decisions. Understand on why and what.

### My answer
* Situation: One of the vertical markets that have not adopted SaaS products is financial industry due to its high compliance requirements and volume.  In order to sell M365 products to the financial industry, Teams product was chosen as the pioneer to implement a new type of authentication token. 
* Task: As the critical service within Teams product, we were the first service to uncover this area and lead all designs. We worked with auth SDK owners within microsoft to design and refine the protocol. We also worked with different client teams (web, mobile, desktop, etc.) to guarantee all types of auth scenarios are supported. The challenge of this products is more on balancing quality and speed. 
  * On one hand, since our service is a pioneer service, we want to do this in a way which could establish reusable patterns and SDKs for all client-facing services within Teams. 
  * On the other hand, since we are the first adopter, we want to balance the speed and quality so that we could test the performance on scale quickly 
* Action: 
  * First we had a weekly cadence meeting including all stakeholders to guarantee that the all important concerns could be raised in time. And all the design tradeoffs we made or corners we cut are documented well for comments and reviews. 
  * When we implement this function, we implemented all functionalities in a dedicated middleware where all services could easily plug into their request handling pipeline. 
* Result: 
  * We delivered this feature within the given timeline, implemented it in a way easy for other teams to adopt, and have all the tradeoffs documented really well for future review and adoption. 

## A failure experience
### Tips
* Template:
  * Not perfect in the beginning. Cannot foresee the risk and dependencies of the project. 
  * In the end, 
* Failure redflag:
  * Failures don't conflict with core values.

### My answer
* 

## Conflict management
### Goal
* Scope of the
* Empathy
* Core values

### Patterns
* Possible scenarios
  * Technology:
    * Tradeoffs 
  * Background and perspectives: 
    * With PM
* Process:
  * Recognize the differences
  * Learn the differences
  * Understand the culture
    * e.g. Amazon Right a lot
    * e.g. People, flexible
  * Possible solutions
    * Convince
    * Adopt the suggestion
* Redflag:
  * Avoid conflict
  * Too aggressive
  * Too concerned about your own needs 
  * Emotional

### My answer
* Situation: Our team is building a org level reusable shared libraries. While I work on the library migration project, I propose that we put the reusable components inside shared libraries so all other teams could reuse it. However, my manager said that there were not much resource, and would prefer not to invest too much into this project. 
* Action: 
  * I first synced with my manager / PM to make sure that I understood their priorities in the coming quarter, and who are the stakeholders for these incoming features. 
  * Then I held a discussion for partner teams who will probably use my package if I did it in a reusable way, and understand how much they will benefit from this. 
  * Next I hold a meeting with my manager and PM, demonstrating how much more influence we could make if we could make if we switch one project in the coming quarter. 

## Leadership
* Process
  * Influence without authority
  * Firefighter 
  * Manage up
  * Manage peer
* Result: Gain the team win and get trust from peers. 
* Provide multiple example and ask the interviewer which one he is interested. 

## Ownership / Responsibility
* Two core points:
  * Volunteer
  * Originally out side of your plate
* Add more details when describing it:
  * Evaluating the resources, prioritizing work, and communicating with partners. 
* Red flag
  * Not assigned to you, wait for directions
  * Hesitated to take the action

# Project deepdive
## Token acquisition library migration

# References
* https://medium.com/@chamod.14_80003/token-caching-wso2-api-manager-5c5b3d6ddd09
* https://www.pingidentity.com/en/company/blog/posts/2021/ultimate-guide-token-based-authentication.html

* [Protecting Server Resources Hosting Unauthenticated APIs](https://medium.com/@robert.broeckelmann/protecting-server-resources-hosting-unauthenticated-apis-d77875db7b8)
* [Who Owns API Security, and How Much Security Is Enough?](https://medium.com/@robert.broeckelmann/nissan-leaf-api-security-who-owns-api-security-and-how-much-security-is-enough-fa467fdb59a1)
* [DSig Part 1: XML Digital Signature and WS-Security Integrity](https://medium.com/@robert.broeckelmann/dsig-part-1-xml-digital-signature-and-ws-security-integrity-225ea3eb894e)
* [DSig Part 2: JSON Web Signature (JWS)](https://medium.com/@robert.broeckelmann/dsig-part-2-json-web-signature-jws-f428d0b5ae40)
* [DSig Part 3: XML DSig vs. JSON Web Signature](https://medium.com/@robert.broeckelmann/dsig-part-3-xml-dsig-vs-json-web-signature-709345c78541)
* [API Security vs. Web Application Security Part 1: A Brief History of Web Application Architecture](https://medium.com/@robert.broeckelmann/api-security-vs-web-application-security-part-1-a-brief-history-of-web-application-architecture-4c8a682a21e)
* [API Security vs. Web Application Security: Part 2](https://medium.com/@robert.broeckelmann/api-security-vs-web-application-security-part-2-e2f327b4b54c)
* [SAML 2.0 VS. JWT: UNDERSTANDING FEDERATED IDENTITY AND SAML](https://medium.com/@robert.broeckelmann/saml-2-0-vs-jwt-understanding-federated-identity-and-saml-a259dff8545c)
* [Delegation — A General Discussion](https://medium.com/@robert.broeckelmann/delegation-a-general-discussion-d0b2ab5a85c7)
* [OAuth2 Access Tokens vs API Keys — Using JWTs](https://medium.com/@robert.broeckelmann/oauth2-access-tokens-vs-api-keys-using-jwts-651f97df9e19)
* [OAuth2 Access Tokens and Multiple Resources Series](https://medium.com/@robert.broeckelmann/oauth2-access-tokens-and-multiple-resources-series-13e467861893)
* [Authorization Series](https://medium.com/@robert.broeckelmann/authorization-series-6b9c5890716c)
* [Active Directory Federation Services (ADFS) and Kerberos](https://medium.com/@robert.broeckelmann/active-directory-federation-services-adfs-and-kerberos-f36c71e13be5)
* [The Benefits of JWTs as OAuth2 Access Tokens](https://medium.com/@robert.broeckelmann/the-benefits-of-jwts-as-oauth2-access-tokens-6ec47dbd2783)
* [IDENTIVERSE REFLECTIONS: NEWS, TRENDS AND A GLIMPSE INTO THE FUTURE](https://medium.com/@robert.broeckelmann/identiverse-reflections-news-trends-and-a-glimpse-into-the-future-d585050b7cf5?readmore=1&source=user_profile---------20-------------------------------)
* [The Many Ways of Approaching Identity Architecture](https://medium.com/@robert.broeckelmann/the-many-ways-of-approaching-identity-architecture-813118077d8a)
* [A Brief Summary of All Things Apigee and API Management that I Have Written](https://medium.com/@robert.broeckelmann/a-brief-summary-of-all-things-apigee-and-api-management-that-i-have-written-46bb71c2d8b9)
* [Authentication vs. Federation vs. SSO](https://medium.com/@robert.broeckelmann/authentication-vs-federation-vs-sso-9586b06b1380)
* [What is Authorization?](https://medium.com/@robert.broeckelmann/what-is-authorization-9977caacc61e)
* [SAML2 vs JWT: A Comparison](https://medium.com/@robert.broeckelmann/saml2-vs-jwt-a-comparison-254bafd98e6)
* [How To Submit Your Security Tokens to an API Provider Pt. 1](https://medium.com/@robert.broeckelmann/how-to-submit-your-security-tokens-to-an-api-provider-pt-1-4a68df35843a)
* [JWT Use Cases](https://medium.com/@robert.broeckelmann/jwt-use-cases-bb94e4e70949)
* [Application Security Models](https://medium.com/@robert.broeckelmann/application-security-models-e5b47fe6ac70)
* [Identity Propagation in an API Gateway Architecture](https://medium.com/@robert.broeckelmann/identity-propagation-in-an-api-gateway-architecture-c0f9bbe9273b)
* [An Alternative to Delegated Access in the Enterprise](https://medium.com/@robert.broeckelmann/an-alternative-to-delegated-access-in-the-enterprise-82023ed423b5?source=user_profile---------63-------------------------------)
* [SAML2 vs JWT: Apigee & Azure Active Directory Integration — A JWT Story](https://medium.com/levvel-consulting/saml2-vs-jwt-apigee-azure-active-directory-integration-a-jwt-story-a3eb00769a1f)
* [Keeping Your APIs Secure for Multiple User Types](https://medium.com/@robert.broeckelmann/keeping-your-apis-secure-for-multiple-user-types-d5c627793c4c)
* [Sample: WSO2 EI Cache Mediator based Token Caching](https://medium.com/@chamilad/sample-wso2-ei-cache-mediator-based-token-caching-3036f2e7e6eb)
