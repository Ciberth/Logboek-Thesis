# Formal problem statement

## Introduction

This page is meant to illustrate the goals, thoughts and questions I want to explore and work on throughout this thesis. In its simplest form this research tries to look at the possibilities and restrictions of flexibility at design time in infrastructure as a code approaches that use modelling techniques. We will focus more specifically on **Juju**. A research group at the university of Ghent created a platform (called **Tengu**) on top of Juju specifically focusing on "Big Data"-technologies. The goal of Tengu is to provide a system where data scientists can use and design models without the need of real "operations knowledge" (see below). The need for such a tool came from within the research facility but led to a central question: "Can’t we make these things way easier to setup, to configure and to use?" A lot of data scientists either don’t have the proper knowledge or the time to setup and configure technology stacks. Next to the functional approach and the technical implementation I wish to acquire opinions and real-life experiences as well on this subject. Therefore, this thesis can also be approached as a research towards modelling an infrastructure and the opinion and vision of domain experts on one side and a more technical approach and use case inside Juju on the other. If you are interested what we want to realise within juju have a look at the [As-is](asis.md) and [To-be](tobe.md). Before looking at the current possibilities and restrictions, it might be interesting to formally define some used terms and definitions.

### A virtual administrator 

To fully define the concept of a virtual administrator it might be interesting to look at our daily work cycles. These days most teams have real life administrators to perform operations on or with different services of technologies. On the other hand, there are the developers who fabricate software with the tools, applications and services setup by the administrators. The DevOps philosophy introduced awareness and techniques to bundle forces and deal with the big differences and issues between developing a piece of software and deploying/managing it. 

Imagine the use case where a group of developers (or data scientists) require some servers and applications (a database for example) for a new project. Right now, either the developer has access to a database server himself (and he needs to do everything himself) or he must properly ask an operations engineer (or DBA) to perform the steps that are necessary (setting up a server, creating users, sharing details...). The idea of a virtual administrator is not per se taking away the job of the DBA but rather creating a system, a tool, a way that focusses on performing the tasks that the developer needs with two key concepts in mind: 

- No physical other person must intervene.
- No real operations knowledge (discussed later) bust me known by the developer. 

The virtual administrator then handles all technical stuff and provides the requested services to the developer in such a way that the developer is ready to go and start carrying out his expertise without bothersome configuration issues. 


### Operations knowledge


The abstract concept of operations knowledge can be defined as the overall sum of specific technologies, their relations, configurations and limitations. This includes version numbers, technology-specific differences for the same (physical or logical) thing, configuration parameters... The following example might clarify this definition.

Imagine a team working on a webshop that sells items. The company wants to start performing some data analysis on the statistics and items of that web shop and they decide to put a team of data scientists to work. This team needs to setup their tools, configure the applications they want to use and they need to be able to get the data from the webshop. This task and how it is achieved is what falls under the domain of operations knowledge. The data scientist doesn’t really care what database (MySQL, MongoDb, Cassandra…) or back- and front-end technology is used. All he wants, is being able to retrieve the data and work with it. In other areas though he might be a bit pickier as the data scientist might request some specific version (of a tool or technology) because of some specific features. Note that the same exercise can be made with developers instead of data scientists. Developers generally don’t care if you run your webserver on a nginx- or an apache-server or whether your database server runs on a MySQL instance or a Mariadb. Just to be clear, I'm not saying that the difference between relational technologies (SQL) and non-relational ones (NoSQL) aren’t important (as these choices will be made by the team), but I am emphasizing that the exact setups, configurations and parameters of these choices often add a lot complexity that developers or data scientists don’t want to be bothered with. This flexibility to request things when having the operations knowledge and at the same time being able to work on a higher (more abstract) level that doesn't require operations knowledge is the goal we want to achieve. 



## Juju 

### What is Juju

