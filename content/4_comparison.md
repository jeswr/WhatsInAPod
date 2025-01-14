## Comparison # {#comparison}

In this section, we examine possible interpretations of the technological underpinnings, and compared them from the viewpoints of storage, publication and query.

In this step, we compare the frameworks of thinking about Solid as a collection of resources exposed over Linked Data Platform, compared to Solid as a Knowledge Graph exposed over a [meta-]model-based interfaces (SPARQL, TPF, …).


### Managing events
For a first comparison, we take an application that works with events.
Given an event consisting of a timestamp, a type, and a message.
In the Solid ecosystem, how can an application store this data on a POD?

#### In the case of an LDP interface
the application will first need to bundle the data it wants to write into resources. In the case of events or other objects with a logical structure, the straightforward approach is to create a separate resource for each event. An alternative approach could be to bundle events in a single resource, but this comes at the cost of the granularity over which permissions can be assigned to events.

With the resources created, the application now wants to store them on a Solid pod.
If the application does not have access to a location on the pod, it can start up a negotiation protocol to receive access to write data to the pod.
With access given to the pod, the application now needs to organize the created resources in a structure that can be written over the LDP interface of data pod.

A first option would be for the application to just write all resources to a single container the application has access to, together with all other application data. Combining all data in resources in a single container means the application is not making any assumptions as to the organization of data. However, in this case, the LDP interface does nothing to help the application find previously stored data, as it either has to keep an internal state in the application of where resources were stored, or check all resources on the pod until a match is found.

Because of this, it is more interesting for applications to create structure in their data, as through these structures, applications can more easily retrieve written data. To create this structure, the application can create an `events` folder in their allocated data space, and store all event resources in this `<storage_space>/events/` location. Now any time the applications wants to read or write events, it can make the local assumption that all event related data *written by the application* will be stored at this `events` folder.

But as the application expects to be working with large amounts of event data, it might decide that further optimizations are required. For this purpose, it might want to choose to organize the data according to one of its properties such as the timestamp of the event, or the type of the event. In this case, an event might be stored as the following resource on the pod: `<storage_space>/events/2022/01/01/event1.ttl`.

We see that the events stored by the application are now structured according to the assumptions and optimizations done by the application.

From a **storage** standpoint, the event data is stored on the file system of the POD server as a file, or may be stored in a graph database where the data is tagged as part of that resource, so that the resource can be reconstructed in its entirety.

From a **publication** standpoint, all data of an event is published as a resource over the POD LDP interface on a location that is tied to the application-specific organization of data.

From a **query** standpoint, the data must first be discovered on the pod, after which it can be queried directly through dereferencing the relevant resources or by an abstraction on the client that provides access to the data over another interface.
Data discovery can happen using brute-force retrieval of all available resources, but is limited by the permissions an agent has over what can be viewed on the data pod. Additionally, public resources in private containers may lead to brute-force methods not discovering these resources and not including the contained data in their results.
Options external to the LDP interface such as the Type Indexing system [TODO:: refer]() and Shape Trees [TODO:: refer]() can optimize this process, but as of yet are not mandatory in the specifications and are not required to be managed by the server [TODO:: is this true for shape trees??]().

#### In the case of [meta-]model-based interfaces
the application is not required to do additional data modeling.
Data can be written as-is to the pod, over any available endpoint exposed by the pod.
The received data is then stored 

From a **storage** standpoint, the event data is stored in a graph-based database on the Pod server. 

from a **publication** standpoint, multiple interfaces can be used 

From a **query** standpoint













### Storing events on a Pod
A first concrete example we can give is the example of storing events on a Solid pod.
Events are a wide-spread 

We will make this comparison based on the aspects of storage of the data on the pod server, the publication of the data over the Web, and querying of data over the interface.

#### Storage



#### Publication



#### Query

**queries and APIs are separate concepts**


### Comparing interfaces
A common misconception is that a server interface should be identical to the client interface [TODO::cite and word according to Ruben blog](). 
Interfaces however can be abstracted away in a proxy (A web page abstracting an SQL interface in filters), or on the client (Comunica abstracting away a TPF server as SPARQL)[TODO::cite]().

