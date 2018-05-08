# Prototyping 

## Basic idea & Overview

There are 3 components we will create in this use case. A consumer application, the generic-database interface and a generic database charm. I try to write everything down from a lifecycle perspective and at the bottom of this page the final code is put together. 

The 3 repo's:

- [Consumer-app](https://github.com/Ciberth/consumer-app)
- [Generic-database-interface](https://github.com/Ciberth/generic-database-layer)
- [Generic-database charm](https://github.com/Ciberth/generic-database)

## Consumer app (charm)

The consumer application in this use case is the idea of a webshop. This charm will use the apache layer, and install some php-pages that display or modify database information for example. 

## Generic database layer 

This is the interface layer that will connect the consumer app and the generic database. Thanks to this layer charms can use "generic-database" in the requires/provides part of the metadata.

## Generic database (charm)

This is a charm that represents a database. In this use case it will also use the apache layer for debugging purposes. This charm provides "generic-database" and requires databaseservices such as for example "postgresql" and "mysql". Note that (in this use case) the generic database charm represents 1 database only, so once a technology is requested/chosen all new incoming relations receive the chosen technology and the data.

## Workflow of this use case

The appropiate order of juju deploys command looks like this:

```bash

juju deploy generic-database
juju deploy mysql
juju deploy postgresql

juju add-relation generic-database mysql
juju add-relation generic-database postgresql:db

juju deploy consumer-app

juju add-relation consumer-app generic-database

juju expose consumer-app

# optional expose generic-database
```

The add-relation between consumer-app and generic-database triggers a cascade of actions by the framework and the system. First of all the consumer-app requests a database to the generic-database charm:

### consumer-app

```python

@when('generic-database.joined')
def request_db()
	endpoint = endpoint_from_flag('generic-database.joined')
	endpoint.request("postgresql")
	status_set('maintenance', 'Requesting postgresql')

``` 

This results in the generic-database interface layer to have:

### requires.py

```python
class GenericDatabaseClient(Endpoint):
	
	def request(self, technology):
		to_publish['technology'] = technology

```

### provides.py

```python
class GenericDatabase(Endpoint):

    @when(endpoint.{endpoint_name}.changed)
    def _handle_changed(self):
        technology = self.all_joined_units.received['technology']
        if technology:
            # flag = '{endpoint_name}.technology.requested'

            flag = '{endpoint_name}.' + technology + '.requested'
            set_flag(self.expand_name(flag))

    def technology(self):
        return self.all_joined_units.received['technology']
```


### generic-database charm

```python
@when('pgsqldb.connected','gdb.postgresql.requested')
def postgresql_db_setup(pgsql):
    pgsql.set_database('dbname')
    status_set('maintenance', 'requesting pgsql db')

@when('pgsqldb.master.available', 'gdb.postgresql.requested')
def share_db_details(pgsql):
    # Fill generic_database object with information
    # of
    # pgsql.master['password'], user, dbname, port available here 
    # but how ?
    clear_flag('gdb.postgresql.requested')
    set_flag('gdb.postgresql.available')   
 
```

### requires.py

```python
def password(self):
    return self.all_joined_units.received['password']

def user(self):
    return self.all_joined_units.received['user']

def dbname(self):
    return self.all_joined_units.received['dbname']

def port(self):
    return self.all_joined_units.received['port']

```

### consumer-app

```python
# is this changed?
@when('generic-database.changed')
def render_postgresql_config(gdb):   
    render('gdb-config.j2', '/var/www/consumer-app/gdb-config.php', {
        'db_pass': gdb.password(),
        'db_host': gdb.dbname(),
        'db_user': gdb.user(),
        'db_port': gdb.port(),
    })
    set_flag('restart-app')
```