# PoC actions and manager for generic databases

To understand the thought process take a look at the following workflow. Note that in this example both databases are stored on the mysql-service (machine) and nowhere else. 

```bash

# deployment of 2 charms (mysql from charmstore)

juju deploy mysql
juju deploy generic-db-manager

# this relation should prepare the db-manager and give possibility to create mysql databases

juju add-relation mysql generic-db-manager

# charm that deploys a website (imagine some php pages) on an nginx or apache

juju deploy webshop
juju expose webshop 

# create the actual databases

juju run-action generic-db-manager create name=webshop-users type=mysql
juju run-action generic-db-manager create name=webshop-items type=mysql

# this action should start a subordinate in generic-db-manager service 
# as a result each subordinate represents a database

# adding relations so that webshop can use the database

juju add-relation webshop db-subordinate-users
juju add-relation webshop db-subordinate-items

# if now a second application wants to access the same database this becomes possible
# imagine webshoptool being another website (node) making it possible to fill the items database and tables

juju deploy webshoptool
juju expose webshoptool

juju add-relation webshoptool db-subordinate-items


```

If we visualise this we get something like this:

![action poc](/images/actionspoc.png)

Note that this offers flexibility:

- When supported the type parameter can be technologyspecific but could also support sql
- Extra parameters could enforce requests
- **!! Multiple applications can use the same database !!**


Biggest problems: 

- Is it possible to create/deploy subordinates (or normal charms) inside an action?
- The database nodes hold some information but are not really services 