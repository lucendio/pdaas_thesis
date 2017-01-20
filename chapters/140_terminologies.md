## Terminologies 



Web Service
: TODO

Open Specification
: TODO

Big Data
: deep learning, neural networks

Profile Data
: individual's inherent data; TODO

Personal Data (TODO)
: Personal Information  predominantly static data points related to an individual

Personal Data as a Service (PDaaS)
: a web service controlled, owned and maybe even hosted by an individual, that provides access to 
the data subject's personal data and offers maintainability as well as permission management. It can
be seen as her personal agent; sometimes also referred to as *the system*

Personal Data Store
: TODO

Vendor Relationship Manager
: The *ProjectVRM* defines the a VRM as follows: "TODO" [@web_2010_projectvrm-wiki_about-vrm]

Personal Information Management Systems (PIMS)
: TODO [@web_2010_projectvrm-wiki_pims-example-list]

serverless
: TODO https://auth0.com/blog/2016/06/09/what-is-serverless/

Digital Footprints
: TODO

Data Subject
: an individual who first and foremost is the owner of all of her personal data; sometimes referred 
to as *owner*
 
[Operator]{#terminologies--operator}
: a *data subject* using a PDaaS to control (and probably host) her personal data; sometimes 
referred to as *data controller*

[(Data) Consumer]{#terminologies--consumer}
: Third party, external entity requesting data, authorized by the data subject to do so; sometimes 
referred to as *(data) collector*

Data Broker(s)
: entities with commercial interests, that collect, aggregate and analyze information/data of any 
kind - in this case about human beings - from different sources in order to enrich the data sets, to
finally license the resulting corpora to other organisations. [@report_2014_data-brokers]

Permission Request
: initial attempt to request permissions for accessing certain data from the *PDaaS*; third party
registers as *data consumer*

Access Request
: obtain/request actual data from the system (requires a third party to be registered as a 
*data consumer*)

Permission Profile
: a data set about a third party that already made a permission request. The set contains 
additional information and access rules
    
Data Access
: after a third party's *permission request* got reviewed and saved, that entity is then able to 
make an attempt to access data.

Endpoint
: an endpoint is defined as the part of the URI that is unique to every *data consumer*, or to be 
more precise, unique to every *permission profile*. Usually it is the first part of a URI, whereas 
following parts stand for different resources that might be available within that endpoint
It can also be seen as group of resources that all can be accessed by under specific circumstances