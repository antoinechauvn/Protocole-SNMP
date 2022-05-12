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

## Principe de fonctionnement
Manager SNMP :
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

**Voici un exemple de structure de table MIB :**

![image](https://user-images.githubusercontent.com/83721477/168039803-5fde58e6-63c9-433d-855f-b63f9a1b391c.png)

On aura par exemple sur un commutateur Nortel Passport l’OID `1.3.6.1.4.1.2272.1.1.20` désignant le taux de charge du CPU.

### Les différents types de messages SNMP
Il existe différents types de messages SNMP qui permettent de mettre en place un mécanisme de supervision réseau via SNMP :

* `GetRequest`<br>C’est le message SNMP qu’un manager SNMP est le plus souvent amené à utiliser pour demander des données.<br>L’appareil ciblé renvoie la valeur demandée sous la forme d’un message de type `Response`.

* `GetNextRequest`<br>Le manager SNMP peut envoyer un message de ce type pour déterminer les informations que l’appareil peut envoyer.<br>En commençant par l’OID 0, le manager peut continuer à requérir les données immédiatement consécutives, ce jusqu’à ce qu’il n’y ait plus des données « suivantes ».<br>Les utilisateurs peuvent ainsi accéder à toutes les informations disponibles concernant un périphérique donné, même s’ils n’avaient pas nécessairement connaissance de l’existence du système ou de l’appareil qui répond.

* `GetBulkRequest`<br>Apparue avec l’introduction de la v2 de SNMP, cette requête est une version nouvelle et optimisée de GetNextRequest.<br>La réponse sollicitée contient autant de données que la requête l’autorise.<br>Il s’agit avant tout d’un moyen de regrouper plusieurs requêtes GetNextRequest, ce qui permet aux utilisateurs de créer une liste recensant l’intégralité des données et des paramètres disponibles.

* `SetRequest`<br>Il s’agit d’une commande initiée par le manager afin de définir ou modifier via SNMP la valeur d’un paramètre sur l’appareil ou système qui sert d’agent.<br>Ce type de message sert également à gérer ou à modifier des paramètres de configuration ou d’autres réglages.<br>Attention à ne pas commettre d’erreur en la matière, une requête SetRequest peut, si elle est mal formulée, provoquer de sérieux dégâts sur la configuration des systèmes et du réseau.

* `Response`<br>La response est le message qu’un appareil agent envoie après avoir reçu une Requête de la part du manager.<br>Quand il fait suite à une requête de type GetRequest type, le paquet contient les données ou valeurs sollicitées.<br>Dans le cas d’une requête SetRequest, le paquet répond en transmettant la nouvelle valeur et confirme ainsi que la requête SetRequest a bel et bien abouti.

* `Trap(v2)`<br>Un trap est envoyé (ou « poussé ») par l’agent SNMP sans avoir été demandé par le manager.<br>Au lieu de cela, des traps sont envoyés dans des situations précises, comme en cas d’erreur, ou de franchissement d’un seuil de tolérance prédéfini.<br>Si les utilisateurs veulent tirer parti des traps à des fins de supervision, ce qui une excellente idée pour qui veut être proactif sur ce plan, il leur faudra probablement configurer préalablement ces traps par le biais du manager SNMP.

* `InformRequest`<br>Ce type de message a été introduit sur SNMP v2 pour donner au manager la possibilité d’accuser réception du message trap émanant d’un agent.<br>Certains agents sont configurés pour continuer d‘envoyer un trap jusqu’à réception d’un message de type Inform.

* `Report`<br>Les messages de type sont uniquement disponibles sur SNMP v3.<br>Ils permettent à un manager SNMP de déterminer la nature du problème qui a été détecté par l’agent SNMP distant.<br>En fonction du type d’erreur mis au jour, le moteur SNMP peut essayer d’envoyer une version corrigée du message SNMP.<br>Si cela s’avère impossible, il peut signaler l’erreur à l’application au nom de laquelle la requête SNMP infructueuse avait été envoyée. [RFC3412]
