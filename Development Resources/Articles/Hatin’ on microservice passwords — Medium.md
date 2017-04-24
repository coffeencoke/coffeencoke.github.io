# Hatin’ on microservice passwords — Medium

by Justin Smith, medium.com

Hatin’ on microservice passwords

Lots has been written about the awfulness of usernames and passwords. I hope at some point in my lifetime there’s an alternative technology that allows humans to log in to apps and web sites. My former employer Microsoft may be onto something (hopefully it works for mobile). Twitter might be as well. I’m not going to re-open those discussions.

Instead I’m focusing this article on the awfulness of passwords in server-to-server authentication. The relevant scenario is simple:

an application (client) makes a request to another application (service).
This scenario is near and dear to my heart and one that I’ve worked on at Microsoft, Google, and a little bit at Apcera. It’s sure to be a dominant theme now that I’m at Pivotal.

This simple pattern that has been around longer than I have, and it’s central to the recent movement towards microservice architectures. After all, if you are exposing functionality as small, lightweight services, you’ll need to consider an authentication scheme. My hope is that this post serves as a small beacon pointing to what I believe is a solid approach to microservice authentication.

Disclaimer

I assert that it’s generally a good thing if services require clients to authenticate before clients can consume service resources. There’s an argument to be made that some datacenter topologies have segmented their networks such that there’s no reason for clients to authenticate. I’m generally not a fan of this approach because over the years I’ve come to believe there is untrusted code running “in the perimeter”. If you believe that bigger walls (e.g. the Maginot line) are enough to keep your apps and data safe, then there’s no need to read on. Best of luck to you.

Temptation

At first glance it’s tempting to solve the microservice authentication problem by requiring clients to authenticate directly with a service using a username and a password. This is really just a shared secret because both the client and the service need to know it.

This model is easy to understand, easy to code, and easy to test. A mystical force simply bestows the service and the client with the secret and from there the client simply passes the secret to the service on every request.

Piece of cake. Done and dusted. Plenty of server-side software uses this approach to authentication: MySQL, Postgresql, CouchDB, NATs, and plenty more do it. It should be good enough for microservices.

However:

Shared secrets are a nightmare to operate and maintain, especially when used for direct client-to-service authentication.
The challenge is less about how a client authenticates, but more about where a client authenticates. Before I explain, let me first tell a story.

Some relevant history

While I was at Microsoft (roughly 2007-ish) I got to work on what became known as the Windows Azure Active Directory Access Control Service (ACS for short☺). It’s a mouthful I know — Microsoft likes long names for some reason. In that time I had the good fortune of working with the likes of John Shewchuk, Dick Hardt, Hervey Wilson, and Yaron Goland. These guys taught me a ton. I hope I don’t bungle the story too badly. I apologize if I do.

John was the executive sponsor for ACS. The ACS charter was to provide (1) Single Sign On (SSO) for websites, (2) authentication for web services, and (3) simple authorization for both websites and web services.

To tackle SSO we basically had to implement SAML, WS-Federation, and OpenID. It was fiddly, but very doable.

Authentication for web services was another matter entirely. At the time SOAP was still around. Maybe it still is; I simply don’t see it anymore. It was clear, however, that REST was the new hotness.

For SOAP web services we implemented the WS-Trust protocol. The flow was simple: (1) client sends credentials to ACS, (2) ACS validates credentials and issues a token, (3) client sends token and payload to service, (4) service validates token then performs work. There were a number of permutations within the WS-Trust protocol, but that’s the gist. It was fiddly, but very doable.

REST was another matter entirely. There simply wasn’t a great model for REST authentication. There was OAuth1, but the required message signing was a PITA to code and debug. There was mutually-authenticated SSL, but the required key distribution seemed too hard, especially at scale. There was Basic Auth (i.e. username and password), but that didn’t seem good either (more on that later in the article). It looked like we had to invent.

I can’t remember who came up with the idea (it was probably either John or Yaron — apologies if I’m wrong), but we decided to collaborate with others. This was not the normal behavior at the time at Microsoft. Anyway, our team reached out to Google, Yahoo!, and others to see if they were interested in working on the problem together (Facebook eventually got really involved). To everyone’s astonishment, everyone seemed convinced that the situation required invention. As a result, Dick, Yaron, Brian Eaton from Google, and Allen Tom from Yahoo! worked on a specification that came to be known as Web Resource Authorization Protocol (WRAP). WRAP had a profile for autonomous server-to-server interactions, and the ACS team implemented it.

The autonomous flow was simple: (1) client sends credentials to ACS, (2) ACS validates credentials and issues a token, (3) client sends token and payload to service, (4) service validates token then performs work. The data flow looked identical to the WS-Trust interaction, but it was RESTFul and mandated SSL.

