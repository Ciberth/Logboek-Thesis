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