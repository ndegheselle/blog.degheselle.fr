---
layout: post
tags: web
---

A small post about how to design an authentication system. I will focus more on why things should be done than how technically they should be implemented, since once you understand the ins and outs you can adapt to almost any platform.

## What ?

Before designing something you should understand accurately what is the need filled by authentication.

An authentication system is used between a service (inside) and users (that are outside). The user is not just anyone but someone that has been granted access to the service, which mean that you need a way to identify a genuine user from anyone else coming from outside. To do this you create a wall, a security system, between the outside (the users) and the inside (the service). An authentication system is a part of this wall, acting like a door for peoples having the key.

## How to setup authentification

Let's look at how most auth systems handles identification.

- The user sends credentials to identify himself
    - Either with a couple login / password
    - Or directly with a key that is used both to identify and validate the connection (most of the time used for machine to machine communication)
- The service checks these credentials
    - Most of the time by comparing it to database values
- The service sends back data to the user

> Credentials can take other forms, but most of the time login /password is used

And that's it ! But obviously there is more than that, but to understand the specific you have to understand the different types of risks for the users credentials : 

<table>
  <thead>
    <tr>
      <th>Attack Type</th>
      <th>Description</th>
      <th>Impact</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Interception</td>
      <td>An outsider is between the user / service and can access everything sent between the two</td>
      <td>Intercept user credentials</td>
    </tr>
    <tr>
      <td>Breach of client</td>
      <td>An outsider has access to the data of the client, impacting only this user</td>
      <td>Obtain user credentials</td>
    </tr>
    <tr>
      <td>Breach of service</td>
      <td>An outsider has access to the data of the service, impacting all users</td>
      <td>Obtain all users' credentials</td>
    </tr>
  </tbody>
</table>

To reduce the impact of *Interception* you have to send the credentials the least possible, for *Breach of client* you have to make sure the client doesn't store his credentials.

You can achieve both of that with a token, the first time the user authenticates you send him back a token that will be used as a replacement credential. With this even if an outsider gets the token by intercepting it or getting it directly from the client he won't have access to the real user credentials (which reduce the impact of the leak).

Most of the modern auth systems are based on that token concept (OAuth2, JWT, session, ...), what vary is :
- Service side :
    - How you create this token
    - How you check the validity of this token
    - How you invalidate the token (once the lifetime is finished or then the user logout)
- Client side :
    - How do you store the token
    - How do you pass it to the service

> Use HTTPS.

For *Breach of service*, what to do to reduce the impact :
- Hash password stored on the service side
- Setup efficient log, alert and save system
- Be ready to forget sleep

## What you should look out for

Let's finish some points that you should keep in mind then choosing and designing an authentication system :

**Auth is only a part of a security system** : A door is only as good as the wall or windows, same for the authentication. Before worrying about authentication you have to be sure that the security system of the server hosting your service is up to date and suitable.

**Make it easy for the users** : Auth should not be too hard to use or it will either lead users to bypass the security making it useless or users will stop using the service, simply because it is not convenient (unless they have to).

**Keep it simple and suitable**: Not all doors are born equals and they don't need to, authentication is one of the things you absolutely don't want to be overengineered, simplicity is key, the more complex a system is, the more likely it is to have exploits.

**Don't do it, it's already done** : Use existing protocols, framework and implementation. Apes strong together : community find, community fix. Profit from experiences of others, whether it be from the past, present and future. No, creating your own encryption system for every request won't be more secure than HTTPS (it is a good exercise to learn how it is implemented though), security through obscurity is the worst mindset you can have.