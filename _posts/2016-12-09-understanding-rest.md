---
title: Understanding REST and MVC
---

I need to learn Spring.io and, in order to do that, I need to understand the REST architecture (and some other bits).

This is my note-taking on the subject, for my own study. My sources are listed [here](#resources).

# Contents

* [What is REST](what-is-rest)
    * [Constraints](constraints)
        * [Uniform Interface](uniform-interface)
        * [Stateless](stateless)
        * [Cacheable](cacheable)
        * [Client-Server](client-server)
        * [Layered System](layered-system)
        * [Code on Demand](code-on-demand)
    * [Principles](principles)
    * [HTTP Methods](http-methods)
        * [Definitions](definitions)
        * [Methods](methods)
    * [HTTP Status Codes](http-status-codes)
* [MVC (Model-View-Controller)](mvc)
    * [Components](components)
    * [Interactions](interactions)
    * [Flow of Information](flow-of-information)
* [Resources](resources)


# What is REST

REST (Representational State Transfer) is an architectural style for designing distributed systems.

## Constraints

### Uniform Interface

Four guiding principles define the interface between clients and servers. They simplify and decouple the architecture.

* Resource-Based
	* Individual resources are identified in requests using URIs (Uniform Resource Identifier).
	* Resources are conceptually separate from the representations returned to the client. **EX:** The database isn't returned, instead some HTML/XML/JSON that represents those records expressed in a certain way based on details of the request and server implementation.
* Manipulation of Resources Through Representations
	* When a client holds a representation of a resources, including any metadata, it has enough information to modify or delet the resource on the server.
* Self-descriptive Messages
	* Each message includes enough information to describe how to process the message.
* Hypermedia as the Engine of Application States (HATEOAS)
	* Clients deliver state via body contents, query-string params, request headers, and the requested URI.
	* Services deliver state via body content, response codes, response headers.
	* Where necessary, links are contained in the returned body/headers to supply the URI for retieval of the object or related objects.

### Stateless

* The necessary state to handle the request is contained within the request itself (as part of the URI, query-string parameters, body, headers).
* The URI uniquely identifies the resource. The body contains the state/state change of that resource.
* After the server does processing, the appropriate state are communicated back.
* Client must include all info for the server to fulfill the request, resending state as necessary.
* Enables greater scalability since the server does not have to maintain/update/communicate state.

* **State (App State)** - That which the server cares about to fulfill a request. Could vary by client and per request.
* **Resource (Resource State)** - The data that defines the resource representation (such as data in the database). Constant across every client.

### Cacheable

* Clients can cache responses.
* Responses must define themselves as cacheable or not to prevent clients reusing stale or inappropriate data in further requests.
* Well-managed caching eliminates some client-server interactions.

### Client-Server

* Separation of concerns.
* Clients not concerned with data storage. Portability of client code improved.
* Servers not concerned with user interface/state. Servers simpler and scalable.
* Servers/clients may be replaced/developed independently as long as interface is not altered.

### Layered System

* Client cannot ordinarily tell whether it is connected directly to the end server or intermediary.
* Intermediary servers may improve system scalability.

### Code on Demand

* The **only** optional constraint.
* Servers are able to temporarily extend or customize the functionality of a client by transferring logic to it to execute (Java applets, client-side scripts, etc.).


## Principles

* **Resources** expose easily understood directory structure URIs.
* **Representations** transfer JSON or XML to represent data objects and attributes.
* **Messages** use HTTP methods explicitly.
* **Stateless** interactions store no client context on the server between requests. State dependencies limit and restrict scalability. The client holds session state.

## HTTP Methods

### Definitions

* **Idempotent** - Same results regardless of how many times requested.
* **Safe** - Do not modify resources.

### Methods

**CRUD** operations - Create, Read, Update, Delete.

* **POST (Create)** - Request that the resource at the URI do something with the provided entity. Not idempotent and not safe.
* **GET (Read)** - Retrieve information. Requests can also be partial or conditional. Idempotent and safe.
* **PUT (Update/Replace)** - Store an entity at a URI. Can create or update. Idempotent but not safe.
	* Replaces an existing entity. If only a subset of data elements are provided, the rest will be replaced with empty or null. Idemp
* **PATCH (Update/Modify)** - Update only the specified fields of an entity at a URI. Idempotent but not safe.
* **DELETE (Delete)** - Request that a resource be removed when next available.

## HTTP Status Codes

Also see [this](http://www.restapitutorial.com/httpstatuscodes.html) full page on them.

* **1XX** - Informational
* **2XX** - Success
* **3XX** - Redirection
* **4XX** - Client error
* **5XX** - Server error

# MVC

## Components

* **Model** - Directly manages the data, logic, and rules of the application.
* **View** - Any output representation of information, such as a chart or a diagram.
* **Controller** - Accepts input and converts it to commands for the model or view.

## Interactions

* **Model** - Stores data that is retrieved according to commands from the controller and displayed in the view
* **View** - Generates new output to the user based on changes in the model.
* **Controller** - Sends commands to the model to update the model's state. Also sends commands to its associated view to change the view's presentation of the model.

## Flow of Information

1. **User** makes a request received by the **Controller**.
2. The **Controller** examines the request and sends it to the **Model** requesting the appropriate information.
3. The **Model** gets the information (from a database or wherever it's stored), applies filters or logic, and returns the data to the **Controller**.
4. The **Controller** selects the appropriate **View** for displaying this information and updates it.
5. The **View** then returns to the **Controller** which it then displays to the **User**.

# From Here

Now that I've gotten going on this, I'm going to jump head-on into Spring.io and learn how to build restful applications. I'll start by going through the [Spring guides](http://spring.io/guides) an various other sources and documenting what I learn or go through there.

----------------------------------------

# Resources

* [Spring.io: REST explained](http://spring.io/understanding/REST)
* [REST API Tutorial: What is REST?](http://www.restapitutorial.com/lessons/whatisrest.html)
* [REST API Tutorial: HTTP Methods](http://www.restapitutorial.com/lessons/httpmethods.html)
* [REST API Tutorial: HTTP Status Codes](http://www.restapitutorial.com/httpstatuscodes.html)
* [Wikipedia: MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