WRAP evolved into what’s now known as OAuth2.
Why all the machinery?

The WRAP data flow is more complex than simply sharing secrets. However, sharing secrets between the client and the service has a ton of disadvantages. Here are a few, some may not be obvious:

Credential exposure

In the simplest flow, a client sends a secret on every request to the service. This means that the secret is continually being sent across the network. In other words, the credentials are exposed while in-flight to the service. Exposure should be minimized. At a minimum one should use TLS to shield the credentials from promiscuous code on the network.

It’s far better to pass a token that has a limited lifetime than it is to send a secret on every request
Difficult provisioning

This may seem obvious, but if every instance of the service must know every client secret, then provisioning new clients requires updating every instance of the service.

It’s far better to focus client provisioning on a security service that’s designed to handle it. With this approach, it is possible to add new client identities without the service being made aware of the addition.
Nightmarish credential rotation

More often than not, credentials don’t rotate (they are secret, right?). There’s frequently no way to rotate the credentials once the service starts. If you rotate credentials, perhaps because of a credential leak or the pursuit of good credential hygiene, you must perfectly choreograph updating the secret at both the client and service. Imperfect timing creates an outage. To get away from this, a service needs to be able to validate more than one set of credentials per client at a time. This is seldom thought of before it’s too late. Bleh.

Rather than implement a credential rotation scheme in every service, it’s far better to offload credential rotation to a security service that’s designed to handle it.
Nightmarish credential distribution

Credentials must be delivered to where they are needed. This is an evergreen problem. Technologies like Square’s Keywhiz can certainly help, but it’s still a hard problem. If you have “N” instances of a service, then each service instance must have access to all of the client credentials. If you try to just keep the client credentials in service memory, then you really have a problem. You’ll probably try to get around that by storing the credentials in a database, which leads to the next two problems.

It’s far better to focus credential distribution on clients and a security service that’s designed to handle it.
Unnecessary I/O

Data access can be fast, but it can also be slow, sometimes without warning. When a service stores credentials in a database it must access the database on every request. It’s possible to cache credentials after the service sees them, but then there’s suddenly a cache-invalidation problem. Double yuck if each instance of a service keeps a cache. In the extreme, an anonymous client can create a denial of service by force the service to perform I/O with bogus credentials.

It’s far better to validate a token signature than a credential stored in a database since it’s relatively easy to store and manage a single public key in memory.
Credential storage

Storing shared secrets is tricky. Get it wrong, and it could be possible to derive the password from the stored data.

It’s far better to store credentials at a hardened security service. In addition, when your security service supports asymmetric keys for client authentication the security service doesn’t need to store a client secret (it only stores the public key)!         
Credential leakage

Credentials will leak. Count on it. They might appear in a log or end up in a source code repository. Credential leakage is a common component in what’s now known as “the mega-breach”.

It’s far better to use tokens that have a limited lifetime — that way a leaked token has to be quickly used to be useful. It is far better to store public keys at a security service and to rotate keys frequently.
Hard as silly-putty

Services that accept shared secrets must be hardened against malicious clients. That is an article in and of itself. As a simple thought experiment, consider what to do if a client is repeatedly sending the right username but a bad password. Should the username be disabled after a certain number of attempts? Should operations be alerted? How should it be logged? More often than not, services that accept secrets are about as hard as silly-putty.

It’s far better to handle authentication at a hardened security service.
Who done-it

Because credentials are hard to distribute, rotate, and store, some services will distribute the same credential to all of it’s clients (NATs is an example). When this happens, a service loses most of its ability to control and audit consumption.

It’s far better to be able to uniquely identify each client, presumably through a field in a security token.
A better approach

As you can tell from the last section, I believe that offloading work to a security service of some kind is generally a superior approach to handling client authentication. It’s true that a client must still authenticate with the security service, but the security service provides a central place to focus on and to harden. As I mentioned previously, it’s less about how the client authenticates, and more about where the client authenticates. Some security services like Google service accounts take the model even further by using asymmetric cryptography for client authentication. In these cases, it’s possible that a client never exposes the credential; instead it self-issues a token and signs the token with the private key. Boo-yah.

There are several open source security services out there. Use them! At Pivotal we have UAA. It’s a solid piece of software. It is missing a few features (asymmetric client credential key types, credential rotation, etc.), but the team is constantly updating it and I’m sure that it will continue to evolve nicely. Embrace it now and you’ll be on the right path IMO.

Original Page: https://medium.com/@justinjsmith/hatin-on-microservice-passwords-4f8f0c0143ec

Shared from Pocket