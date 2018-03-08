# How does Juju work

## Juju concepts

I wont recite the [juju concepts-page](https://jujucharms.com/docs/stable/juju-concepts). But the following table summarizes most concepts and terms.

| **Concept**|                                    **Meaning**                                   |    **Example**   |
|:----------:|:--------------------------------------------------------------------------------:|:----------------:|
|    Cloud   |                          Resource that provides machines                         |  AWS, MAAS, LXD  |
| Controller |         Initial cloud instance that functions as central management node         |         -        |
|    Model   |     A model has 1 controller and is the playfield for deploying applications     |         -        |
|    Charm   | The sum of instructions needed to install and configure applications on machines | MySQL, Wordpress |
|   Bundle   |                              A collection of charms                              |    wiki-simple   |
|   Machine  |                    An instance of the cloud (mostly has an IP)                   |         -        |
|    Unit    |                                 Deployed software                                |      MongoDB     |
|  Relation  |            The concept of connecting multiple units through interfaces           |         -        |
|    Agent   |             Software on a juju machine to keep track of state changes            |         -        |


## Overview

## Reactive framework

