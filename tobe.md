# The TO-BE situation 

This "To-Be" situation is an iterative step by step process of abstracting and implementing charms in Juju. The idea of abstracting 1 thing and slowly adding more support and functionality allows us to focus on one thing, and one thing only before trying to go on to the next step. This page shows a summary of the different steps. On the other pages these steps are discussed in more detail. As you can see one of the key elements is reusing the charms that exist on the charm store!

## Disclaimer

This to-be representation is part of a research. It does not claim to be better than the existing approaches. Feel free to leave a comment [here](https://github.com/Ciberth/Logboek-Thesis/issues/10) I would love to hear your thoughts.

## Step 1

![TO-BE step 1](/images/tobe1.svg)

The first step adds the intermediate node "SQL Database". The functionality of this node is rather minimal. The idea is that this node get's a relation that requests information about a SQL-database. The node then handles this by it's relation to the MySQL charm and shares the configured parameters and information back to the original charm (the application).  

## Step 2

![TO-BE step 2](/images/tobe2.svg)

Step two focusses on implementing the PostgreSQL and providing support for both. The application asks some type of relation and the "SQL Database" node needs to properly handle this and make sure the right charm performs the right actions.

(An interesting approach in this step is to research how other nodes (a phpmyadmin application, a backup-node) can work with this "SQL Database" charm. And related (as a step 4) have a look at defining users/views/stored procedures/...) 

## Step 3

![TO-BE step 3](/images/tobe3.svg)

Step three tries to do 2 things. It focusses on creating a new type of node called "Abstract Database" that has relations to the other (less abstract) "Technology Database" charms (such as the "SQL Database" charm from step 1 and 2). 