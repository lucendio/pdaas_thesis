## Standards, Specifications and related Technologies



The overall goal for this project is to involve as many standards as possible in order to increase 
the chances of interoperability and lower the required effort to integrate with third parties or 
other APIs. The following section describes potentially used technologies and outlines the purposes 
they might serve and why.



__[HTTP]{#def--http}__ [@web_spec_http1], the well known stateless 'transport protocol' for, among 
other things, the *World Wide Web*. It most likely will fulfill the same purpose in the context of 
this work, because it implements a server-client pattern in its very core.
Whether internal components, locally on the same host or as part of a distributed system, talk to 
each other or data consumers interact with the system, this protocol transfers the data that needs 
to be exchanged. The relevance and use cases of the features introduced with Version 2 
[@web_spec_http2] of the protocol are not yet explored.
Unlike *HTTP*, __WebSockets__ [@web_spec_websockets] provide the concept of ongoing bidirectional 
connections on top of TCP, though connection establishment utilizes the same principles known for 
HTTP(S). This combination still allows TLS while benefiting from high efficiency real-time data 
exchange or from supporting remotely pending process responses, while at the same time avoiding 
HTTP's long-polling abilities. It is conceivable to use *WebSockets* for communicate between 
components or even with external parties.

__JSON__ [^abbr_json] is an alternative to the data serialization format XML, whose syntax is inspired
by the JavaScript object-literal notation. It's heavily used in web contexts to transfer data via *HTTP*.
Like XML, its structuring mechanisms allow i.a. type preservation and nesting, which  enable the
representation of more complex data structures, including relations.

The open standard __[OAuth]{#def--oauth}__ defines a process flow for authorizing third parties to 
access externally hosted resources, such as the user's profile image on a social media platform. 
The authorization validation is done by the help of a previously generated token. However, 
generating and supplying such tokens can be initiated in a variety of ways depending on the 
underlying architecture and design, for example, with the user entering her credentials 
(`grant_type=authorization_code`). This design misleadingly seems to encourage developer to integrate
*OAuth* as an authentication service [@web_2012_problem-with-oauth-for-authentication] rather then
an authorization service. This is done whether as an alternative or as an addition to existing in-house
solutions. In doing so, the application authors pass the responsibility on to the OAuth-supporting data providers. 
OAuth *version 1.0a* [@web_spec_oauth-1a], which is rather considered a protocol, provides 
confidentiality by encrypting data before it gets transferred, and integrity of transferred data by 
using signatures. Whereas *Version 2.0* [@web_spec_oauth-2], labeled as a framework, requires *TLS* 
and thus passes on the responsibility for confidentiality to the transport layer below. It also 
supports additional process flows for scenarios involving specific platforms such as 
*"web applications, desktop applications, mobile phones, and living room devices"* 
[@web_2016_oauth-2, para. 1].

This id different in the open standard __OpenID__, which is designed to validate the authenticity of a requesting user. 
An in-depth description of the whole process can be found in the protocol's same-titled 
specifications [@web_spec_openid-spec-index]. With decentralization in mind, the protocols's nature 
encourages to design a distributed application architecture, similar to the idea behind a 
*microservice*, but without owning all services involved. This could be described as  
*decentralized authentication as a service*. An application owner doesn't have to write or 
implement its own user authentication and management system, instead it is sufficient to merely 
integrate those parts that are needed to support signing in with *OpenID*, which is typically a 
client interacting with the Identity Provider.
Equally the user is not required to register an account for every new app, instead she can use her 
*OpenID*, already created with another identity provider, to authenticate with the application. The 
extension *OpenID Attribute Exchange* allows the importing of additional profile data, similar to 
what OAuth is meant to be used for.
*OpenID Connect* [@web_spec_openid-connect-1] is the third iteration of the OpenID technology. While 
facebook, for example, uses OAuth also for authentication (known as pseudo-authentication 
[@web_2017_wikipedia_openid-vs-pseudo-oauth]) instead of just authorising entities, *OpenID connect* 
on the other hand, provides authentication in an additional layer built upon *OAuth2.0* and *JWT*. 
Previous versions of OpenID provided the concept of extension in order to add functionality 
such as accessing profile information. This ability is now part of the core facilitated by OAuth, so 
that a user's identity can share certain data with third parties via REST interface. 

If, due to being part of a distributed software, some components are required to not maintain any 
state, either the architecture needs to be changed so that the state is no longer needed in that 
component, or the state needs to be embedded into the process communication so that it is passed 
from one component to the other. This is a common use case for a __[JSON Web Token]{#def--jwt}__ 
*(JWT)* [@web_spec_json-web-token]. The token contains the state and is formatted as *JSON* and also
encoded with *base64url*, which is a URL-safe version of the widely known Base64 encoding 
[@web_spec_base64url], before it gets transferred.
Here is where the use of *HTTP* comes in handy because the 
token can be stored within the HTTP header and therefore can be passed through all communication 
points, where the data can then be extracted and verified. Such a token typically consists of three 
parts: (A) information about itself, (B) a payload, which can be arbitrary data such as user or 
state information, and (C) a signature; all separated with a period. Additional standards define 
encryption *(JWE [^abbr_jwe])* to ensure confidentiality, and signatures *(JWS [^abbr_jws])* to 
preserve integrity of it's contents.
Using a *JWT* for authentication purposes is described as *stateless authentication*, because the 
verifying entity doesn't need to be aware of session IDs nor any other information about a session. 
So, instead of the backend interface being burdened to check a state on every incoming request in
order to verify permissions, which required to maintain a state in the first place, it just needs
decrypt the token and proceed according to the contained information.

When transferring data over a potential non-private channel, several features might be desired in 
order to provide trust in those data. One important aspect might be that no one else except sender 
and receiver are able to know and see what the actual data are. In order to achieve this, we use
__Symmetrical Cryptography__. It states that the sender encrypts the data with the help of a key and the receiver 
decrypts the data with that same key. That is, sender and receiver, both need to know that 
particular key, and everyone who is not allowed to access that information must not be in possession 
of that key. To agree on a key without compromising the key during that process, both entities 
either need to switch to a private medium (e.g meet physically and exchange) or have to use a 
procedure in which the entire key is not exposed to others at any single point in time. For this we use an
algorithm called __Diffie-Hellman-Key-Exchange__ [@paper_1976_d-h-key-exchange], which is based on 
the mathematical discrete logarithm problem [XXX - source]. It allows to parties to to agree on a common *secret* while
using a non-private channel. The data exchanged during the process alone cannot be used to exploit the secret.
The possibility to use a potential hostile channel for communication is also one of the strengths of
__[Asymmetrical Cryptography]{#def--asym-crypto}__ *(or public-key cryptography)*
[@book_2014_chapter-9-1-public-key-crypto], which is underpinned by a *key-pair* for every communication participant.
Every participant publishes her *public* key and keeps the other one strictly *private*. For every one else it is
now possible to use one these public keys to *encrypt* data, so that the resulting cypher can only get *decrypted*
using the related *private* key. If this is combined with  the concept of digital signatures (encrypted fingerprints
from data), the result would provide integrity and authentication.

Putting *HTTP* on top of the *Transport Layer Security (TLS)* [@web_spec_tls] results in 
__[HTTPS]{#def--https}__. 
TLS provides encryption during data transport, which reduces the vulnerability to 
*man-in-the-middle* attacks and thus ensures not only confidentiality, but data integrity too. 
*Asymmetric cryptography* is the foundation for the connection establishment, hence *TLS* also 
allows us to verify integrity of the entity on the the connection's other side and, depending on the 
integration, it could even be used for authentication purposes. Though relying on those 
cryptographic concepts requires additional infrastructure. Such an infrastructure is known as 
*Public Key Infrastructure (PKI)* [@book_2014_chapter-14-5-pki]. It manages and provides keys and 
certificates for a dedicated scope of entities in a directory, including information related to their owners.
The infrastructure that is needed to provide secure HTTP connections for the internet is one of those
*PKI*s - a public one and probably one of the largest. It is based on the widely used IETF
[^abbr_ietf] standard *X.509* [@web_spec_x509].

__REST(ful)__ [^abbr_rest] is a common decription for a web service that follows a set of principles
known as Representational state transfer (*REST*). *REST* describes how to design web resources and their 
interaction. It primarily defines server-client communication in a more generic and therefore 
interoperable way. Aside from hierarchically structured URLs, which can reflect semantic relations 
or hierarchical order between data items, it involves a rudimentary vocabulary [^http_methods] for 
HTTP requests to provide basic CRUD-operations [^abbr_crud] across distributed systems. The entire 
request needs to contain everything that is required to be processed on the REST-server, e.g. state 
information and possibly authentication parameters. 
A restful API typically has the purpose of exposing certain features provided through the platform 
or service to third parties in order to synergistically integrate with them. But utilizing these
principals for all internal server-client interaction is also very common.
This concept can also be understood as a proxy to the actual business logic in the back end.

The *query language* (*QL*) __[GraphQL]{#def--graphql}__ [@web_spec_graphql] is developed
by Facebook Inc., to abstract multiple data sources into an unified API or resource. Such an extra
level of abstraction allows them to make different storage technologies seamlessly queryable without
using their underlying native *query languages*. The query 
result is provided in JSON format, which naturally supports graph-like data structures. This is 
utilized in GraphQL and implicitly embraced through its purpose of abstraction. Data items that
might be somehow related but stored in different locations, can be obtained so that both end up in 
the same object through which they are related or indirectly linked to each other. The shape of a 
query is later mirrored by the result. GraphQL is not only an abstraction towards a more generic 
query language. It also separates almost any operation and the flow control from the the *QL* and 
moves it into a second layer. The so called *GraphQL* server is responsible for resolving and 
executing queries. 

The term __[Semantic Web]{#def--semantic-web}__ bundles a conglomerate of standards released by the 
W3C [^abbr_w3c]. It is based on an idea called *web of linked data*, which aims to *"enable people 
to create data stores on the Web, build vocabularies, and write rules for handling data"* 
[@web_2016_w3c_semantic-web-activity, para. 1]. The standards address syntax, schemas, formats, 
access control and integrations for several scopes and contexts. Among others, the following three 
technologies are essential for the *Semantic Web*.
RDF [^abbr_rdf] basically defines the syntax. OWL [^abbr_owl] provides the guidelines on how the 
semantics and schemas (vocabulary) should be defined and with [SPARQL]{#def--sparql} 
[@web_w3c-tr_sparql], the query language, data can be retrieved.
One can not help but picture the web as a database of queryable data with URIs that are embedded 
in arbitrary websites. Imagine a person's email address, which is available under a specific domain 
(preferably owned by that person) - or to be more precise, a URI *(WebID) [@web_w3c-draft_webid]* - 
and provided in a certain syntax *(RDF)*, tagged with the semantic *(OWL)* of a email address, all 
together embedded in an imprint of a website. This information can then be queried *(SPARQL)*, if 
the unique identifier of that person *(URI)* is known. 
While defining the standards, a main focus was to design a syntax that is at the same time valid 
markup. The vision behind this: embracing the concept of a single source of truth (per entity) and 
embedding or linking data items rather then creating new instances of the same data that might only
valid at that point in time - in short, preventing redundant work and outdated data.
Related to the *Semantic Web* is the a project called __Solid__ [^abbr_solid]. Backed by the 
*WebAccessControl* [@web_2016_wiki_webaccesscontrol] system and based on the *Linked Data* 
principles including the standards just mentioned, the project focuses on decentralization and
personal data management while developing a specification around this. A reference implementation 
called *databox* [@web_2016_demo_databox] follows the specification process. 

The concept of an application (or software) __[container]{#def--container}__ is about encapsulating 
runtime environments by introducing an additional layer of abstraction. A container bundles just 
those software dependencies (e.g. binaries) that are absolutely necessary so that the enclosed 
program is able to run properly. The actual separation from the host system is done, aside from 
other technologies, with the help of two features provided by the Linux kernel. *Cgroups* 
[^abbr_cgroups], which defines or restricts how much of the existing resources can be used by a 
group of processes (e.g. CPU, memory or network). Whereas *namespaces* [@web_2016_kernel-namespace] 
defines or restricts what parts of the system can be accessed or seen by a process (e.g. filesystem, 
user, other processes).
The idea of encapsulating programs from the operating system-level is not new. Technologies, such as 
*libvirt*, *systemd-nspawn*, *jails*, or *hypervisors* (e.g. VMware, KVM, virtualbox) have been used 
for years, but some of them were usually too cumbersome and never reached a great level of 
convenience, so that only people with expertise have been able to handle systems built upon 
virtualization, whereas people with other backgrounds couldn't and weren't very interested, at least 
until *Docker* and *rkt* emerged. After some years of separated work, both authors, and others, 
recently joined forces in the *Open Container Initiative* [@web_2016_open-container-initiative], 
which promises to harmonize the diverged landscape and start building common ground to ensure a 
higher interoperability. That, in turn, started to raise the demand for sophisticated orchestration. 
It also marks the initial draft of specifications for runtime [@web_oci-spec_runtime] and image 
[@web_oci-spec_image] definition for *application container*, which are still under heavy 
development. This concept of *containerization* also has the side-effect of making the application 
platform-agnostic, because it allows a certain set of software to run on a system which might 
otherwise not support that software (e.g. mobile devices); it just requires the runtime to be 
supported.

In the past years different countries around the world started to introduce *information technology* 
to the day-to-day processes, interactions and communications between public services and their 
citizens. Processes like changing residence information or filing tax reports, are all summarized 
since then under the name *E-government* [^abbr_egov]. One of those developments is the so called 
__Electronic ID Card__{#def--eid-card}, hereinafter called *eID card*. Equipped with storage, logic 
and interfaces for wireless communication, those *eID cards* can be used to store certain 
information and digital keys, or to authenticate the owner electronically to a third party without 
being physically present. Such an *eID card* was introduced also in Germany in 2010. The so called 
*nPA* [^abbr_npa] has been an important step towards an operational *e-government*.
Aside from minor flaws [@web_2013_npa-sicherheitsdefizit] and disadvantages 
[@web_2014_test-qes-support-in-npa] an *eID card* might come along with, the question here is, how 
can such technology be usefully integrated in this project and is it plausible to do so? 
As an official document, the card has one major advantage over inherent, self-configured or 
generated secrets like passwords, fingerprints or TANs [^abbr_tan]. It is *signed* by design, which 
means, by creating this document and handing it over to the related citizen, the third party 
(or *'authority'*) - in this case the government - has verified the authenticity of that individual.

When communicating via email it's already common to encrypt the transport channel, but using
*asymmetric cryptography* for encrypting emails end-to-end is rather unusual. The equivalent to a 
*PKI* would be basically a *public key server* that follows a concept called *web of trust*. In 
which all entities (user; senders and recipients) are signing each other's public keys. The more 
users have signed a public key, the higher the level of trust is on that key and therefore on the 
entity who the signed key-pair belongs to. Public keys are simply uploaded by its owners to those 
key servers mentioned before. If someone want to write an email to others, all public keys relating 
the recipients must be obtained from these public server, so that the email can then be encrypted 
with those keys and are therefore only readable by the owner of the keys's private part.

Related to that topic, another technology emerging as part of the *e-government* development, is the 
german __[De-Mail]{#def--de-mail}__ [@web_2017_about-de-mail]. It's an eMail-Service that is meant 
to provide infrastructure and mechanisms to exchange legally binding electronic documents. One would 
expect a *public key cryptography*-based implementation from sender all the way over to the 
recipient [@statement_2013_de-mail]; maybe even with taking advantage of the *nPA's* capability to 
create *QES*, which refers to the ability of using the *nPA* to sign arbitrary data. Instead, the 
creators of the corresponding law decided that it's sufficient to prove the author's identity based 
on its credentials when handing over the email to the server, and that it's reasonable to let the 
provider sign the document on the email server, and finally that this described implementation 
results in a legally binding document by definition of that law.
The different levels of mistakes made in conception and legislation are outlined in a statement from 
CCC[^abbr_ccc]-members [@statement_2013_de-mail], who have been consulted as official experts during 
the development of that law.



[^abbr_json]: The JavaScript Object Notation (JSON) Data Interchange Format; ECMA Standard  
    [@web_spec_json] and Internet Engineering Task Force RFC 7159 [@web_rfc_json]

[^abbr_jwe]: JSON Web Encryption, Internet Engineering Task Force RFC 7516 
    [@web_spec_json-web-encryption]

[^abbr_jws]: JSON Web Signature, Internet Engineering Task Force RFC 7515 
    [@web_spec_json-web-signature]
    
[^abbr_ietf]: Internet Engineering Task Force; non-profit organisation that develops and releases 
    standards mainly related to the Internet protocol suite

[^abbr_rest]: *Representational State Transfer*, introduces by Roy Fielding in his doctoral 
    dissertation [@web_spec_rest] 

[^http_methods]: HTTP Methods or Verbs [@web_spec_http-methods] (e.g. GET, OPTIONS, PUT, DELETE)
    
[^abbr_crud]: Create, Read, Update, Delete
    
[^abbr_w3c]: World Wide Web Consortium; international community that develops standards for the web  
    
[^abbr_rdf]: Resource Description Framework [@web_w3c-tr_rdf]

[^abbr_owl]: Web Ontology Language [@web_w3c-tr_owl]

[^abbr_solid]: social linked data [@web_spec_solid]

[^abbr_cgroups]: control groups [@web_2015_cgroup-doc]

[^abbr_egov]: Electronic government

[^abbr_npa]: in german so called *elektronische Personalausweis (nPA)*

[^abbr_tan]: Transaction authentication number

[^abbr_qes]: Qualified Electronic Signatures [@paper_2013-keymanangement-fuer-qes-mit-npa]

[^abbr_ccc]: Chaos Computer Club e.V.
