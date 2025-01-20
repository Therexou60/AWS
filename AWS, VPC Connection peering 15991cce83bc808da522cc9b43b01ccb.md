# AWS, VPC Connection peering

[https://www.notion.so](https://www.notion.so)

AWS VPC Connecting VPCs by peering

Première étape prendre les ip des instances que l’on veut connecter.

Créer une peering connection, aller dans VPC et à gauche, cliquez sur « Peering Connections » ensuite «  Create peering connection »

Donner un nom, choisir pour le premier VPC ID, l’instance qui veut communiquer avec l’autre

Ensuiite, choisir l’instance qu’on veut communiquer,

Une fois le peering créer, il faut accepeter la demande de peering, dans action et ensuite, accepeter la demande.

Aller dans le subnet ID ety cliquez depuis l’intances VPC, pour l’instance qui veut communiquer.

Pareil mais pour la table des routes

Ensuite dans routes et editer les routes

Bien mettre le réseau de l’instance qu’on veut aller

Faire pareil mais pour celui qui va communiquer.

Et bien mettre egalement le réseau de l’autre

Dans le EC2, on va aller dans les groupes de sécurité mais seulement pour l’instance cible.

Editer les règles piour autoriser le ping, dans mon cas j’ai deux instancezsd qui veulent joindre la cible