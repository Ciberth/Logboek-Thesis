# Formal problem statement

## Introduction

This page is meant to illustrate the goals, thoughts and questions I want to explore and work on throughout this thesis. In its simplest form this research tries to look at the possibilities and restrictions of flexibility at design time in infrastructure-as-a-code approaches that use modelling techniques, more specifically, in **Juju**. A research group at the university of Ghent created a platform (called Tengu) on top of Juju specifically focusing on "Big Data"-technologies. The goal of Tengu is to provide a system where data scientists can use and design models without the need of real "operations knowledge" (see below). The need for such a tool came from within the research facility but led to a central question: "Can’t we make these things way easier to setup, to configure and to use?" A lot of data scientists either don’t have the proper knowledge or the time to setup and configure technology stacks. Next to the functional approach and the technical implementation I wish to acquire opinions and real-life experiences as well on this subject. Therefore, this thesis can be approached as a research towards modeling (all the things in) an infrastructure and the opinion and vision of domain experts on one side and a more technical approach and use case inside Juju on the other. Before looking at the current possibilities and restrictions, it might be interesting to formally define some used terms and definitions.

### A virtual administrator 

To fully define the concept a virtual administrator it might interesting to look at our daily work cycles. These days most teams have real life administrators to perform operations on or with different services of technologies. On the other hand, there are the developers who fabricate software with the tools, applications and services setup by the administrators. The DevOps philosophy introduced awareness and techniques to bundle forces and deal with the big differences and issues between developing a piece of software and deploying/managing it. 

Imagine the use case where a group of developers (or data scientists) requires some servers and applications (a database for example) for a new project. Right now, either the developer has access to a database server himself (and he needs to do everything himself) or he must properly ask an operations engineer (or DBA) to perform the steps necessary (setting up a server, creating users, sharing details…). The idea of a virtual administrator is not per se taking away the job of the DBA but rather creating a system, a tool, a way that focusses on performing the tasks that the developer needs with two key concepts in mind: 

- No physical other person must intervene.
- No real operations knowledge bust me known by the developer. 

The virtual administrator then handles all technical stuff and provides the requested services to the developer in such a way that the developer is ready to go and start performing expertise without bothersome configuration issues. 


### Operations knowledge



The abstract concept of operations knowledge can be defined as the overall sum of specific technologies, their relations, configurations and limitations. This includes version numbers, technology-specific differences for the same (physical or logical) thing, configuration parameters... The following example might clarify this definition.

Imagine a team working on a webshop that sells items. The company wants to start performing some data analysis on the statistics and items of that web shop and they decide to put a team of data scientists to work. This team needs to setup their tools, configure the applications they want to use and they need to be able to get the data from the webshop. This task and how it is achieved is what falls under the domain of operations knowledge. The data scientist doesn’t really care what database (MySQL, MongoDb, Cassandra…) or back- and front-end technology is used. All he wants, is being able to retrieve the data and work with it. In other areas though he might be a bit pickier as the data scientist might request some specific version (of a tool or technology) because of some specific features. Note that the same exercise can be made with developers instead of data scientists. Developers generally don’t care if you run your webserver on a nginx- or an apache-server or whether your database server runs on a MySQL instance or a Mariadb. Just to be clear, I'm not saying that the difference between relational technologies (SQL) and non-relational ones (NoSQL) aren’t important (as these choices will be made by the team), but I am emphasizing that the exact setups, configurations and parameters of these choices often add a lot complexity that developers don’t want to be bothered with. This flexibility to request things when having the operations knowledge and at the same time being able to work on a higher (more abstract) level is the goal we want to achieve. 



## Juju 

### What is Juju

TODO

### Disclaimer ("hacking our way in")

Juju is built around the idea of **application modelling**. This means that the philosophy of Juju is fundamentally different (then what we want to achieve in this research) and the focus is thus somewhat different. What we want to do in our philosophy is going a step further. We want to model "all the things", including nodes that aren’t fully fledged applications or instances but rather entities that represent some logical item that holds some sort of information (or mostly configuration). A database or table for example on top of an application node.  


### What about other tools

There are a few reasons why Juju is our choice when it comes down to implementing the concepts as discussed in the functional definitions. First, the charms that may or may not end up useful can be directly implemented within Tengu as Tengu is basically Juju. Secondly, using other tools means adding another layer of complexity for Juju/Tengu to handle. Note that Juju does not require one specific language to implement hooks. This allows a lot of freedom and possibilities for the charm writer. If he wants to use configuration management tools such as chef or puppet then that is entirely possible. Finally, the focus lies on the design level and in abstracting the layer that holds the operations knowledge. As of today (march 2018) no tool (besides Juju) gives the freedom to use multiple technologies on different providers (locally, amazon web services, google cloud platform…) and that give the ability to model the infrastructure you want to have with **Juju gui** (deploying nodes and adding relations).

**AWS CloudFormation** may be the closest thing to Juju when it comes to modelling an infrastructure and building it from that model which again is nothing more than a text file. CloudFormation also works with templates that can be reused when creating new models. Obviously CloudFormation is limited to AWS and is a service to be used (unlike to open software tools that can be installed and managed by yourself). Note that AWS CloudFormation has the same characteristic where people using the service still need a lot of "operations knowledge", which is something we want to evade. 

Tools like **Bitnami's stacksmith** offer a similar idea of implementing and doing the operational work for you but it operates on another level. Stacksmith focusses on (re)packaging, maintaining and monitoring existing software stacks and deploying them in a public or private cloud.

## Focus

As mentioned before Tengu is a tool that is built on top of Juju, therefore the functional approach and the technical implementation in this thesis are limited to Juju. In addition, a lot of thought processes discussed in this thesis can be applied to a lot of different operational stuff. We will however only look at datastores, their technologies and their possible abstractions. Note that things like message brokers, queues, topics, load balancers, webservers… all have some way or another use cases where an abstraction might be useful and relevant.  