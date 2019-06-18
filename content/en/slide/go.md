+++
date = "2018-08-22T13:10:52-05:00"
description = "Government service delivery modernization via Cloud Native principles."
draft = false
language = "en"
tags = ["cloud ops", "cncf", "protoc", "protocol buffers", "grpc"]
title = "GoC Cloud Native"
type = "slide"
+++

<!-- .slide: id="presenters" data-transition="concave" -->

# Presenter(s)

> Modernizing app delivery for Canadians

<div class="col-xs-6 col-sm-6 col-md-6">

<ul class="list-unstyled">
<li>William Hearn</li>
<li>@sylus <i class="fa fa-github"></i></li>
</div>

<div class="col-xs-6 col-sm-6 col-md-6">
<ul class="list-unstyled">
<li>Zachary Seguin</li>
<li>@zachomedia <i class="fa fa-github"></i></li>
</div>

Note:

Will:

I will be doing my best to explain all of this but for where I fall short (and I most certainly will). I have my amazingly knowledgable and good friend Zach here to assist.

Zach:

Actually it was Zach who got me interested in Proto buffers / gRPC based on a really cool project he is working on.

Not my place to talk about it but one of my personal goals for this year is to get him to do a presentation as I know it will blow everyone away.

Anyways for now can think of us a tag team so he might freely chime in and elaborate upon things that I will miss.

___

<!-- .slide: id="presenters-gopher" data-transition="concave" -->

# Gophers

<div class="col-xs-6 col-sm-6 col-md-6">
  <img src="https://storage.googleapis.com/gopherizeme.appspot.com/gophers/64993e4fd1e3008ad453690af0c21e8fb6776215.png">
</div>

<div class="col-xs-6 col-sm-6 col-md-6">
  <img src="https://storage.googleapis.com/gopherizeme.appspot.com/gophers/0c9f1682a737032df29e6d5eb727a7448466009d.png">
</div>

Note:

I encourage you all to generate your own gopher avatar and embrace the fanboy/girl-ism in being part of the Go community.

I personally say the best community of users out there is the Kubernetes one, but in the venn diagram that falls smack in the middle of the great golang community.

---

<!-- .slide: id="agenda" data-transition="concave" -->

# Agenda

* Go basic overview
* Protocol Buffers
* gRPC
* Roundtable
* Q&A

Note:

So in this talk I will go over very quickly some basic golang which will just be enough to go over our upcoming code example demonstrating these concepts.

We will then go over Protocol Buffers with discussions on where and when you can use them.

The Protocol Buffers discussion lays the foundation for the one surrounding gRPC (Remote Procedure Call) itself.

Finally will be having a roundtable talk about future sesssions and ways to increase user engagement as I personally know others doing cool things.

---

# Protocol Buffers

<!-- .slide: id="protobuff" data-transition="concave" -->

![protoc](/digital-academy/images/golang/protocolbuffers.png)

Note:

Protocol Buffers is all about how we can (de)serialize structured data and transmit said data over a wire or for storage data.

That could include any scenario involving persistence (disk, blobs in a database, etc), transfer (sockets, files, etc), or simply in-memory storage (snapshots, memento, deep-clone).

In technical terms.

> This is accomplished through an `IDL` interface description language that describes the structure of some object and a program that generates the source code from the IDL for generating or parsing a stream of binary data (bytes) that represent the structured data.

Perhaps the most helpful way to think of Protocol Buffers is that it defines a kind of  `API contract` upon which all criteria must exactly match up.

___

<!-- .slide: id="protobuff-background" data-transition="concave" -->

# Background

* Built by google (open source)
* Platform agnostic
* Static code generator
* (De)Serialization API in multiple languages
* Performance

Note:

Protocol Buffers are widely used at `Google` for the storing and interchange of all kinds of structured information.

This method serves as a basis for a custom remote procedure call `RPC` system that is used for nearly all inter-machine communication at Google `gRPC`.

Google has also provided a `static code generator` for multiple languages 10+ which mentioned in upcoming diagram under an OS license.

The design goals for Protocol Buffers emphasized simplicity and performance. In particular, it was designed to be smaller and faster than XML.

Messages are serialized into a binary wire format and streamed across platforms which is a compact, forward and backward compatible, but not self describing.

Though the primary purpose of Protocol Buffers is to facilitate network communication, its simplicity and speed make Protocol Buffers an alternative to data-centric C++ classes and structs, especially where interoperability with other languages or systems might be needed in the future.

___

<!-- .slide: id="protobuff-video" data-background="http://media.giphy.com/media/dbtDDSvWErdf2/giphy.gif" -->

# Caution

