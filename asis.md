# The AS-IS situation 

## Step 0

![AS-IS](/images/asis.svg)

Juju (cli) has a lot of power and it allows ad hoc monitoring (juju status) and intervening (juju ssh or juju debug-hooks) for people with operations knowledge. Our focus lies in the modelling aspect of Juju as visually representing applications in the form of an undirected graph helps people understand (and design) how different applications work together. As of today, a person (developer, data scientist) who has access to a model (let's assume juju is installed and bootstrapped correctly by an operations engineer), can look up at the charm store, select what he needs, adds the relations between the charms and deploy the entirety of the technology stack he wanted without having to deal with any setup, command, or configuration whatsoever. There are however some limitations!

### Relations across models

Imagine that your operations team deployed a MySQL charm on one of their models. They configured some fancy tools to monitor that specific machine for security and maintenance purposes, therefore they ask that when you (as a scientist or developer) need a specific database, that you only use that specific machine. 

Juju allows **cross model relations** so this is perfectly possible, but the Juju GUI currently (March 2018) has no proper of visualizing this.

### Charm specific handling and the use of multiple databases

The responsibility of correctly handling relations lies with the charm (and therefore with the developer of the charm of that specific application or technology). Use cases are not always the same so it might happen that the implementation of a relation, might not be completely what you want. 

Imagine that you have an application (e.g. a webshop) that adds a relation to a mysql charm. When that relation is established the mysql charm creates a new database and provides (on the relation) the connection parameters to the application. Perfect, job well done! But imagine that your team creates a completely new webapplication in another technology but that needs access to the same database that was created for the original application (the webshop). If you add a relation from the new webapplication the mysql charm then a new database will be created and the webapplication won’t retrieve the connection details from the other database. Juju GUI can’t solve this problem as there are no charms that represent databases but only charms that represents applications.


The above examples show that Juju operates on the application modelling level (and encapsulates all operations knowledge) and does that quite well but there are use cases where this approach still requires manual intervention, configuration as the only nodes we have access to are applications.

On the [To-be page](tobe.md) we try to model more than only applications but also logical entities to answer to these limitations. 


