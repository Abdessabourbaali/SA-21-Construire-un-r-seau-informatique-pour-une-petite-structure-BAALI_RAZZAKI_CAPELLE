# SAE-21, Construire un réseau informatique pour une petite structure

Durant la SAE-21, nous avons été chargés de mettre en place un réseau informatique pour une petite entreprise. 

Une tâche que nous seront certainement amenés à réaliser dans un futur proche.


## Le cahier des charges est le suivant :
---

- Prévoir un plan d'adressage pour toutes les machines de notre réseau

- La structure de l'entreprise comprend un service administratif, un service commercial et un service informatique

- Configurer un serveur web externe (dans une DMZ) contenant une page Web statique, accessible aux personnes, à l'intérieur et à l'extérieur du réseau

- Un serveur DNS (dans une DMZ) qui assure la résolution des noms symbolique

- Un serveur Web interne (Intranet)

- Un serveur DHCP, qui fournira des IP à toutes les machines du réseau

- Mise en place d'un peu de sécurité

- Les différents accès : 

    - les administratifs devront pouvoir accéder à internet et aux serveurs internes et externe

    - Les commerciaux devront pouvoir accéder aux serveurs internes et externes

    - Le SI doit pouvoir avoir accès à toutes les machines en SSH

    - Aucun message provenant de l’internet ne doit pouvoir arriver vers le LAN. Par contre, ils peuvent atteindre le serveur web externe


## Matériel et logiciel choisis : 
---

- Nous avons choisi d'utiliser GNS3, car c'est selon nous le meilleur logiciel libre de simulation de réseau informatique.
- Nous avons choisi d'utiliser Github pour la rendue et l'organisation parce que cela permet la mise en commun et l'organisation de nos travaux.
- Pour ajouter de la sécurité, nous avons choisi les règles ACL, vue précédemment dans la ressource R103

## Mise en place du réseau
---

La mise en place du réseau se découpe en deux grandes parties :

- Configuration de la partie virtuelle
- Configuration de la partie physique

## Travail préliminaire
---

Premièrement, nous avons commencé par mettre en place un environnement de travail professionnelle. Pour cela nous avons décidé d'utiliser Github afin d'organiser et de mettre en communs tous nos fichiers. Vous pouvez retrouver le Github ICI : https://github.com/Abdessabourbaali/SAE21-Construire-un-reseau-informatique-pour-une-petite-structure-BAALI_RAZZAKI_CAPELLE

Par la suite, on a réfléchi et mis en place un plan d'adressage pour notre réseau. Ce plan est disponible dans les fichiers joint sous le nom de "Plan_Addressage.png"

</br>
 
<img src="https://github.com/Abdessabourbaali/SAE21-Construire-un-reseau-informatique-pour-une-petite-structure-BAALI_RAZZAKI_CAPELLE/blob/main/SAE-21/Plan_Addressage.png" style="width: 800px">

</br>

Une fois ces deux choses terminées, nous avons commencé à réaliser la partie virtuelle puis dans les dernières séances, nous nous sommes occupés de la partie physique.

# Mise en place et configuration de la partie virtuelle
---

Afin de créer et mettre en place la partie virtuelle, on a dû configurer les routeurs, mettre en place des Vlans, un serveur DHCP, des règles ACL, des droits sur le service SSH, un serveur Web.

## Configuration du routeur
---

Pour configurer le routeur CISCO sur gns3, il faut commencer par configurer les adresse IP des pattes interne et externe du routeur. Nous avons par la suite ajouté le service DHCP pour une attribution automatiques des IP et pour finir on a mis en place OSPF.

## Configuration des Vlans (Switch)
---

Nous avons configuré le NAT sur chaque Vlan du routeur, pour cela il a fallu configurer les pattes interne et externe. On a dû aussi mettre en place l'ACL contenant une liste des adresses sources internes et pour finir la configuration du pool d'adresse IP globale.


## Configuration des Vlans (Routeur)
---

Pour les Vlan, on a dû configurer 4 Vlans, pour cela on utilise le code suivant :

    conf t
    interface FastEthernet 0/0.1
    encapsulation dot1Q 10
    ip address 192.168.10.1 255.255.255.248
    exit

On utilise ce code 4 fois pour la configuration des 4 Vlans.

## Configuration du pool DHCP pour les Vlans
---

Pour le pool d'adresses DHCP, il a fallu configurer chaque Vlan séparément. Pour cela on a utilisé le code suivant :

    ip dhcp pool VLAN10
    network 192.168.10.0 255.255.255.248
    default-router 192.168.10.1
    exit

On utilise ce code 4 fois pour la configuration des 4 Vlans

## Configuration des ACL
---

Nous avons choisi les ACL pour ajouter de la sécurité vu dans la ressource R103. Les ACl (Access Control List), permet une gestion plus fine des droits d'accès.

Pour la configaration des ACL, on a dû préalablement définir ce quelle devait filtrer. Puis appliquer la règle sur l'interface choisie.

    R1(config)#interface fastEthernet 0/0.1
    R1(config-subif)#ip access-group firsttothird in
    R1(config-subif)#ip access-group firsttothird out