___

<!-- .slide: id="protobuff-when-not" data-transition="concave" -->

# When not to use

* Data is consumed by a browser
* Only JS architecture (Unless HTC)
* 300kb < protobuf < 1m

Note:

Something I should add at this point:

* Context matters and there is no one size fits all `json` vs `gRPC`
* Always have to approach in a very objective matter
* Test against benchmarks / requirements
* How big is storage and how fast can it transmit

In fact there are a few scenarios where you would not want to use Protocol Buffers:

* Browsers use JSON extremely well and are natively optimized for it.
* Native binding is extremely powerful in JavaScript.
* Protobufs excels at a very specific filesize: 300kb < protobuf < 1m
* Proto buffers edge out but negligible at 30kb diff, will matter at scale

Another consideration but I would potentially mark as less of a blocker is that for XML:

* You would sacrifice human readability/editability which is nice in many scenarios
* XML has an interesting tooling by way of xslt, xsd, sax, etc

___

<!-- .slide: id="protobuff-when" data-transition="concave" -->

# When to use

* High throughput computing (HTC)
* Bandwith is at a premium (IoT)
* Microservices spanning multiple languages
* Legacy API (XML)

Note:

Protocol Buffers performs at the hyperscale and is particularly useful for organizations where `bandwidth` is at a premium.

For examples one can think of always on busy systems like datacentres or even to the burgeoning IoT space.

Additionally being a `binary` (rather than text) protocol, Protocol Buffers are noticeably cheaper to process. This results in better use of memory and disk cache along with faster copying / moving due to being smaller on the wire.

Based on several published benchmarks for XML, Protobuf is 3 to 10 times smaller and is about 20x to 100x faster.

This is why sometimes it can also make sense to leverage for web sockets and concurrent connections where resource load is high.

___

## Protoc

<!-- .slide: id="protobuff-protoc" data-transition="concave" -->

![protoc](/digital-academy/images/golang/codeblock.png)

Note:

Before starting on this diagram should mention that Protobuf is both `version-tolerant` and `cross-platform` and that the diagram represents a valid schema.

We declare our syntax to be `version: proto3`.

We then declare our messages which are analogous to `types` and represent `data structures` used later in the `service(s)` methods.

Some specific things that I would like to highlight:

* The field level versioning where various types are assigned a unique id
* The use of collections through the `repeated` keyword
* Note that there is really no metadata one of the reasons pb is so powerful

Finally, the `service` block declares the remote methods, with the rpc keyword, that make up the service.

For each of the above unary methods they send a single request and recieve a single response.

___

# Arch

<!-- .slide: id="protobuff-arch" data-transition="concave" -->

![protoc](/digital-academy/images/golang/process.png)

Note:

Lets say you have a go service and you want to transmit stuff to a client further down the line.

One of the things is that your go service will call an API, go API that will then serialize it into a binary format and transmit over the wire.

This hits a NodeJS server or whatever other type of microservice you might have. This is where PB are most powerful as this packing right here is very dense, as in a lot of smal files sent over the network.

So NodeJS srvers get it, calls the API, (de)serializes into the JS objects, and then does what it needs to do before sending off to the client.

---

<!-- .slide: id="grpc" data-transition="concave" -->

# gRPC

> Is there tool with the Linux philosophy kept in mind?

![gRPC](/digital-academy/images/golang/grpc.png)

Note:

GRPC is A universal open-source RPC framework designed to create efficient and fast polyglot services with usage ranging from datacenter scale computing to bandwidth-constrained devices.

It adheres to the Linux philosophy of writing focused tools that do one thing well and interoperate.

For me personally I like to think of gRPC as something that makes problems in distributed computing more boring. Particularly when all we want is interoperability between components in a simple / efficient manner.

Of note, Kubernetes itself leverages gRPC for the large majority of its components:

* Communication with API server, controller, and client #25738
* Most of the other internal components
* How the `CSI` Container Storage Interface talks to storage platforms

In general gRPC is the IPC for modern cloud applications:

* Efficiency (Happier users, data centre savings)
* As software architectures are rapidly evolving helps you to keep pace
* Cloud native, componentizaton, and interopability
* Microservices, containers, scaling, failures, and continuous delviery

___

# gRPC: On the wire

<!-- .slide: id="grpc-wire" data-transition="concave" -->

* HTTP/2 for secure/fast multiplexed transport
* Leverages Protocol Buffers
* Clients open 1 long connection to gRPC server
* Allows simultaneous in-flight RPC calls
* Allows client/server side streaming

Note:

gRPC is built on top of HTTP/2, which allows for client-side and/or server-side streaming.

