---
layout: post
tags: web
---

A small post about how to design an authentification system. I will focus more on why things should be done than how technically they should be implemented, since once you understand the ins and outs you can adapt to almost any plateform.

## Why authentification

Before designing something you should understand accuratly why it is intended to do.
An authentification system is used between a service and a user (which can be an human or an other computer).
- Service an inside (public information) and an outside (private information)
- Authentification is the door between the inside and the outside
- Service can have a window making the inside visible from the outside without the outside being able to modify what is inside.

- Security system is a wall
    - Authentification is a door
        - Link between somone in particular outside and something inside

##

What is auth used for :

- Authentification is what make the difference between something public and private
- Protect data or service

How it is achieved :
- User pass credentials to the service
    - Either with a couple login / password
    - Or directly with a key that is used both to identify and validate the connection
        - Though, login / password are way more human friendly since it is easier to remember two words than a set of randoms gibrish
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

- Simplicity is key, the more complex a system is the more likely it is to have exploits
- Use existing protocols, frameowork and implementation.
    - Apes strong together : community find, community fix. Profit from experiences of others, whether it be from the past, present and future
    - No, creating your own encryption system for every request wont be more secure than HTTPS (it is a good exercice to learn how it is implemented though), Security through obscurity is the worst mind set you can have
    - Still good to do it to learn and have a solid grasp on how all of this work