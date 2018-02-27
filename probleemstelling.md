# Problem & Research question(s)

## Introduction 

Let's define the concept of a virtual administrator. These days most teams have real life administrators to perform operations on/with different services of technologies. Imagine the following use case. Luc is a system engineer in company X. He is the database administrator of the database server he set up in the past. Lisa is a project manager in the same company. This new project will need and use a database. Imagine that Lisa and her team don't have the knowledge to setup a database/table or they lack the rights to do so. In this scenario Lisa needs to ask Luc to configure the database/table and provide Lisa with the configurations her team needs for the new project. 

A virtual administrator is an automated piece of software that functions as an administrator and that can operates as if it was a real life human performing actions on a service (database in this case). Returning to our example we want to replace Luc with some sort of functionallity or software that would perform the same actions as Luc (so the technical implementations). In addition the entity should be accessible for Lisa. Her requirements, limitations and knowledge shouldn't change and yet the end result should stay the same. 


## What do we have now?

A lot of teams have evolved they workedflow. Modern techniques to manage infrastructure consist of using tools to automatically deploy machines, services and applications. Configuration management tools such as puppet and chef or orchestration tools like ansible make it possible to "code your infrastructure". The devops philosophy introduced awareness and techniques to deal with the big difference between developping a piece of software and actually deploying and managing it. 

Another tool that helps operation engineers is for example juju. I will quote the [juju website](https://jujucharms.com/docs/stable/about-juju):

> Juju is a state-of-the-art, open source modelling tool for operating software in the cloud. Juju allows you to deploy, configure, manage, maintain, and scale cloud applications quickly and efficiently on public clouds, as well as on physical servers, OpenStack, and containers. You can use Juju from the command line or through its beautiful GUI.

The nice thing about juju is that it focusses on the modelling aspect of linking services together. It is a moddeling tool that uses various techniques (also possibly in combination with configuration management tools) to create and configure an infrastructure. With the use of charms (self written or existing ones from the charmstore) a team can setup huge stacks of technologies very fast and without bothering about a lot of technical overhead.

There is however something these tools and techniques all have in common: they still require what I will call "implementation knowledge". Even juju requires you to know that you want a mysql server or you want an apache service.

## Implementation knowledge

Implementation knowledge can be defined as the overall sum of specific technologies, their relations, configurations and limitations. This definition might seem abstract so I'll try to explain it with an example.

Imagine the project Lisa is working on. Her team needs to create a webapp. When the team analyses the requirements and they design their solution they will probably make decisions about technologies, frameworks, languages and so on. For the sake of simplicity let's say they want to create some sort of webshop. There are 2 crucial things about a webshop: your customers (the goal audience) and the items you want to sell (the product). I hope it is clear that the team will need some sort of front- and back-end technology and a database with multiple tables (at least one for users and let's assume one for the items). Right now the team can determine what technologies they will use and in a lot of cases these choices are relevant and important but there are cases where some elements are less relevant. For example the choice between php or node is huge when it comes to developping the webapp, but imagine our company has already a database with a user table they want to use. At that moment the database technology (MySQL vs PostgreSQL for example) is less relevant. 