* Allows you to break free from the call-and-response architecture necessitated by HTTP/JSON
* Incredibly efficient in terms of reusing http connections
* Addresses the "head of line blocking" issue
* Whereby lowers the time to first display data by streaming results as soon as they become ready on the server (server-side streaming)

Leverages Protocol Buffers to vastly improve (de)serialization times, some benefits I haven't mentioned earlier.

The more important benefit comes from the explicit format specification of proto:

* Clients receive strongly typed objects rather than free-form JSON
* Auto-completion in their IDEs
* Type-safety if their language supports it
* Enforced compatibility between clients and servers with differing versions

Multiple connection options available:

* Unary
* Server/Client side streaming
* Bi-Directional streaming

___

# gRPC: Extensibility

<!-- .slide: id="grpc-extensibility" data-transition="concave" -->

* Extensible middleware API
* Tracing using interceptors (Jaegar)
* Pluggable authorization
* HTTP stream taps for rate limits
* Limit size for both request and response

Note:

Extensible middleware API for authentication, authorization, trading, service policy:

* Intercept requests to implement retry / backoff logic
* Add tracing using interceptors using tools such as Jaegar / OpenTracing
* HTTP stream taps to implement rate limits
* Support for pluggable authorization
* Ability to limit size for both request and response

---

<!-- .slide: id="crcp" data-background="https://media.giphy.com/media/MWy1jCkUs6zbW/giphy.gif" -->

# Curious...

___

## CRCP

<!-- .slide: id="crcp-overview" data-transition="concave" -->

![protoc](/digital-academy/images/golang/arch.png)

Note:

The "Curiously Reoccurring Communications Pattern" is aptly named because is a non obvious answer to important architectural questions.

People keep reinventing these very same patterns over the years only to discover that they are keep coming back to the same solution.

Patterns like these can offer a valuable roadmap to help new beginner architects like me to overcome common challenges.

The `CRCP` is a large scale communications pattern found in the architectural fabric of many distributed applications.

* Frontend components communicate with the backend using a RESTful service interfaces (jsonAPI)
* Synchronous backend activities are performed using Remote procedure call (thrift, gRPC, custom...)
* Sometimes the core of the backend communicates asynchronously over a messaging fabric (Kafka, CNCF NATS)

Each communications technology offers the perfect blend of features and function for the subsystem in which it is found.

___

## RESTful Outside

<!-- .slide: id="crcp-outside" data-transition="concave" -->

* Operates over the Internet
* With browser/proxy caches all over world
* Platform level directives (headers)
* Application level directives (verbs, URL, body)
* Native browser functionality

Note:

Like perhaps most modern systems, the `outside` part of a CRCP system operates over the Internet and makes use of the `REST` architectural pattern.

RESTful APIs extract maximum value from the underlying and ubiquitous HTTP protocol.

* Free use of browser and proxy caches all over the world
* HTTP also provides RESTful services with clean separation between platform level directives (headers) and application level communications (verbs, IRIs, status, and bodies).
* Drop in HTTP/2 and the whole thing goes a lot faster at no extra charge (technical or otherwise).
* Authentication schemes
* Powerful HTTP aware gateways
* Native browser optimizations

If you want to leverage the global infrastructure of the web no better choice than REST for your API.

RESTful interfaces exhibit a Resource-Oriented Architecture (ROA), being decomposed into resources and operations on those resources, typically making these APIs easier for developers such as myself to work with.

Again I have to plug jsonAPI as I have found it extremely flexible in its use.

___

## RPC Inside

<!-- .slide: id="crcp-inside" data-transition="concave" -->

* Refined services
* Improved Application Migration
* Performance
* Efficient containerization

Note:

The world changes considerably when we enter the wonderous realm of the backend service.

Whether your in the cloud or in a traditional on-premises data center, the nature of application decomposition in the backend tries to gear towards:

* Smaller and smaller services
* Fewer bits of web infrastructure moving about
* A single organizational view of your IPC

If you take away the web and the need for cross-organizational adoption you take away much of the RESTful value proposition.

## Application Migrations

Another consideration in a modern cloud native environment is application migration.

For example if you are moving from a large, monolithic, traditional system to microservices, odds are that your monoliths might not have REST APIs internally; rather they have functions and methods.

Monolith functions and methods can be readily repackaged as RPC services in short order.

However, migrating the same interface to a resource-oriented API environment like REST can sometimes be a significant engineering undertaking impacting clients and servers.

## Performance

Perhaps the most important point worth considering is the heightened need for performance on the backend.

Microservice oriented systems, in particular, are likely to require many backend calls to satisfy a single frontend request.