On the [about juju-page](https://jujucharms.com/docs/stable/about-juju), Canonical, the company behind Juju and Ubuntu, describes Juju as follows:

> Juju is a state-of-the-art, open source modelling tool for operating software in the cloud. Juju allows you to deploy, configure, manage, maintain, and scale cloud applications quickly and efficiently on public clouds, as well as on physical servers, OpenStack, and containers. You can use Juju from the command line or through its beautiful GUI.

The latter is what interests us. The Juju GUI (see the image below) shows a visual representation of the different applications and how they are connected. It is basicly an undirected graph where each node represents an application (and in a lot of cases a seperate machine) and each vertex contains the relation-specific details between the two applications. 

![Juju GUI](https://jujucharms.com/static/img/jujudocs/gui2_management-status.png) 

Juju uses **charms** and **bundles** (a formal defined collection of charms and their relationships) to setup an infrastructure. Charms can be written and created in any language (including existing configuration management tools such as Chef and Puppet). The great thing about these charms is that once they are written they provide a way of setting up systems without "application-specific" knowledge (hence our defined operations knowledge). Things like dependencies, operational events like backups and updates can all be encapsulated in the charm. The stronger the knowledge of the application of a charm writer, the more options will be available and the more flexibility one can have when designing in the GUI.


### Disclaimer ("hacking our way in")

Juju is built around the idea of **application modelling**. This means that the philosophy of Juju is fundamentally different (then what we want to achieve in this research) and the focus is thus somewhat different. What we want to do in our philosophy is going a step further. We want to model "all the things" (or at least the things that seem interesting), including nodes that aren’t fully fledged applications or instances but rather entities that represent some logical item that holds some sort of information (or mostly configuration). A database or table for example on top of an application node. A small disclaimer inside this disclaimer (but more on this in the functional analysis) is where do we draw the line. Is the logical representation of a database enough or do we want the flexibility of adding nodes that represent tables or documents as well? When talking about database technologies, what do we do about users, views, stored procedures, indices...


### What about other tools

There are a few reasons why Juju is our choice when it comes down to implementing the concepts as discussed in the functional definitions. First, the charms that may or may not end up useful can be directly implemented within Tengu as Tengu is basically Juju. Secondly, using other tools means adding another layer of complexity for Juju/Tengu to handle. Note that Juju does not require one specific language to implement hooks. This allows a lot of freedom and possibilities for the charm writer. If he wants to use configuration management tools such as chef or puppet then that is entirely possible. Finally, the focus lies on the design level and in abstracting the layer that holds the operations knowledge. As of today (march 2018) no tool (besides Juju) gives the freedom to use multiple technologies on different providers (locally, amazon web services, google cloud platform…) and that give the ability to model the infrastructure you want to have with **Juju gui** (deploying nodes and adding relations). The following paragraphs illustrate some other tools and their relevance to this research.

Tools like **Bitnami's stacksmith** offer a similar idea of implementing and doing the operational work for you but it operates on another level. Stacksmith focusses on (re)packaging, maintaining and monitoring existing software stacks and deploying them in a public or private cloud. While Stacksmith also abstracts operations knowledge, it's use case does not overlap with what we want to achieve in this research.

**AWS CloudFormation** may be the closest thing to Juju when it comes to modelling an infrastructure and building it from that model which again is nothing more than a text (JSON-) file. CloudFormation also works with templates that can be reused when creating new models. Obviously CloudFormation is limited to AWS and is a service to be used (unlike to open software tools that can be installed and managed by yourself). It is however a free service offered by AWS and support is included as well.  

Another tool is Terraform by HashiCorp. Just like CloudFormation, Terraform follows a similar approach in defining configurations that describe the (target) state of your infrastructure. It then calculates the necessary steps to reach that defined target and it finally executes the changes. The nice thing about Terraform is its ability to support other cloud providers and 3rd party services next to AWS. One key feature of Terraform is the separation of a planning and execution phase. The planning phase makes it possible to analyze the resources and changes. 

An interesting link when comparing AWS CloudFormation and Terraform is [this article](https://cloudonaut.io/cloudformation-vs-terraform/) written by Andreas Wittig.

Note that AWS CloudFormation and Terraform both have the same characteristic where people using the service still need a lot of "operations knowledge", which is something we want to evade. Building layers (a model GUI for example) could be a solution. If it were not for Juju, looking at Terraform and the possibilities to add a modelling layer would be the closest thing when it comes to actual implementing our theoretical approaches.  


## Focus

As mentioned before Tengu is a tool that is built on top of Juju, therefore the functional approach and the technical implementation in this thesis are limited to Juju. In addition, a lot of thought processes discussed in this research can be useful in other areas, tools or technologies. Because of the broad scope of this subject (and time restrictions), we will limit our look to datastores, their technologies and their possible abstractions in Juju. Note that things like message brokers, queues, topics, load balancers, webservers... all have some way or another use cases where an abstraction might be useful and relevant.  

## Some research questions

- Is abstracting operations knowledge and infrastructure modelling a step forward when it comes to system design and gives it enough flexibility according to industry experts?
- Is modelling infrastructure as a code the next step in automation and configuration management? Does this offer everything data scientists and developers need when it comes to operations and infrastructure management?
- Are there tools to manage datastores on the design (visual) level and if not can charms achieve this and how?
- Would it be possible for Tengu to take the role of virtual database administrator with the researched and implemented charms in Juju? What are the limitations concerning “at design time vs at runtime”?
- Is there a need for another (not Juju) modelling framework or language that offers the flexibility to use existing tools (Juju, Chef, Puppet...) but allows everything in its models.