Ce code permet d'appliquer la règle ACL choisie sur une interface choisie.

## Configuration de SSH sur le routeur
Afin de configurer le service SSH sur le routeur, il faut choisir les options qui nous concernent.

Pour commencer on active le service SSH :

    ip ssh version 2

Nous avons donc sélectionné les options suivantes :

    ip ssh logging events
---
    ip ssh logging events
---
    ip ssh time-out 60
---
    ip ssh authentication-retries 3
---
    service password-encryption
---
    username admin password 0  admin
---
    username admin privile 15 pass admin
---
    transport input ssh

## Configuration des restrictions sur SSH
---

Pour configurer les restrictions sur les connexions SSH, il faut modifier le fichier sshd_config et ajouter les lignes suivantes :


    DenyUsers test
    DenyGroups test

    DenyUsers root
    DenyGroups test

    PermitRootLogin no

Cela a pour effet de contrôler qui peut se connecter

## Configuration du serveur web
---

Pour la mise en place du serveur web, nous avons choisi d'utiliser Apache2, une solution que nous avions déjà utilisée lors de TP antérieurs.

Après avoir réalisé une page web assez simple, nous avons configuré le serveur Apache2 pour que notre page index.html soit accessible.

</br>

# Mise en place et configuration de la partie physique

## Problème rencontré
---

Lors de la configuration du routeur pour la partie physique, notre routeur ne répondez plus et ne possédais pas d'adresse IP. Nous avons donc du passer par le logiciel WinBox pour le reconfigurer.

<br/>

## Configuration du firewall
---

On a essayé de configurer les règles du firewall de façon que tout les VLAN aient accès à internet toute en les protègant des attaques qui peuvent venir de l'exterieur, pour ça on a autorisé l'accès juste aux trames TCP provenant du port 80 ou 443 et les paquets ICMP et les paquets DHCP provenant du port 67 et de destination le port 68 , et nous avons laisser tombé tout les autres paquets .

<br/>

## Configuration du serveur DNS
---

Pour configurer notre serveur DNS, nous avons commencé par installer le paquet BIND9 ( sudo apt-get install bind9 ), et le paquet dnsutils ( sudo apt-get install dnsutils ) qui fournit des outils très pratiques pour tester et débugger le service DNS. 

Ensuite pour faire la correspondance entre notre nom de domaine qualifié et l'adresse IP de notre serveur WEB nous avons modifié trois fichiers de configuration qui ce trouvent dans le répertoire /etc/bind :

db.NomdeNotreZone.fr

named.conf.local

named.conf.options

# Composantes essentielles et apprentissage couvert

Enfin nous avons veiller à ce que notre projet respecte bien la  méthodologie d'évaluation :

* Composantes essentielles :

    * Choisir les solutions et technologies réseaux adaptées : Nous avons effectivement respecter cette composantes car nous avons fourni un travail de recherche et nous avons choisi plusieurs outils que nous avonis déjà utilisé par le passé.

    * Respect des principes fondamentaux de la sécurité informatiques : Effectivement en protégeant notre réseau avec un firewall et en mettant en place des règles ACL sur notre routeurs cela rempli bien cette composante.

    * Utiliser une approche rigoureuse pour la résolution des dysfonctionnements : Nous avons résolu nos dysfonctionnements, tout d'abord en faisant des recherches.

    * Respect des règles métiers : Cettes composantes rejoint le respect des principes fondamentaux de la sécurité. En respectant la sécurité du réseaux et le respect du cahier des charges nous respectons les règles métier;

    * Assurer une veille technologique : Effectivement en faisant des recherches lié au projet constamment, nous somme au point sur les dernières technologie réseaux.

* Apprentissages critiques :

    * Comprendre l’architecture des systèmes numériques et les principes du codage de l’information : En effectuant un schéma du réseaux que nous allons mettre en place, en codant les sites que nous avons mit en ligne sur le serveur WEB nous remplissant effectivement cette aprentissage critique.

    * Savoir configurer les fonctions de base du réseau local : Dans ce projet, nous avons fait multiple manipulation de configuration réseaux avec les PC de l'IUT mais aussi avec les routeurs (Cisco/Mikrotik) que se soit sur la partie virtuelle ou physique.

    * Maitrîser les rôles et les principes fondamentaux des systèmes d’exploitation afin d’interagir avec ceux-ci pour la configuration et administration des réseaux et services fournis : Effectivement dans le cadre de ce projet nous avons utiliser 2 systèmes d'exploitation (Windows / Linux). Nous avons utiliser GNS3 sur les différents systèmes.

    * Identifier les dysfonctionnements du réseau local : Dans ce projet nous avons eu divers problèmes que se soit avec le DHCP ou les routeurs Mikrotik mais nous avons su régler les problèmes avec une approche rigoureuse cette apprentissage critique rejoint la composante essentielle **Utiliser une approche rigoureuse pour la résolution des dysfonctionnements**

    * Savoir installer un poste client : Notre réseau ce compose de plusieurs PC dans les différentes partie (Virtuelle / Physique) donc nous avons effectivement configuré un bon nombre de poste ce qui permet de couvrir cette apprentissage critique. 