## Data



The core task of a *PDaaS* is providing data, *personal data*, which in conjunction is the digital
manifestation of an individual, a person. One party creates the data, another one obtains and
processes it. Thus, both need to agree, or at least need to know, how that data looks like, how is 
it structured and what are their semantics. The following section is intended to discuss different
technologies, used to create queries that obtain those data points that are desired. Further on, it 
describes some basic data types and schemas, that might be useful in the context of *personal data* 
and also for previously introduces [scenarios](#scenarios).  

First of all, to address the need of portability, which has to be satisfied by those components, 
who are storing and providing *personal data*, it is essential to abstract the actual storage from 
how it gets accessed. This makes it possible to relocate those storage onto other platforms and 
environments. Thereby the *personal data storage* itself becomes platform-agnostic from an outside 
perspective, in other words portable. In order to reduce possible issues related to unsupported 
communication protocols it might be reasonable to enforce HTTP - over TLS, if they don't share the 
same environment - even if the storage therefor requires an additional driver or proxy layer, like 
for example a mobile app.

Possible technologies are for example *[GraphQL](#def--graphql)* or the 
*[SPARQL](#def--sparql)*, which is part of the *[Semantic Web Suite](#def--semantic-web)*. Both are
query languages underpinned by the concept of a graph. This means, relations between data points 
are embedded within the data structure itself. That meant, in terms of a graph, relations are 
*edges* and data points are *nodes*. In consequence the structure of a query itself reappears in its 
result, which means the originator of that query knows exactly what to expect for the response. 
Therefore it's not necessary to provide any additional information about how to handle and interpret 
the responded data. The code examples 
([Code 01](#code-01_sparql-query) and [Code 02](#code-02_sparql-query-results), 
[Code 03](#code-03_graphql-query) and [Code 04](#code-04_graphql-query-result)) 
give a first impression of how it might look like, when a *consumer* obtains the name of the 
*data subject* and a bank account of hers, that supports online payment.

Without going into much details here, the syntax of the SPARQL query 
([Code 01](#code-01_sparql-query)) already shows its nature of decentralization. This aspect at the 
same time introduces additional external dependencies. Because the query language itself has no 
concept of schemas or any kind of semantic, it needs to be made aware of them. SPARQL queries 
typically return XML which then can be rendered into (HTML) tables. JSON and RDF are also supported.
The reason for performing two queries in the example instead of just one is because the 
result would otherwise have returned multiple 'rows' with redundant data if more then one bank account  
supported online payment; the bank account data would be different, but the profile information would be repeated.

Whereas the GraphQL query syntax ([Code 03](#code-03_graphql-query)) compared with its result 
([Code 04](#code-04_graphql-query-result)) shows of a remarkable resemblance. 
By defining `paymentMethod` as an argument, the resolver for `bankAccounts` then implements an 
instruction to match the value of that argument (`'online-service'`) against the whole set. 
GraphQL's server then knows from which resources the data in question need to be pulled and how
they need to be aggregated. 
While SPARQL has a full-featured query language syntax including all sorts of controls (e.g. 
aggregation, operators, nested queries etc.), GraphQL's syntax instead is more rudimental, because
all its functions and logic has been abandoned from the language itself and put into a server part. 
With this concept of separation it is straightforward to validate queries, because it essentially 
means matching against types. 
Both query languages share a comprehensive understanding of a type-system, that encourages to 
create all kinds of data types. While in GraphQL common schemas still need to be created, SPARQL 
already provides a reasonable amount of vocabulary [@web_w3c-tr_rdf-schemas]. However, when 
comparing the results of both languages, some distinctions appear. While in GraphQL the 
characteristics of graph-structured data are remain, SPARQL's output is missing a certain level of 
depth. The reason for that originates in the design of the query language and its syntax. SPARQL is 
able to notice implicit relations between data points, though its query language is not capable of 
grabbing and presenting them. Thus the result ([Code 02](#code-02_sparql-query-results)) only 
consists of two dimensions.

It is crucial for the *PDaaS* to provide the *data subject* with abilities to create her own data 
types and schemas ([S.P.03](#sp03)). Thereby she is enabled to serve data points according to her 
own needs and terms. In order to interact with their customers or users, *data consumers* might 
as well develop and provide schemas for their requests. This can help *data subjects* to speedup the 
process of permission granting and to easier understand what data points are affected. Data types 
and schemas are the key to validate incoming and outgoing data. If data violates the underlying 
schema or no appropriate schema exists, the data transfer fails. 
Other missing data types could be developed by a community, because not every *data subject* might 
be capable of modeling her own data types. Thus everyone can benefit from that effort taken by a 
few. As a result, the ones that are widely used might then become de facto standards. Moreover, it's 
also possible that several data types will emerge, that are based on common standards, for example 
*medical record* [@web_spec_data-schemas_ehr], *point of interest* [@web_spec_data-schemas_poi] or 
*bank transfer* [@web_spec_data-schemas_bank-transfer]. With that approach those data types can be 
viewed as something like a plugin or add-on to the *PDaaS* ecosystem.

In order to avoid confusion about the differences between types and schemas and to simplify their
relations, the following two definitions are henceforth being used. A (data) *type* is the superior 
term; hence refers to both of them. 

[primitive]: most basic or atomic data type; defined as either *String*, *Boolean*, *Integer*, 
    *Float* or *Nil (null)*

[struct]: combines multiple types in order to define more complex data types; typically composed of 
    *primitives*, but can consist of other structs as well
  
Based on these two concepts almost any imaginable data type can be modeled. A selection of such
types can be found in the list of [suggested structs (List 01)](#list-01_suggested-structs), 
whereas an extract of (sub-)categories that might be useful in a *PDaaS* are specified in a list
of [data categories (List 02)](#list-02_data-categories). Additional examples for *structs*
are a [*data subject's* profile (Code 05)](#code-05_struct_profile), a
[contact (Code 06)](#code-06_struct_contact) and bare 
[position information (Code 07)](#code-07_struct_position). 
All those examples and lists are only to be understood as a starting point that should cover basic 
scenarios as well as to give a first impression of what data types a *PDaaS* could provide.

__[List 01: Suggestions for useful structs]{#list-01_suggested-structs}__
+   Address
+   Contact
+   Location
    -   Country
    -   City
    -   Position
+   Media
    -   Audio
    -   Video
    -   Photo
+   Organisation
+   Date
+   TimeRange
+   Language
+   Diseases

__[List 02: relevant (sub-)categories of data]{#list-02_data-categories}__
+   Finance
    -   Income
    -   Bank transfers
+   Shopping history
+   Product
+   Things/Objects
+   Media consumption
    -   Music playlist
    -   Watchlist
+   Favorites/Interests
    -   Music genres
    -   Songs
    -   Movies
    -   Books
    -   Travel destinations
    -   Topics
+   Curriculum vitae (CV)
    -   Educational level
    -   Visited schools
+   Visited ...
    -   points of interest
    -   countries
    -   websites/URLs (browser history)
+   Units (measurements)
+   Organisations
    -   Company
    -   Bank
    -   ...
+   Medical/Health Record
    -   Diseases
    -   Treatments
    -   Visits to the doctor
    -   Medication

The available *primitives* mainly depend on those who are supported by the query language itself.
In this case, all *primitives* mentioned above are supported by *SPARQL* [@web_spec_xml_types] and 
*GraphQL* [@web_spec_graphql_types]. When choosing a database system it has to be ensured that 
either the system already supports the required *primitives* or they can be emulated somehow with a 
least amount of drawbacks. When modelling relations between data points one can use for example keys 
(or identifiers) to make reference, or additional syntactical tools like *lists* (or arrays) and 
maps (or objects). Those tools facilitate readability so that relations are almost intuitively 
observable, hence they should be enforced. Whereas another known concept in data modelling, 
called *inheritance*, isn't required, but could help to reason about certain *structs* and their 
representations. It might add complexity, though.

Aside form the subject's personal data other information and data must be persisted as well. This
includes for example:

+   Application data
    -   Templates ([P.I.05](#pi05))
    -   Permission profiles (incl. versioning)
    -   Consumer information
    -   Meta data
    -   Notifications
    -   States
    -   Tokes
    -   Access logs
+   Files
    +   Cryptographic keys
    +   Executable program
    +   Container images
    +   Configurations
    +   User interfaces
    +   Documentations

The list revels that not only a database system is needed to satisfy the requirements, but the 
environments filesystem might need to be utilized as well.
This leads to the question of what requirements a database system has to satisfy. But first of all
it is pivotal to distinguish between the needs of a *personal data storage (PDS)* and a general 
*persistence layer (PL)* for the system's backend.


| Characteristic                               | Personal Data Storage | Persistence Layer |
|----------------------------------------------|:---------------------:|:-----------------:|
|  portable                                    |           -           |         -         |
|  advanced user & permission management       |           -           |       __X__       |
|  document-oriented                           |         __X__         |       __X__       |
|  support common primitives                   |         __X__         |       __X__       |
|  replication                                 |           -           |       __X__       |
|  efficient binary storage and serialization  |         __X__         |       __X__       |
|  high performance                            |           -           |       __X__       |
|  operations and transactions                 |         __X__         |       __X__       |
|  background optimization                     |           -           |       __X__       |
|  version control                             |           -           |         -         |

Table: selection of characteristics that a database system has to feature in order to be suitable 
    for either of the defined purposes {#tbl:dbs-features} 


Although, most of the characteristics (in Table @tbl:dbs-features) are self explanatory, certain 
aspects need to be commented on. Fist, portability, an important requirement ([S.A.02](#sa02)), 
that is oddly not marked in Table @tbl:dbs-features. This is because of the priorly introduced 
concept of abstracting the *personal data storage* with an additional query language. Therefore the 
access to the *PDS* becomes platform-agnostic. Whereas the database system storing that data can be 
implemented with respect to the requirements while considering the environment constraints at the 
same time. 
Basic permission management should suffice the *PDS*, since it's not accessed in multiple ways. 
It only relates to the query language abstraction. Data and especially its structure is expected to
be highly fluctuant ([S.P.02](#sp02)), thus advantages of relational databases (e.g. schema-oriented 
and -optimized) would instead harm the performance and flexibility, because they are not primarily 
designed to handle schema changes. Database systems, whose storage engine is build upon a 
document-oriented approach, would be a better choice. 
Replication can be utilized for horizontal scaling, federation or backups ([S.P.05](#sp05)). The 
focus here is on the latter, because without *PL* the *PDaaS* wont be able to function. In case of 
irreversible data loss, the whole system state is gone, which then has to be reconfigured and 
reproduced from the ground up. Such effort can be spared by introducing a reliable backup strategy. 
With the *PDS* on the other hand replication is not necessary, but to ensure no data loss still 
needs to be addressed. Therefor every database system that might be used for the *PDS* must provide 
a mechanism to backup or at least to export the data, which can be triggered and obtained through 
the operator's management tool. Another approach imaginable could be to not only store the actual 
data written to the *PDS* but also to save all queries in a chronological order that have somehow 
changed the data. Therefore the current state can be restored just by running those queries against 
the *PDS*. It is reasonable to store the queries of the abstracted query language not the ones the 
query language is transformed into. If a mobile device is part of the *PDaaS*, another approach for 
the operator could be to perform regular device backups. Those strategies are all just initial 
thoughts which might be sufficient only as a starting point. Other solutions are imaginable. Though, 
elaborating on those is beyond the scope of this work.
Depending on the technologies that are being used, it might be necessary from a conceptional 
perspective to split the *PL* into two parts. One part is a database system and the other is 
represented by the environment's filesystem. It might be no alternative when it comes to key files,
which are typically accessed as files, or configuration files for certain technologies. 
In any case, both *PL* and *PDS*, have to be able to store files of any kind, which is required for 
instants to support the secenario of medical records. File size restriction should be mandatory 
though, because the *PDaaS* has no intention to replace existing *file hosting* solutions. 

Being able to undo changes of certain data points or to review the change-history of those data can 
be very useful; not only when those changes were persisted mistakenly. This behaviour might not be 
necessary for every data, especially when it comes to application configuration or logs. Also, not 
every *operator* might require these features. Therefore, and because database systems with no 
alternative might not be able to provide this capability, it's not required by either *PDS* nor 
*PL*. If a history is not natively supported but still desired, it has to be considered if for 
example high frequently backups would already suffice or if a implementation on the 
application-level is required.

Before serving data it needs to be at first inserted into the *PDS*. This can be done in three 
different ways:

a)  The *data subject* uses forms provided by the graphical user interface to insert data about her, 
for example her [profile information (Code 05)](#code-05_struct_profile). This data is then 
submitted into the *PDaaS* which takes care of storing it.
b)  The *data subject* is in possession of file(s) or string(s) containing a data format that is 
supported by the system. The graphical user interface provides a mechanism to either upload the 
file(s) or insert the string(s). Thereby the data is then send into the system. If this raw data
is not self-explaining to the system the *data subject* has to provide more information on the 
context of those data.
c)  Third party software, for example a browser plugin, is used to provide the *PDaaS* with data; in
this case a browser history. This software uses a restricted API provided by the *PDaaS* to let data 
flow into the system.

These three concepts, especially b) and c) are required to get inspected for malicious content and
extensively validated against existing *structs*. Only if these checks do not fail, the submitted 
data can be stored. For scenario b) the *data subject* needs to be asked to review the imported data 
to make sure everything is as expected. When enabling third party software to submit data, 
appropriate authentication and permission mechanisms must be in place. 
That software is classed like all other *operator* front ends, but without permissions to obtain 
data. According to the [discussion on authentication](#authentication), these mechanisms thus can be 
implemented through *JWT*.



*__Conclusions:__*
In order to gain flexibility in choosing technology and location for the *personal data storage*, 
the logical consequence is to abstract the interface from the database system. Introducing a
separate query language is proposed as a reasonable approach. It can be chosen between two suggested 
query languages, *GraphQL* or *SPARQL*. Both provide the necessary features required to integrate 
them with a distributed system; *SPARQL* with its concepts of URIs as identifiers and resources, and 
*GraphQL* with its separation of query definition and execution. This also effects the process of 
query validation, which is much harder to do for *SPARQL*, because its syntax is more flexible and 
allows some shorthands, therefore the possibilities are way to many. In general *SPARQL's* syntax is 
harder to reason about compared to *GraphQL*. And even though the result of both languages is 
formatted in JSON, only *GraphQL* preserves all the relations in the output, which are already 
embedded in the query syntax. 
In result, *GraphQL* (and its implementations) is the query language of choice for this project.

When it comes to creating new structs engaging a user community can compensate the lack of certain
types. Examples for a potential starting point of *PDaaS*-supported data types were showed before.
Data Modelling in general is a large research field to its own. With regards to the *PDaaS* it 
needs much more attention, though it's beyond the scope of this work. The basic approaches within 
this section should only be viewed as an introduction that gives an outlook of how it's imagined. 
