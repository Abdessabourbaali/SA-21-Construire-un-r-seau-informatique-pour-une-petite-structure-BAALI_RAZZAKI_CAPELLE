# SAE-21, Construire un réseau informatique pour une petite structure

Durant la SAE-21, nous avons été chargé de mettre en place un réseau informatique pour une petite entreprise. 

Une tâche que nous seront certainement amené à réaliser dans un futur proche.

## Le cahier des charges est le suivant :

- Prévoire un plan d'addressage pour toute les machines de nôtre réseau

- La structure de l'entreprise comprend un service administratif, un service commercial et un service informatique

- Configurer un serveur web externe ( dans une DMZ ) contenant une page Web static, accessible au personne à l'interieur et à l'extérieur du réseau

- Un serveur DNS ( dans une DMZ ) qui assure la résolution des nom symbolique

- Un serveur Web interne ( Intra-net )

- Un serveur DHCP, qui fournira des IP à toutes les machines du réseau

- Mise en place d'un peu de sécurité

- Les différents accès : 
    - es administratifs devront pouvoir accéder à internet et aux serveurs internes et externe

    - Les commerciaux devront pouvoir accéder aux serveurs interne et externes

    - Le SI doit pouvoir avoir accès à toutes les machines en SSH

    - Aucun message provenant de l’internet ne doit pouvoir arriver vers le LAN. Par contre il peuvent atteindre le serveur web externe












- Règles ACL d'apres R103, manière d'isoler les Vlan
