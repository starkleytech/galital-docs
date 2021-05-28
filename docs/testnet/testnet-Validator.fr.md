# Créer un Validateur

## Conditions requises

La façon la plus courante pour un débutant de faire fonctionner un validateur est sur serveur cloud linux. Vous pouvez choisir le fournisseur de votre choix.

Le poid des transactions dans le Testnet Galital ont été évalués sur du matériel standard. Il est recommandé que les validateurs utilisent au moins le matériel standard afin de s'assurer qu'ils sont capable de traiter tout les blocs à temps.
Les éléments suivants ne sont pas des exigece minimales, mais si vous décidez de fonctioner avec moins que cela, sachez que vous pourriez avoir des problèmes de performance.

### Matériel minimun :

- 6GB ram, 60 GB disque dur, 2 CPU , <strong>connexion stable du serveur avec IP fixe</strong>

### Matériel idéal :

- 60GB ram, 300 GB disque dur, 6 CPU, <strong>connexion stable du serveur avec IP fixe</strong>

## Ubuntu 20.04 : 

### Mettre à jours votre Ubuntu
```
sudo apt-get update
```

### Validateur

Installer et configurer le client NTP (Network Time Protocol)
NTP est un protocole de réseau conçu pour synchroniser les horloges des ordinateurs sur un réseau. NTP vous permet de synchroniser les horloges de tous les systèmes au sein du réseau. Actuellement, il est nécessaire que les horloges locales des validateurs restent raisonnablement synchronisées. Vous devez donc utiliser NTP ou un service similaire. Vous pouvez vérifier si vous avez le client NTP en exécutant :


Si vous utilisez Ubuntu 18.04 / 19.04 / 20.04, le client NTP devrait être installé par défaut.
```
timedatectl
```

Si NTP est installé et fonctionne, vous devriez voir `System clock synchronized : yes` (ou un message similaire). Si vous ne le voyez pas, vous pouvez l'installer en exécutant :
```
sudo apt-get install ntp
```

ntpd sera lancé automatiquement après l'installation. Vous pouvez interroger ntpd pour obtenir des informations sur son état afin de vérifier que tout fonctionne :
```
sudo ntpq -p
```

>AVERTISSEMENT : Si vous sautez cette étape, le validateur peut manquer des opportunités de création de blocs. Si l'horloge est désynchronisée (même de peu), les blocs produits par le validateur peuvent ne pas être acceptés par le réseau. Il en résultera des ImOnline sur la chaîne, mais aucun bloc alloué sur la chaîne. 
>


## Installer le binaire

<br>

Installer and configurer le temps
```
sudo apt install chrony
```

Activez-le
```
sudo systemctl enable chrony
```

Autoriser le processus dans le pare-feu

```
sudo ufw allow 22
sudo ufw allow 30333
sudo ufw enable
```

Configurer fail2ban
```
sudo apt install -y fail2ban && sudo systemctl enable fail2ban && sudo service fail2ban start
```

Récupérer le paquet Galital depuis github

```
wget https://github.com/starkleytech/galital/releases/download/2.0.1/galital && sudo chmod +x ./galital && sudo mv ./galital /usr/bin/galital
```

## Rendre le service permanent

Créez le fichier systemd dans /etc/systemd/system/tal.service

```
sudo nano /etc/systemd/system/galital.service
```

```
[Unit]
Description=Galital Validator
After=network-online.target

[Service]

ExecStart=/usr/bin/galital --port "30333" --name "A Node Name" --validator --chain galital   
User=root
Restart=always
ExecStartPre=/bin/sleep 5
RestartSec=30s

[Install]
WantedBy=multi-user.target
```

> Si vous avez besoin de changer de port, vous pouvez les configurer avec `--prometheus-port` `--rpc-port` et `--ws-port`

puis démarrez le service 
```
sudo systemctl enable tal && sudo service tal start 
```

Vérifiez si votre validateur apparaît dans la télémétrie : [https://telemetry.polkadot.io/#list/Galital](https://telemetry.polkadot.io/#list/Galital)

> N'oubliez pas de modifier le paramètre de nom (--name "A Node Name")


## Assigner le validateur à votre compte

Vous pouvez obtenir des RTAL (jetons du testnet) depuis le discord de Galital

Vous devez créer un compte de controlle afin de pouvoir effectuer les étapes suivantes

>le compte "stash" vous sert de "cold wallet"
>
>le compte de controlle vous sers a gerer votre "stash"
><br></br><strong>Toujours garder en lieu sûr votre fichier keystore ou votre see de 12/24 mots</strong>

Pour créer un compte de controlle, rajoutez un compte
![Controller](assets/controllerAccount1.png)

Sauvegardez votre seed
![Controller](assets/controllerAccount2.png)
 
puis nommez votre compte et ajoutez un mot de passe
![Controller](assets/controllerAccount3.png) 

Envoyez ensuite quelques $RTAL (de votre compte "stash") pour couvrir les frais de réseau.

Vous pouvez passer aux étapes suivantes

### Créer une clé de session :

Allez dans votre terminal où le validateur est installé et collez la commande suivante, vous obtiendrez la clé de session de votre validateur.

```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```

### Enregistrer votre clé:

Allez sur le [testnet](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fgalital-rpc-testnet.starkleytech.com#/staking/actions) et créez un validateur en utilisant la key obtenu précédement.
![Validator](assets/valitador1.png)

Selectionnez votre compte "stash", votre compte de controlle etc..
![Validator](assets/valitador2.png)

Ajoutez la clé de votre validateur.
![Validator](assets/valitador3.png)

Vous devriez maintenant voir votre validateur dans la rubrique "Waiting"
![Validator](assets/valitador4.png)


Voila, vous avez fini

<br></br>

<p align=right> écrit par Masterdubs & WeHaveCookie </p>
