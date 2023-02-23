---
layout: post
tags: web
---

What is auth used for :

- Authentification is what make the difference between something public and private
- Protect data or service

How it is achieved :
- User pass credentials to the service
    - Either with an login / password
    - Or directly with a key that is used both to identify and validate the connection
        - Though, login / password are way more human friendly since it is easier remember two words than a set of randoms gibrish
- Service check that the credentials are valid
    - Can be done various way (stored credentials on the server side, digital signatures, ...)

Let's make it a little bit more complicated :
- The less the user credentials are shared the better
- Provide a token / key / value that will be used as a temporary credential
- All the auth system are based on that, what vary is :
    - Service side :
        - How you create this token
        - How you check the validity of this token
        - How you invalidate the token (once the lifetime is finish or then the user logout)
    - Client side :
        - How do you store the token
        - How do you pass it to the service

Differents types of risks :
- Interception : someone can heard the discusion, worse case scenary he can modify it
    - That's why you use a token, at most you lose a temporary credential
    - HTTPS
- Breach of service : someone have access to the data of the service
    - Hash : minimize impact on client
- Breach of client : someone have access to the data of the client
    - Double factor authentification (2FA)
    - Most tech are mature, main opening is the user

Auth should not be too hard to use or it will either :
    - Lead users to bypass the security making it useless
    - Users will stop using the service, simply because it is not convenient (unless they have to)