For decentralized networks of data sources such as the Solid ecosystem, these abstractions will mainly be the building blocks for applications to work with data stored on the pods.

We pose that the interface exposing the server Knowledge Graph influences the possibilities and biases for abstractions created over them.
As the Linked Data Platform allows data to be retrieved by retrieving the resources present in the containers and resources advertised by the main index [TODO:: wording??](), consequences of the interface such as public resources in private containers not being discoverable through link traversal of the LDP index will limit the abstractions that can be created over the interface.

Where initiatives such as the Solid Interoperability Specification [TODO::cite]() work to provide answers for these use-cases, they are bound to the organization of data on a pod imposed by the Linked Data Platform specification.


### Data storage comparison
Different interfaces exposing data of a knowledge graph may require different storage solutions to access the underlying data.
Note that again here building blocks providing conversions for different interfaces on specific storage mechanisms may be implemented.

#### Linked data platform interfaces
can use multiple approaches for data storage. The original Solid introduction paper [TODO::cite]() proposed a file-based backend for the storage of non-rdf resources, where rdf resources were stored in a file-based or graph-based system dependent on the optional support for SPARQL over the resources in the pod.

#### [meta-]model-based interfaces
may require more elaborate back-end support depending on the interface exposed. As interfaces such as SPARQL and TPF interfaces rely mostly on an indexed data graph storage mechanisms[TODO:: ...](), 
Support for non-rdf resources must always be taken into account, requiring some sort of organizational structure for these resources to be setup.




### Data publication comparison
The data management interface used by a Solid pod influences how the internal data is published over the Web.
The interface directs how data can be accessed, the granularity of 


#### Linked data platform interfaces
provide a resource-based data organization organized according to the Linked Data Platform specification.

- slash semantics - bias
- data discovery difficulties (type index, interop spec, ...)


#### [meta-]model-based interfaces

- quad / view?-based
- discovery through interface index (SPARQL, TPF, ...)
- non-RDF resource metadata indexing (as in original paper)
- ...



### Data querying comparison
The organizational structure of data published over the interface influences the query resolution and optimization process.
Other differences such as the granularity with which the data can be accessed influences the querying process.

#### Linked data platform interfaces
rely for querying on their data organization, or on derived interfaces to support querying requirements.
As data discovery is hindered by local assumptions made by applications in the organization of data and their shape, either knowledge about the organization of data, or brute forcing of the available resources is required to discover and retrieve data relevant to a query.
The inclusion of optional SPARQL support in the first iterations of the Solid specification [TODO::cite]() supports this.

#### [meta-]model-based interfaces
require the client to discover the exposed interface, and adopt the querying strategy accordingly. A practical example of this is the comunica querying framework, that can adapt its querying strategy according to the datasource interface [](cite:cites Taelman_Van_Herwegen_Vander_Sande_Verborgh_2018).
Querying performance and evaluation is dependent on the exposed interface.
Assumptions on the shape of data contained in the internal knowledge graph still provide the same challenges, but the organizational structure of data should be abstracted away for clients working with and querying the data.


### How does the framing of pods as a Knowledge Graph solve the API integration problem?

In the problem statement, we pose that Linked Data Platform is a meta-API that leads to API integration problems.
How does this solve this issue?

We first pose that API's are a means of syncing data between systems [TODO:: cite](), and data retrieval will always happen over an API (even direct retrieval implicates an API, as you know the location of the resource, etc ...) [TODO:: ...]().

The problem with enforcing Linked Data Platform as the single meta-API for Solid is that biases and 

<!-- 
the APIs are just a means of syncing data between systems 
(see https://ruben.verborgh.org/blog/2021/12/23/reflections-of-knowledge/#abstracting-away-p-4) 
-> Granted but this does not really provide the comforting words that "we will change the API-integration hell into a data-integration opportunity".

cons -> need to support multiple interfaces
pros -> used interface should not have influence on the exposed data, just on how it can be accessed, as the context remains the same? -> something like this but requires some extra thinking? -->