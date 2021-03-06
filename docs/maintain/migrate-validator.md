# Migrate your Validator


## Setup:
You need to set up a node as per the tutorial, launch it for few seconds so you create the initial data directory


## Migrate:
In order to get the new node ID being the same as the old one you need to move the folders ``` .local/share/galital/chains/galital/network/ ```  and ``` .local/share/galital/chains/galital/keystore/ ``` if you stored keys inside it. To access the correct path you need to be in your user directory ( access it by typing ```cd``` ).


## The commands:
**This assumes you followed the documentation deployment and you use root user, replace it if the user is different. This will replace all the data on the new server!**

### 1) Stop galital service on the new server after the node has completed the full synchronisation

``` service galital stop ```

### 2) login in the server you want to migrate from ( where the old node is running )

``` service galital stop && cd && scp -rp .local/share/galital/chains/galital/network root@NEW_SERVER_IP:/root/.local/share/galital/chains/pirl/network && scp -rp .local/share/galital/chains/galital/keystore root@NEW_SERVER_IP:/root/.local/share/galital/chains/pirl/keystore ```

### 3) Go back into the new server
Relaunch galital service

``` service galital start ```



Voila, you are all set, just wait for the chain to sync and catch the latest blocks.