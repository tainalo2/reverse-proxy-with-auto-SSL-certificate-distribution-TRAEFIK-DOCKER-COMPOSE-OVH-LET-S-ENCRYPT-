# Reverse proxy with auto SSL certificate distribution (TRAEFIK, DOCKER COMPOSE, OVH, LET'S ENCRYPT)
A simple way to setup an automated reverse proxy with certificate ditribution.

[FR]
# Summary
  - Problématique
  - Prérequis
  - Explication technique
  - Installation
    - Installer docker
    - Installer docker-compose
    - créer un domaine pour le serveur
    - créer un accès à l'API OVH
    - Rédiger le YML de docker-compose
    - Tester
  - Credit

# Problématique
L'objectif est le suivant :
  - avoir un VPS permettant d'accéder à plusieurs docker/instances node-red en fonction du propriétaire/outil
  - l'accés aux instances doit être chiffré (certificat SSL signé par une autorité)
  - il faut un URL unique à chaque instance

# Prérequis
  - Un serveur linux (ici debian 10)
  - un nom de domaine administré chez OVH

# Explication technique
  - Traefik est un reverse proxy qui peut fonctionner en "auto-découverte" via le sock de docker
  - On déploie traefik
  - On indique aux dockers visés de créer une route avec traefik (permet d'éliminer le port-forwarding)
  - Traefik découvre et gère toutes les routes en autonomie ET créé les certificats SSL pour chaque route !

# Installation
  - Installer docker
    - entrer les commandes suivante :
     - sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common
     - curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
     - sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
     - sudo apt update
     - sudo apt install docker-ce
  - Installer docker-compose
    - entrer les commandes suivante :
     - sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
     - sudo chmod +x /usr/local/bin/docker-compose
  
  - créer un domaine pour le serveur
    - Rendez vous sur votre dashboard OVH : https://www.ovh.com/manager/web/#/domain/
    - Sélectionnez votre nom de domaine > aller dans "Zone DNS" > "Ajouter une entrée" > "A" > saisir le sous domaine "\*" > cible = IP de votre serveur > "Suivant"
  
  - créer un accès à l'API OVH
    - Aller sur le site : https://eu.api.ovh.com/createApp/ (if canada go on : https://ca.api.ovh.com/createApp/)
    - Renseigner les divers champs > Enregistrer précieusement l'APP KEY et la APP SECRET
    - récupérer le fichier "curl-api-OVH.text" > rempacer le "YOUR_APP_KEY" par l'app key que vous venez d'obtenir
    - faire la requête CURL en cli > Se rendre sur le lien de validation et se connecter > sauvegarder la CONSUMER KEY en réponse de la requête !
  
  - Rédiger le YML de docker-compose
    - Télécharger sur votre serveur le fichier "docker-compose.yml" hébergé dans ce GIT
    - Editer le fichier en modifiant :
    - "your.mail@domain.com" par votre mail à vous
    - YOUR_APP_KEY par votre app key OVH
    - YOUR_APP_SECRET par votre app secret OVH
    - YOUR_CONSUMER_KEY par votre consumer key OVH
    - "sous-domaine1.domain.com" et "sous-domaine2.domain.com" par votre domaine OVH et le sous-domaine que vous souhaitez, pas besoin de le déclarer chez OVH

    - en CLI placez vous dans le dossier du fichier "docker-compose.yml" et exécutez la commande : "docker-compose up -d"
 
  - Tester
    - Rendez vous sur la page web "sous-domaine1.domain.com" elle est normalement chiffrée et signée par Let's Encrypt

# Credit
Create by tainalo2 : french streamer on twitch every week day from 07H to 09H (Paris hours)
All my links here : https://linktr.ee/tainalo2
