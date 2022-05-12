# Protocole-SNMP
Approfondissement du protocole SNMP

### Qu'est-ce que le protocole SNMP?
```
Le protocole SNMP (Simple Network Management Protocol) a été élaboré par le groupe de travail IETF
(Internet Engineering Task Force) pour la surveillance et la gestion des systèmes et des unités dans un réseau.
L’utilité de SNMP dans le domaine de l’administration réseau tient à ce qu’il permet de collecter des
informations relatives aux appareils connectés à un réseau de façon standardisée et sur un large éventail
d’appareils et de types de logiciels.
```

### Manager SNMP :
Un gestionnaire ou système de gestion est une entité distincte chargée de communiquer avec les périphériques réseau mis en œuvre par l'agent SNMP. Il s'agit généralement d'un ordinateur utilisé pour exécuter un ou plusieurs systèmes de gestion de réseau.

### Agent SNMP :
L'agent est un programme logiciel intégré à l'élément de réseau. L'activation de l'agent lui permet de collecter la base de données d'informations de gestion à partir du périphérique localement et la met à la disposition du gestionnaire SNMP, lorsqu'il est interrogé. Ces agents peuvent être standard (par exemple Net-SNMP) ou spécifiques à un fournisseur (par exemple, l'agent HP Insight)
* Collecte des informations de gestion sur son environnement local
* Stocke et récupère les informations de gestion telles que définies dans la MIB.
* Signale un événement au gestionnaire.
* Agit en tant que proxy pour certains nœuds de réseau non SNMP gérables.

### Base de données d'informations de gestion ou Management Information Base (MIB):
La MIB (Management Information base) est la base de données des informations de gestion maintenue par l’agent, auprès de laquelle le manager va venir pour s’informer.<br>
La MIB est une structure arborescente dont chaque noeud est défini par un nombre ou OID (Object Identifier).<br>
Deux MIB publics ont été normalisées: MIB I et MIB II (dite 1 et 2).<br>
Un fichier MIB est un document texte écrit en langage ASN.1 (Abstract Syntax Notation 1) qui décrit les variables, les tables et les alarmes gérées au sein d’une MIB.

### OID
OID est un acronyme correspondant à Object Identifier, c’est-à-dire identifiant d’objet.Les OID permettent d’identifier de manière unique chacun des objets gérés qui sont définis dans des fichiers MIB.

## Principe de fonctionnement

Le protocole SNMP a deux façons de travailler:
* Le polling
* Les traps

Le `polling` consiste à envoyer des requêtes distantes de manière automatique ou à la demande, en effectuant une opération de requête synchronisée.
<br>Les `traps` sont des messages envoyés par des appareils SNMP à une adresse configurée en fonction de modifications ou d’événements, de manière désynchronisée.

**Voici un exemple de structure de table MIB :**

![image](https://user-images.githubusercontent.com/83721477/168039803-5fde58e6-63c9-433d-855f-b63f9a1b391c.png)

On aura par exemple sur un commutateur Nortel Passport l’OID `1.3.6.1.4.1.2272.1.1.20` désignant le taux de charge du CPU.

### Les différents types de messages SNMP
Il existe différents types de messages SNMP qui permettent de mettre en place un mécanisme de supervision réseau via SNMP :

* `GetRequest`<br>C’est le message SNMP qu’un manager SNMP est le plus souvent amené à utiliser pour demander des données.<br>L’appareil ciblé renvoie la valeur demandée sous la forme d’un message de type `Response`.

* `GetNextRequest`<br>Le manager SNMP peut envoyer un message de ce type pour déterminer les informations que l’appareil peut envoyer.<br>En commençant par l’OID 0, le manager peut continuer à requérir les données immédiatement consécutives, ce jusqu’à ce qu’il n’y ait plus des données « suivantes ».<br>Les utilisateurs peuvent ainsi accéder à toutes les informations disponibles concernant un périphérique donné, même s’ils n’avaient pas nécessairement connaissance de l’existence du système ou de l’appareil qui répond.

* `GetBulkRequest`<br>Apparue avec l’introduction de la v2 de SNMP, cette requête est une version nouvelle et optimisée de `GetNextRequest`.<br>La réponse sollicitée contient autant de données que la requête l’autorise.<br>Il s’agit avant tout d’un moyen de regrouper plusieurs requêtes GetNextRequest, ce qui permet aux utilisateurs de créer une liste recensant l’intégralité des données et des paramètres disponibles.

* `SetRequest`<br>Il s’agit d’une commande initiée par le manager afin de définir ou modifier via SNMP la valeur d’un paramètre sur l’appareil ou système qui sert d’agent.<br>Ce type de message sert également à gérer ou à modifier des paramètres de configuration ou d’autres réglages.<br>Attention à ne pas commettre d’erreur en la matière, une requête SetRequest peut, si elle est mal formulée, provoquer de sérieux dégâts sur la configuration des systèmes et du réseau.

* `Response`<br>La response est le message qu’un appareil agent envoie après avoir reçu une Requête de la part du manager.<br>Quand il fait suite à une requête de type `GetRequest` type, le paquet contient les données ou valeurs sollicitées.<br>Dans le cas d’une requête `SetRequest`, le paquet répond en transmettant la nouvelle valeur et confirme ainsi que la requête `SetRequest` a bel et bien abouti.

* `Trap(v2)`<br>Un trap est envoyé (ou « poussé ») par l’agent SNMP sans avoir été demandé par le manager.<br>Au lieu de cela, des traps sont envoyés dans des situations précises, comme en cas d’erreur, ou de franchissement d’un seuil de tolérance prédéfini.<br>Si les utilisateurs veulent tirer parti des traps à des fins de supervision, ce qui une excellente idée pour qui veut être proactif sur ce plan, il leur faudra probablement configurer préalablement ces traps par le biais du manager SNMP.

* `InformRequest`<br>Ce type de message a été introduit sur SNMP v2 pour donner au manager la possibilité d’accuser réception du message trap émanant d’un agent.<br>Certains agents sont configurés pour continuer d‘envoyer un trap jusqu’à réception d’un message de type Inform.

* `Report`<br>Les messages de type sont uniquement disponibles sur SNMP v3.<br>Ils permettent à un manager SNMP de déterminer la nature du problème qui a été détecté par l’agent SNMP distant.<br>En fonction du type d’erreur mis au jour, le moteur SNMP peut essayer d’envoyer une version corrigée du message SNMP.<br>Si cela s’avère impossible, il peut signaler l’erreur à l’application au nom de laquelle la requête SNMP infructueuse avait été envoyée. [RFC3412]

## Versions SNMP
* `SNMPv1` (ancien standard): Ceci est la première version du protocole, tel que définie dans le RFC 1157.. On dit que la sécurité de cette version est triviale, car la seule vérification qui est faite est basée sur la chaîne de caractères  » community « . SNMPsec (historique): Cette version ajoute de la sécurité au protocole SNMPv1. La sécurité est basée sur des groupes. Très peu ou aucun manufacturiers n’a utilisé cette version qui est maintenant largement oubliée.
RFC 1155

* `SNMPv2p` (historique): Beaucoup de travaux on été exécutés pour faire une mise à jour de SNMPv1. Ces travaux ne portaient pas seulement sur la sécurité. Le résultat est une mise à jour des opérations du protocole, des nouvelles opérations, des nouveaux types de données. La sécurité est basée sur les groupes de SNMPsec.

* `SNMPv2c` (expérimental): Cette version du protocole est appelé  » community stringbased SNMPv2 « . Ceci est une amélioration des opérations de protocole et des  types d’opérations de SNMPv2p et utilise la sécurité par chaîne de caractères « community  » de SNMPv1.
RFC – 1441

* `SNMPv2u` (expérimental): Cette version du protocole utilise les opérations, les types de données de SNMPv2c et la sécurité basée sur les usagers.

* `SNMPv2*` (expérimental): Cette version combine les meilleures parties de SNMPv2p et SNMPv2u. Les documents qui décrivent cette version n’ont jamais été publiés dans 12 les RFC. Des copies de ces documents peuvent être trouvées sur le site web et SNMP Research (un des premiers à défendre de cette version).
RFC – 1901

* `SNMPv3` (standard actuel): Cette version comprend une combinaison de la sécurité basée sur les usagers et les types et les opérations de SNMPv2p, avec en plus la capacité pour les  » proxies « . La sécurité est basée sur ce qui se trouve dans SNMPv2u et SNMPv2*.
RFC – 3411
