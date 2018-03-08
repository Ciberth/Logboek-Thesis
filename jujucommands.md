# Juju cheat sheet

[Command Reference](https://jujucharms.com/docs/stable/commands)


|           **Command**                    |             **Description**              |
|:-----------------------------------------|:-----------------------------------------|
| ``juju login -c <controller> -u <user>`` | Login to controller as user              |
| ``juju status``                          | Get an overview                          |
| ``juju gui``                             | Open gui (browser)                       |
| ``juju deploy mysql``                    | Deploy (default) mysql charm             |
| ``juju add-relation wordpress mysql``    | Add relation between 2 services          |
| ``juju expose wordpress``                | Expose a service                         |
| ``juju remove-application mysql``        | Remove application                       |




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

juju deploy local:/mysimplephpapp

```

## Adding ssh-keys

To use the command ``juju ssh <machinenr>`` you first have to add a public key to juju. You can do this with ``juju add-ssh-key <public key>``. Generating a keypay can with the command ``ssh-keygen -t rsa``.

## Retrieving the password of MySQL 

```bash
mysql -u root -p`sudo cat /var/lib/mysql/mysql.passwd`

```