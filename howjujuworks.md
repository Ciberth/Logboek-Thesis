# What is Juju

## Model
## Controller
## Charms
## Hooks


# Juju in action

## Deploying a few services

In this first example we will deploy a wordpress service, a mysql service and a jenkins service from the jujucharms store. This should illustrate the different elements discussed in the sections above.

The controller that we are using stans on a vmware cluster and is called vmware-main. When using the ``juju register ...`` command a user is automatically logged in after setting up his or her password. When a user is logged out and he wants to resume his activities he needs to login: ``juju login -c vmware-main``.

To watch an overview of the current status of the controller one can use ``juju status`` or ``juju gui`` to watch everything visually in a browser.

Using the ``juju deploy <servicename>`` command will fetch a jujucharm from the store matchiing the servicename. Therefore firing up a wordpress and a mysql instance and adding a relation between the two is done as follows:
```
juju deploy mysql
juju deploy wordpress
juju add-relation wordpress mysql
juju expose wordpress
```


Thanks to the expose command we can visit the public address (get it with ``juju status``) and access the wordpress website in a browser. 



## Writing our first charm

[Source](https://jujucharms.com/docs/stable/authors-charm-writing)

We create a local charm repository.

```
mkdir -p charms/testing
cd charms/testing

charm create vanilla

```

