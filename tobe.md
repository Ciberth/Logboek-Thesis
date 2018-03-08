# The TO-BE situation 

This "To-Be" situation is an iterative step by step process of abstracting and implementing charms in Juju. The idea of abstracting 1 thing and slowly adding more support and functionality allows us to focus on one thing, and one thing only before trying to go on to the next step. This page shows a summary of the different steps. On the other pages these steps are discussed in more detail. 

## Step 1

![TO-BE step 1](/images/tobe1.svg)

The first step adds the intermediate node "SQL Database". The functionality of this node is rather minimal. The idea is that this node get's a relation that requests information about a SQL-database. The node then handles this by it's relation to the MySQL charm and shares the configured parameters and information back to the original charm (the application).  

## Step 2

![TO-BE step 2](/images/tobe2.svg)

Step two focusses on implementing the PostgreSQL and providing support for both. The application asks some type of relation and the "SQL Database" node needs to properly handle this and make sure the right charm performs the right actions.

(An interesting approach in this step is to research how other nodes (a phpmyadmin application, a backup-node) can work with this "SQL Database" charm.) 

## Step 3