> For example, Netflix has noted in talks, that in one analyzed setting, each Internet call typically triggers 6-7 backend calls. Whether the number is 3 or 20, latency in the call chain could quickly add up.

The cumulative latency of these inside services could become a user experience problem if not managed. Fortunately, high-performance Remote Procedure Call (RPC) systems are available to address this concern.

## Landscape

CNCF’s gRPC and Apache’s Thrift are both cross-platform RPC systems and both are regularly clocked at rates an order of magnitude faster than the functionally equivalent service using a REST interface.

These “Modern RPC” systems also support interface evolution, allowing you to add methods and parameters without rebuilding old clients.

Both also support cross-language calls, supporting every programming languages in widespread commercial use today.

Nearly all of the hyperscale firms have a history of RPC innovation and adoption.

* Google invented Protocol Buffers (the serialization system under gRPC)
* Facebook followed with Thrift (now Apache Thrift),
* Twitter created the Scala based Finagle system (which can operate over Thrift)

Neither gRPC nor Apache Thrift requires an application server, instead, they offer integral lightweight RPC servers in each of the languages they support.

Application servers offer many valuable features but in a world where services are atomically packaged and deployed, perhaps multiple times on the same node, placing an entire application server in a container to host one small microservice can amount to undesired overhead and additional latency.

## Modern RPC in a cloud native system backend

Whether your priority is:

* Monolith migration
* Low latency
* Efficient containerization

Modern RPC solutions can assist you with these requirements.

___

## Messaging

<!-- .slide: id="crcp-messaging" data-transition="concave" -->

* State deltas
* Improved debugging
* Kafka / NATS

Note:

In many applications, things at some point stop being synchronous and no longer occur simultaneously.

For example:

* Mobile user submits an order for 100 shares of Tesla to a trading system
* Validating the order and enriching it may occur in the synchronous RPC space
* Sending the order to a stock market and waiting for it to execute takes place in the background

Decoupling subsystems with widely varying processing times is a job for messaging.

For those that are interesting a small group of cluster based, cloud native messaging platforms have found their way into next-generation applications.

* Apache Kafka
* CNCF’s NATS

Both are high profile examples of messaging systems that have proven to be able to scale to the level demanded by large microservice systems.

Sometimes they can also be referred to as the “central nervous systems” for their respective applications.

Messages can also be captured and replayed to:

* repro bugs
* runs tests
* train ML systems

Messaging also allows us to embrace "event sourcing" where each message represents a new element of system state and the state deltas can be distributed to a wide range of service that may want to act on them independently.

---

<!-- .slide: id="cobra-video" data-background="https://media.giphy.com/media/11WeFhbmZzHHS8/giphy.gif" -->

# Cobra

Note:

Here am I going to quickly mention one of my favorite tools which is Cobra.

> A Commander for modern Go CLI interactions

___

<!-- .slide: id="cobra-cli" data-transition="concave" -->

# Cobra CLI

* go get -u github.com/spf13/cobra/cobra
* cobra init
* cobra add task

Note:

Cobra bills itself as both a library for creating powerful modern CLI applications as well as a program to generate applications and command files.

In fact many of the most widely used Go projects are built using Cobra including:

* Kubernetes
* Hugo
* etcd
* Docker
* OpenShift
* Delve

Cobra provides:

* Easy subcommand-based CLIs: app server, app fetch, etc.
* Fully POSIX-compliant flags (including short & long versions)
* Nested subcommands
* Global, local and cascading flags
* Easy generation of applications & commands with cobra init appname & cobra add cmdname
* Intelligent suggestions (app srver... did you mean app server?)
* Automatic help generation for commands and flags
* Automatic help flag recognition of -h, --help, etc.
* Automatically generated bash autocomplete for your application
* Automatically generated man pages for your application
* Command aliases so you can change things without breaking them
* The flexibility to define your own help, usage, etc.
* Optional tight integration with viper for 12-factor apps

---

<!-- .slide: id="vscode" data-transition="concave" -->

# VSCode

* Tight golang integration
* Debugging with Delve
* Build, Lint, and Vet

Note:

Language features like:

* IntelliSense
* Code navigation
* Symbol search
* Bracket matching
* etc

---

<!-- .slide: id="demo" data-transition="concave" -->

# Demo

* Text to speech example
* [Just for Func](https://www.youtube.com/playlist?list=PL64wiCrrxh4Jisi7OcCJIUpguV_f5jGnZ) video series

Note:

With the background information now disseminated, it is time to take a look at a hopefully simple demo, based on the work done in the `Just for Func` video series that I would highly recommend to anyone interested in learning golang.
