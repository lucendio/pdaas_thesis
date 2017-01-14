## Authentication



First of all, the system has to support two [roles]({#sa03). Any entity can be assigned to either 
one of them, hence entities that are trying to authenticate to the system might have different 
intentions. The *operator* for example wants to review *permission requests* in real time, so 
accessing the system from different devices is a common scenario. When inheriting the *operator 
role* an entity gains further capabilities to interact with the system, such as data manipulation.
Whereas a *data consumer* always uses just one origin and processes requests sequentially. Those 
very distinct groups of scenarios would make it possible to apply different authentication 
mechanisms that do not necessarily have a lot in common.

With respect to the requirements ([S.A.01](#sa01)), the most appropriate way to communicate with the 
*PDaaS* over the internet would be by using *[HTTP](#link_http)*. Furthermore, to preserve
confidentiality on every in- and outgoing data ([S.P.01](#sp01)) the most convenient solution is to 
use *HTTP* on top of *TLS*. *TLS* relies i.a. on [asymmetric cryptography](#link_asym-crypto). 
During the connection establishment the initial 
handshake requires a certificate, issued and signed by a CA [^abbr_ca], which has to be provided by 
the server. This ensures at the same time a seasonable level of identity authentication, almost 
effortless. If the certificate is not installed, it can be installed manually on the client. If 
the certificate is not trusted (e.g. it is self-signed), it can either be ignored or the process 
fails to establish a connection, depending on the server configurations. The identity 
verification in TLS works in both directions, which means not only the client has to verify the 
server's identity by checking the certificate. If the server insists on, the client has to 
provide a certificate as well, which then the server tries to verify. Only if the outcome is 
positive, the connection establishing succeeds. According to the specification 
[@web_spec_tls-12_client-auth] it is still optional though.

*HTTP* as a comprehensive and flexible protocol enables to use several technologies for 
server-client authentication purposes. 
Within the scope of this work, those technologies are categorized in the following types 
(TODO: maybe find other labels): (A) stateful and (B) stateless authentication. The first one (A) 
includes vor example *Basic access Authentication* (or *Basic Auth*) and authentication based on 
*Cookies*. Whereas the *two-way authentication* in TLS mentioned above and 
[authentication based on web-token](#link_jwt) are associated with the latter (B). 
*Basic Auth* is natively provided by the *http-agent* and requires in it's original form 
(*user:password*) some sort of state on the server; at least when the system has to 
provide multitenancy. If instead just a general access restriction for certain requests would 
suffice, no state is required. One of the most common implementations of user-specific states is a 
*session* on the server, that contains one or more values representing the state and a unique 
identifier, by which an entity can be associated with. A client has to provide that session ID in 
order to get provided with all the session-related data hold by the server. This is typically done 
in a HTTP header, whether as *Basic Auth* value, a *Cookie*, which is domain-specific, or in some 
other custom header. 
Since the *two-way authentication* (or *mutual authentication* [@web_2017_wikipedia_mutual-auth]) is
done based on files containing keys and certificates, which are typically not very fluctuant in
it's contents or state, this procedure is categorized as stateless. Order or origin of incoming 
requests have no impact on the result of the actual authentication process. The same applies to TLS 
features such as *Session \[ID, Ticket\] Resumption* 
[@book_2013_networking-101_tls-session-resumption], thus they are left aside, because they serve the 
sole purpose of performance optimization.
Similar to the *Session Ticket Resumption* [@web_spec_tls-session-ticket-resumption] a web token, 
namely the [JSON Web Token](#link_jwt), also moves the state towards the client, but that's about 
all they have in common. A *JWT* carries everything with it that's worth knowing, including possible 
states, and if necessary the token is symmetrical encrypted by the server. This is, only the server 
is able to obtain data from it and reacting accordingly.

Keeping track of a state (or multiple states) on the server and keeping data that is involved  
synchronized between server and client is expensive and by fare trivial. Expensive in the sense of 
additional resources a server would require to remember all the data for those states, that 
otherwise won't be needed. And it's not trivial, because this pattern requires the server to be 
aware of all current states (sessions) and has to have them accessible at all time. This also means, 
that the contents responses for certain requests might depend on preceding requests and their 
incoming order. Furthermore those session data has to be safely stored from time to time. Otherwise 
if the server fails to run at some point, data only existing in the memory would be gone without 
any possibility to get recovered.
To stateless authentications non of those aspects apply. Certificates and keys as well as web 
tokens are both carry the information that might be necessary with them. Thus, considering those
disadvantages, *public key cryptography* and web tokens are the preferred technologies for all
authentication processes.

Because of it's simplicity the concept of web tokens are fairly straightforward to implement into 
the *PDaaS*. But since web tokens ensure integrity and perhaps also the confidentiality only of 
their own carriage and not the entire HTTP payload, both requirements need to be addressed 
separately. Serving HTTP over *TLS* solves this issue, but using TLS or *asymmetric cryptography*
properly - place value on integrity, confidentiality, authenticity - requires additional 
infrastructure. Such an infrastructure is known as *Public Key Infrastructure* (or *PKI*)
[@book_2014_chapter-14-5-pki]. It manages and provides public keys in a directory, including related 
information to the entities those certificates belong to. A Certificate Authority (or *CA*), as part 
of that infrastructure, issues, maintains and revokes digital certificates.
The infrastructure that is needed to provide secure HTTP connections for the internet can be seen as 
a large, if not the largest, public *PKI*. It is based on the widely used IETF [^abbr_ietf] standard 
*X.509* [@web_spec_x509].
For connections that use a web token, it should be sufficient to rely on the public PKI
that drives *HTTP* over *TLS*, because all information required to authenticate is provided by the 
token itself. Though, the situation is different if instead *two-way authentication* is used. For 
this, the 
system has to provide it's own *PKI* including a Certificate Authority that issues certificates for 
*data consumers*, because not only the *endpoints* on the *PDaaS* (server) need to be certified, 
all *data consumers* (clients) need to present a certificate as well. Only the *PDaaS* verifies and
thus determines (supervised by the *operator*) who is authorized to get access to the system. Hence 
the *PKI* needs to be self-contained without any external role in order to function independently so 
that only invited parties can get involved.







An advantage of token-based authentication over TLS-based *mutual authentication* is that the token 
can be used on multiple clients at the same time. Or the account, a token is associated 
with, can actually have more than one token. Whereas during the asymmetric cryptography-based 
*mutual authentication* the client's private key is required. So if it's likely that a *operator* 
has several clients, regardless for what purposes, the private key needs to be on those clients. But 
a private key typically does not leave it's current system or is at least not used multiple times, 
in order to prevent exposure, which is implied in any action of duplication. To reduce those risks, 
it's common practice to generate a private key at that location where it is going to be used.



If a public-key-based connection, performing a *mutual authentication*, establishes successfully, it
implies that the identity originating the request is valid and the integrity of the containing data 
is given.
Whereas on a token-based authentication every incoming request has to carry the token so that the
system can verify and associate the request with an account. Furthermore data it not automatically 
encrypted and thus integrity is not preserved.



Computations based on asymmetric cryptography usually is slower then the ones based on symmetric
cryptography [@book_2014_chapter-10-5-asym-random-number-gen], but since there are no timing 
constrains when interacting with the *PDaaS*, regardless of whether it's external communication with 
*data consumers* or internal between components, parameters for cryptographic procedures can chosen 
as costly as the system resources allow them to be, thus the level of security can be increased.

To hardening an authentication procedure often one or more factors are added. This makes the 
procedure more complex and thus increases the effort that's needed for succeeding attacks.
Using multi-factor authentication is generally valued and will be briefly noted as an 
optional security enhancement for the *operator role*. However detailed discussions regarding this 
topic are left to follow-up work on the specification.


 
[^abbr_ietf]: Internet Engineering Task Force; non-profit organisation that develops and releases 
    standards mainly related to the Internet protocol suite



*Conclusions:* 
Based on the several requirements and distinct advantages of the two authentication mechanisms, 
it would make sense to use asymmetric cryptography in combination with *HTTPS* for the communication 
between the system and *data consumers*, where the system provides it's own *PKI*
and a token-based authentication on top of *HTTPS* and public CAs for communication between the 
system and the *operator*, preferable based on *[JSON Web Tokens](#link_jwt)*, because the session 
state is preserved within the token rather then having the system itself keeping track of it.