Implementation knowledge in this example is the sum of all used technologies, their dependancies, configurations, versions... Some things are of extreme importance such as languages and versions (node vs php) or accessability in underlying technologies (a MySQL table isn't approached the same way as a mongodb document) but other things like mongodb version, jdbc drivers... seem less relevant to a developer and shouldn't bother them. These technical details are what I call implementation knowledge.


## Simplify all the things

Some key questions that pop up when thinking about these problems are the following ones: "Shouldn't things be easier?", "Why are there so many problems when setting up project", "It works on my system, why are there problems with deploying it?", "You are operations, it's not my problem..."

The idea behind the creation and use of virtual administrators is based on these previous questions. A developer (for projects) or a data scientest for data analyses should be able to focus on the project itself. He/she shouldn't be depended on an operations engineer to get started and he/she should be able to work on a project the same way regardless of a developping, qa or production environment. 


## Concrete proposal and research in juju

In this research we will use juju and try to abstract the concept database (and table) from the services of several database technologies. When designing your stack of technologies in juju there is currently no way to represent a database, there is only your database server. When adding a relation from a service to the server a new table is created. The following code on a working juju environment (on a model linked to a controller) illustrates the **as-is situation**. If you want to visualise this setup, imagine a graph with 2 nodes connected to each other.

```

# Default charms wordpress and mysql get pulled from charm store and 2 machines get configured

juju deploy wordpress 				# basicly an apache server
juju deploy mysql 					# mysql server
juju add-relation wordpress mysql   # create a wordpress table
juju expose wordpress 				# make the server accessable


```


The current **problem** in juju is the lack of visually knowing what the mysql server has. What databases? What tables? All we know is that we used a mysql charm. If we now wanted to deploy another application linked to the same database previously created, manual intervention and a custom charm might be necessary. That's why in this first step we want to visually represent the databases and tables as well and add a relation between the application and the table and a relation between the table and the server. A direct connection between application and server should then be unnecessary. Keep in mind that the intermediate charm will be of the type "mysql database charm" and "mysql table charm".


In a next step we would want to a level higher. We will research if it possible to create a generic database charm that is linked with the intermediate "<technology> database charm" and on it's turn linked to the existing technology server charm. 


UPDATE (for reread)

[Source](https://jujucharms.com/docs/stable/interface-mysql) "When the mysql charm is notified of a “relation joined” event, it creates a database". This is an implementation choice of the mysql charm and it shows that "my model" we have no possibilities to actually make relations between databases or tables. Imagine again trying to connect 2 different webapplications to the same database.



## In action 

Let's assume we have a working bootstrapped juju configuration to work with. On this image INSERT IMAGE the juju gui is shown as accessible in the browser. Deploying juju services is as easy as running the command ``juju deploy <service>``. A lot of services are already provided on the juju charm store. In a lot of cases it might be interesting to create your own charms. In this write up we will focus on creating some small examples for charms that will be connected (a relation will be added) to charms from the charm store (mysql or mongodb for example). The first step in understanding juju is creating a very basic example. The service we want to create is nothing more then an apache server that will run a php application. The php application will have 2 files, one to read and one to write to a mysql datastore. Our second application will be a nodejs application that does the same thing. For production uses we will manually ssh to our mysql machine and create a temporary database. INSERT IMAGE After ssh'ing to the machine use ``mysql -u root -p`` to login to the mysql client. The password can be found in ``/var/lib/mysql/mysql.passwd``

Let's create a table and insert some values.

```sql
create database test;

use test;

create table items(
id INT NOT NULL AUTO_INCREMENT,
name VARCHAR(100) NOT NULL,
price INT NOT NULL, 
PRIMARY KEY (id)
); 


insert into items (name, price) values (tv, 200);
insert into items (name, price) values (laptop, 1000);


```  


The next step is creating the application. Before actually creating our charm let's run our app locally. This let's us now the "operation step(s)". This will tell us what services need to be installed and where we will need to deploy our app. These steps are the steps we will need to reproduce in the charm (using the reactive framework) in python-code.

```
sudo apt-get install apache2 -y
sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql -y

# Apache2 Ubuntu Default Page get's displayed when surfing to your own IP

sudo vim /var/www/html/info.php

<?php

phpinfo();

?>

# PHP information get's displayed when surfing to IP/info.php

```

The above steps (testing the ubuntu default page and the info.php) ensures that apache is running and php is installed.
Let us now create our php application. We will create 2 php files: a config.php that will have the configuration parameters to connect to the mysql database, a read.php to read from the mysql database and a write.php to write towards the database.

### Read.php

```php

<?php 

$db_user = "";
$db_pass = "";
$db_host = "";
$db_db = "";

$db_handle = mysql_connect($db_host, $db_user, $db_pass, $db_db) or die()

?>

```


