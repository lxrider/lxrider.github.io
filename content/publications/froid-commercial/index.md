+++
title = "Cybersécurité du froid commercial : qui peut agir sur vos installations, et jusqu'où ?"
description = "Connaître les équipements, maîtriser les accès, limiter les impacts et préserver la continuité du froid"
date = 2026-10-22
draft = false
template = "doc-page.html"
[extra]
article_version = "1.0 — publication initiale"
author = "lxrider"
rights = "© 2026 lxrider — Tous droits réservés pour le texte original"
+++

Dans une installation de froid commercial, une connexion à distance peut donner accès à une supervision, à des fonctions de commande et parfois à plusieurs équipements ou établissements. **La question déterminante est l'étendue des capacités d'action associées à cet accès** : qui peut modifier un paramètre, à travers quelles interfaces, avec quels droits et quelles conséquences possibles sur le procédé ?

Les incidents d'octobre 2026 concernant des équipements Danfoss et Copeland/Dixell ont rendu ces questions particulièrement concrètes. Le CERT-FR a notamment recommandé de supprimer l'exposition directe des interfaces de gestion sur Internet. Le vecteur initial des compromissions et l'étendue exacte des droits exploités ne sont toutefois pas établis publiquement.

Cet article propose une **méthode d'analyse** : reconnaître les équipements, reconstituer les chemins d'accès, qualifier les droits réels et évaluer les conséquences et les protections indépendantes. Il s'adresse aux exploitants, frigoristes, intégrateurs et professionnels de la cybersécurité industrielle. Il complète le [guide pratique de sécurisation des supervisions frigorifiques](https://lxrider.github.io/publications/securiser-supervision-frigorifique/), consacré aux mesures de protection et à la continuité des interventions.

Les guides de l'ANSSI fournissent un socle d'hygiène informatique et des recommandations adaptées aux systèmes industriels et à la GTB/GTC. Ils éclairent les contrôles proposés ici, sans remplacer l'examen de l'architecture et des droits propres à chaque installation frigorifique.

Le fil conducteur est une question opérationnelle : **« Qui peut agir sur vos installations, et jusqu'où ? »**

<figure class="research-figure"><a class="figure-open" href="01-installation-frigorifique-lxrider.webp" target="_blank" rel="noopener" aria-label="Ouvrir l’illustration d’ouverture en haute résolution"><img src="01-installation-frigorifique-lxrider.webp" alt="Salle technique frigorifique avec compresseurs, tuyauteries et armoires de supervision ; illustration d’ouverture, non documentaire." loading="eager" decoding="async" fetchpriority="high"></a><figcaption>Illustration d’ouverture. Salle technique frigorifique et systèmes de supervision ; représentation générée, non documentaire.</figcaption></figure>

## 1. Connaître l'installation

Dans les grandes et moyennes surfaces (GMS), la **gestion technique centralisée (GTC)** peut regrouper la surveillance des températures, des alarmes et des historiques, ainsi que, selon les solutions et les droits configurés, certaines fonctions de pilotage. Elle peut être dédiée au froid ou interconnectée avec une **gestion technique du bâtiment (GTB)** plus large, couvrant par exemple la ventilation, l’éclairage ou l’énergie. *GTC* désigne ici une fonction de gestion et de supervision : ce n’est pas nécessairement un appareil ou une couche réseau distincte du superviseur frigorifique.

Un parc peut mêler plusieurs générations de superviseurs Danfoss AK-SM/AK-SC, des régulateurs AK-PC, des interfaces AK-PI, des systèmes Copeland/Dixell XWEB, des bus de terrain, un PC froid, un routeur 4G, des accès VPN et une plateforme multisite.

Ces composants n'ont ni les mêmes fonctions, ni les mêmes possibilités de mise à jour ou d'administration. **Un AK-SM 850 historique n'est pas un AK-SM 800A récent ; XWEB historique, EVO et PRO ne sont pas interchangeables.** Une recommandation applicable à une génération ne se transpose pas automatiquement à l'autre. **L’AK-SM assure des fonctions de supervision et de gestion ; l’AK-PC est un régulateur de centrale frigorifique.** Ces rôles doivent rester distincts, y compris dans la documentation des installations.

L'inventaire opérationnel devrait identifier, pour chaque installation :

- les références exactes, firmwares, versions des bibliothèques ou logiciels utiles, et l'état de support ;
- le superviseur, les régulateurs, les bus de communication (par exemple RS485/Modbus lorsqu'ils existent), les passerelles et les postes intermédiaires ;
- la GTC du froid, ses éventuelles interfaces avec une GTB ou une supervision multisite, le sens des échanges et les possibilités de lecture, d’écriture ou de commande ;
- les accès distants : publication Web, NAT, VPN, réseau cellulaire, téléassistance, plateforme constructeur, connexion de secours ;
- les fonctions qui dépendent d'un équipement ou d'une liaison : régulation locale, programmation, alarmes, historisation, configuration, télésurveillance ;
- le responsable technique et le propriétaire de chaque accès, y compris lorsque le routeur ou la SIM ont été installés hors du réseau informatique du magasin.

Une fiche d'installation protégée, datée et tenue à jour vaut mieux qu'un inventaire théorique. Elle doit aussi indiquer où trouver les schémas, les sauvegardes de référence et les coordonnées d’astreinte, y compris si les outils habituels sont indisponibles. Elle doit permettre de répondre rapidement : *si cette liaison ou ce superviseur tombe, qu'est-ce qui continue de fonctionner et qu'est-ce que nous perdons ?*

## 2. Frontières de confiance

Dans une installation, plusieurs parcours peuvent mener à la supervision ou aux régulateurs :

**Intervenant distant → VPN ou accès cellulaire protégé → PC de maintenance ou système de supervision/GTC du froid → régulateur → procédé frigorifique.**

Ce parcours est illustratif. La supervision peut être assurée par différents constructeurs ou intégrateurs ; AK-SM et XWEB sont deux exemples documentés dans les références citées. Selon les sites, une GTB reçoit des informations par une passerelle, une plateforme multisite regroupe certaines fonctions et un poste froid permet la téléassistance. **Le sens des flux, les comptes et les droits effectifs déterminent si ces liaisons permettent la consultation, la commande ou l'administration.** Une interconnexion de données peut rester strictement consultative.

Chaque transition soulève une question de confiance : **qui franchit quelle frontière, avec quelle identité et quelle autorisation ?** L'accès au VPN, l'ouverture d'une session, la permission d'écriture, l'acceptation d'un ordre par le régulateur et l'effet sur le procédé sont des étapes distinctes. Le PC froid ou l'outil de téléassistance ajoute ses propres comptes, permissions et traces à cette chaîne.

En pratique, il faut distinguer quatre points :

1. **Accessibilité** : d'où l'équipement est-il joignable ?
2. **Authentification** : quel compte, quelle session ou quel dispositif identifie l'intervenant ?
3. **Autorisation** : ce principal peut-il consulter, acquitter, commander, modifier des paramètres ou administrer ?
4. **Application** : l'ordre est-il réellement reçu, accepté et traduit en comportement physique ?

<figure class="research-figure"><a class="figure-open" href="02-acces-supervision-lxrider.svg" target="_blank" rel="noopener" aria-label="Ouvrir la figure 1 en haute résolution"><img src="02-acces-supervision-lxrider.svg" alt="Quatre conditions distinctes : accessibilité réseau, authentification, autorisation de modifier et application de la commande. Franchir une condition ne prouve pas la suivante." loading="lazy" decoding="async"></a><figcaption>Figure 1. Accessibilité, authentification, autorisation et application sont des conditions distinctes. Schéma conceptuel, sans représentation d’une installation particulière.</figcaption></figure>

La protection doit couvrir l'ensemble du parcours : un VPN sécurise l'entrée distante ; les droits applicatifs, les comptes partagés et les accès alternatifs demandent des contrôles complémentaires.

## 3. Autorités et périmètres d'action

La gestion des identités et des accès (*Identity and Access Management*, IAM) permet de connaître **qui accède à quoi**. L'analyse des capacités d'action va plus loin : **quelles opérations l'identité peut-elle réellement déclencher, sur quels régulateurs et dans quelles conditions ?**

Concrètement, afficher une température, acquitter une alarme, modifier une consigne, changer une limite de réglage, programmer un dégivrage ou restaurer une configuration n'engagent pas le même niveau d'autorité. Il faut vérifier, pour chaque rôle et équipement, quelles opérations sont accessibles et quels effets elles peuvent produire.

Les documentations constructeurs illustrent cette granularité. XWEB PRO distingue notamment `Read Parameters`, `Edit Parameters Value` et `Send Commands`. Chez Danfoss, les permissions dépendent de la génération et certaines opérations avancées requièrent un niveau `Supervisor`. **La documentation atteste des fonctions possibles ; la configuration d'un site détermine les pouvoirs réellement attribués.**

À cette autorité technique s'ajoute **l'autorisation de l'intervention** : détenir un compte capable de modifier une consigne ne constitue pas, en soi, un mandat de modification pour une opération de diagnostic. Pour recouper les faits, les traces de connexion doivent être rapprochées des commandes, acquittements éventuels et valeurs effectivement relues sur les régulateurs.

### Concentrations d'autorité et accès multisites

Une supervision ou une plateforme peut regrouper plusieurs régulateurs, magasins ou clients. Trois questions doivent être vérifiées séparément : **combien de sites sont joignables, sur lesquels l'identité possède-t-elle des droits d'écriture et existe-t-il une fonction d'action commune ?**

Une plateforme centralisée ne prouve pas que toutes les installations partagent les mêmes privilèges. Inversement, lorsqu'une identité autorise effectivement des modifications sur plusieurs sites, sa compromission peut élargir le périmètre d'impact. Les permissions locales, les relais et les éventuelles restrictions de commande doivent être examinés jusqu'au dernier équipement concerné.

## 4. Limiter le blast radius

Le *blast radius* désigne ici le périmètre **potentiellement affectable** depuis un accès ou une identité compromis, selon les droits et les dépendances techniques. L'étendue des effets constatés pendant un incident nécessite une vérification distincte. Pour une installation frigorifique, il doit être évalué à plusieurs niveaux :

- **Fonctionnel** : une consigne seulement, ou également les bornes autorisées, les dégivrages, les alarmes, les programmations et les configurations stockées ?
- **Local** : un régulateur, plusieurs meubles, une centrale, ou des sections reliées par des fonctions de synchronisation ?
- **Organisationnel** : un établissement, plusieurs magasins d'une enseigne ou plusieurs clients d'un prestataire ?

Si la supervision du froid est reliée à une GTB ou à d’autres systèmes techniques, qualifier les éventuelles commandes transversales **à partir des configurations et droits effectifs**. Une simple remontée d'informations ne présume aucun pouvoir d'administration sur le système destinataire.

Un exemple documenté : sur le régulateur Dixell XR60CX, la borne supérieure d’une consigne (`US`) est elle-même paramétrable. Si une même autorité peut modifier cette borne et la consigne (`SEt`), **la limite reste sous le contrôle de cette autorité**. L'indépendance des sécurités mécaniques, électriques et des alarmes externes exige une vérification spécifique, en fonction de l'objectif protégé.

Pour limiter les conséquences, il faut **réduire les permissions, cloisonner les communications, limiter les droits multisites et identifier les protections réellement indépendantes**. Une sécurité haute pression peut protéger un équipement sans garantir, à elle seule, la conservation des denrées : chaque garde-fou doit être évalué selon l'objectif qu'il protège.

## 5. Vérifier les capacités et les limites des interfaces

Un accès privé à la supervision constitue un premier contrôle, mais le risque résiduel dépend également des **services disponibles, des droits applicatifs et des interfaces alternatives**.

Pour une évaluation des capacités d'action, deux exemples constructeur méritent d'être examinés selon la version réellement installée :

- **Danfoss AK-SM 800A :** les modes documentés `Backward Compatible`, `Permissive` et `Strict` ne produisent pas les mêmes contrôles d'accès. Le service de maintenance ST-500, désactivé par défaut dans la version 4.3.1 documentée, peut être réactivé temporairement. La version affichée ne suffit donc pas à décrire tous les services actifs et droits disponibles.
- **Copeland/Dixell XWEB :** XWEB PRO distingue des permissions applicatives et des authentifications d'API. Les évolutions des versions 1.12 et 1.13 concernent notamment les comptes et la sécurité des interfaces ; leur effet doit être contrôlé sur l'installation concernée. La famille XWEB EVO est arrivée en fin de support logiciel le 16 mars 2026, ce qui pose une question de mesures compensatoires et de migration, sans établir une exploitation de vulnérabilité pendant les incidents.

Le contrôle doit inclure les éventuels chemins supplémentaires : routeur cellulaire, poste froid, téléassistance, compte prestataire, API ou plateforme multisite. **Une modification de protection réseau doit être suivie d'une vérification des droits et de la réception effective des alarmes.** Les choix d'architecture et de mise à jour restent dépendants des matériels, versions, contrats et contraintes de continuité.

## 6. Protéger les données utiles à la maintenance et à la reprise

Plans réseau, configurations, sauvegardes, identifiants de télémaintenance et listes d'astreinte renseignent sur **les chemins d'accès et les autorités techniques**. Ces documents doivent être accessibles aux personnes habilitées, protégés contre la divulgation et disponibles en cas de défaillance du réseau ou de la plateforme documentaire.

Un coffre de secrets adapté aux comptes techniques et aux clients, associé à un espace documentaire contrôlé, permet de séparer identifiants, schémas et configurations. Il faut notamment prévoir la révocation des accès, la restitution des informations lors d'un changement de mainteneur et une méthode de récupération utilisable en situation dégradée.

Une sauvegarde récente peut déjà contenir des paramètres anormaux. Restaurer un état maîtrisé suppose donc de connaître **la référence validée**, l'état réel des régulateurs et le sens des échanges entre supervision et terrain.

Le complément publié par Dixell France en octobre 2026 précise, pour **XWEB PRO et EVO**, qu'une sauvegarde générale de configuration du superviseur **n'archive ni les historiques de fonctionnement ni les paramètres des régulateurs**. Ceux-ci exigent une sauvegarde et une vérification distinctes. Sur XWEB EVO, *importer* un fichier de paramètres et *écrire* ces paramètres vers les régulateurs sont également deux opérations différentes.

## 7. Préparer les modes dégradés

L'analyse doit séparer **la régulation locale**, **la supervision**, **la transmission des alarmes** et **la surveillance des températures de conservation**. Une coupure Internet, l'arrêt d'un superviseur et une rupture de communication avec les régulateurs sont des événements distincts dont les conséquences dépendent du matériel et de ses modes autonomes.

**Une régulation locale peut continuer sans que la télésurveillance soit assurée.** Inversement, une perte du bus de communication ne signifie pas automatiquement l'arrêt de la régulation locale : il faut vérifier ce que chaque régulateur conserve comme fonctions, consignes et sécurités. La figure 2 illustre ces dépendances ; les états indiqués pour chaque scénario restent à qualifier sur le site réel.

Pour préparer les modes dégradés, les questions prioritaires sont :

| Événement | Point à vérifier avant incident |
|---|---|
| Accès distant ou WAN indisponible | Quel fonctionnement local subsiste et comment l'astreinte reçoit-elle les alarmes ? |
| Superviseur isolé ou arrêté | Quels régulateurs, programmes et historiques restent opérationnels ? |
| Bus de terrain interrompu | Quels modes autonomes ou de repli sont réellement configurés ? |
| Surveillance des températures ou alarmes défaillante | Quels capteurs, seuils, communications et destinataires indépendants restent disponibles ? |
| Paramètres suspects | Quelle référence permet de restaurer et comment contrôler l'état physique réel ? |

<figure class="research-figure"><a class="figure-open" href="03-regulation-alarmes-lxrider.svg" target="_blank" rel="noopener" aria-label="Ouvrir la figure 2 en haute résolution"><img src="03-regulation-alarmes-lxrider.svg" alt="Schéma des fonctions distinctes de régulation locale, supervision et alertes, avec une éventuelle surveillance de conservation indépendante par sonde et transmission dédiées." loading="lazy" decoding="async"></a><figcaption>Figure 2. Régulation locale, supervision et alertes : des fonctions reliées mais distinctes. Une surveillance de conservation séparée reste à vérifier de bout en bout (schéma conceptuel).</figcaption></figure>

### Surveiller les températures de conservation indépendamment de la régulation

La **surveillance des températures de conservation des denrées** doit aussi être examinée indépendamment de la régulation frigorifique. Lorsque les seuils, les temporisations ou l'acheminement des alarmes dépendent des mêmes équipements et droits de modification que le pilotage, une modification de configuration peut **retarder, inhiber ou rendre inefficace une alerte**.

Lorsque le risque le justifie, une chaîne de mesure et d'alerte distincte, avec ses propres capteurs, des seuils validés, des accès protégés et une astreinte opérationnelle, permet de limiter cette dépendance. Son efficacité doit être vérifiée **de bout en bout** : emplacement et fiabilité des mesures, déclenchement, transmission, réception et traitement des alarmes, y compris pendant les dégivrages et en mode dégradé. L'indépendance suppose aussi d'identifier les points de défaillance communs : alimentation, réseau, comptes d'administration ou plateforme d'alerte.

Les exigences de surveillance et d'enregistrement dépendent des denrées et de l'usage des équipements. Pour les produits surgelés, le règlement (CE) n° 37/2005 prévoit des modalités de contrôle et des dérogations pour certains meubles de vente au détail. La norme NF EN 12830:2018 décrit les exigences techniques des enregistreurs de température ; l'indépendance de la chaîne d'alerte demande une évaluation complémentaire.

### En cas de suspicion de compromission

L'alerte du CERT-FR distingue les équipements **non compromis** des équipements **compromis**. Les opérations de contrôle, de réinitialisation et de reconfiguration varient en fonction du produit et de la situation. Le retour à une interface accessible ne démontre pas à lui seul que les paramètres, les régulateurs et les alarmes sont revenus à l'état attendu.

La séquence à préparer avec l'exploitant et le frigoriste reste : **qualifier les effets physiques, contenir le chemin suspect, assainir la configuration puis valider la régulation et les alertes jusqu'à l'astreinte**. Les coupures et essais susceptibles d'affecter le froid demandent un cadre autorisé et une préparation opérationnelle. La conception complète d'un PCA/PRA relève d'un travail distinct.

## 8. Relier les autorités techniques aux décisions métier

Les chemins d'accès traversent souvent plusieurs organisations : exploitant, frigoriste, prestataire réseau, intégrateur et fournisseur. L'analyse doit relier **chaque capacité d'action technique** à une personne ou un service habilité, à son périmètre contractuel et à une décision opérationnelle identifiable.

Une question mérite d'être résolue avant un incident : *qui peut suspendre un accès à 2 heures du matin, qui est prévenu et comment la surveillance des températures est-elle maintenue ?* La réponse doit intégrer les capacités techniques des intervenants et les effets possibles de l'isolement sur les alarmes ou l'astreinte.

La répartition des fonctions **décider, administrer, intervenir, vérifier et révoquer** doit être discutée entre les entreprises concernées. Pour l'analyse de cybersécurité, elle permet de **valider les frontières de confiance et les capacités d'action réellement maîtrisées**, sans présumer une responsabilité juridique à partir d'un simple accès informatique.

## 9. Quand approfondir l'analyse de sécurité ?

Le frigoriste connaît les équipements et les contraintes de continuité du froid. L'administrateur réseau ou le prestataire IT maîtrise les communications et les protections qu'il administre. Une analyse de sécurité complémentaire examine comment **les accès, les permissions et les dépendances peuvent se combiner jusqu'à permettre une modification du procédé**. Ces compétences se recoupent parfois et gagnent à être confrontées au fonctionnement réel des installations.

Une évaluation ciblée est particulièrement utile lors de la reprise d'une télémaintenance, de la mutualisation d'accès entre clients, d'une interconnexion GTC/GTB, d'une migration de supervision ou lorsque des paramètres et alarmes présentent des anomalies inexpliquées. Elle peut commencer par les documentations, schémas et configurations fournis **avec autorisation**, sans essai intrusif sur le procédé.

Le résultat attendu est concret : **cartographier les chemins d'accès, vérifier les droits d'écriture effectifs, mesurer le périmètre potentiellement affectable et identifier les protections indépendantes**. Les constats doivent distinguer ce qui est observé, documenté, hypothétique ou inconnu.

### Des mesures proportionnées aux risques et aux moyens

La priorité est de protéger les fonctions critiques avec des mesures **compatibles avec l'exploitation, maintenables et proportionnées aux moyens disponibles**. Réduire un droit excessif, révoquer un accès oublié, documenter une responsabilité ou tester la réception d'une alarme peuvent être plus utiles qu'ajouter immédiatement une nouvelle solution technique.

Une dépense ou un remplacement d'équipement se justifie lorsque les protections disponibles ne permettent plus de maîtriser un risque important. La restitution doit présenter les gains attendus, les contraintes d'intervention et les risques résiduels, afin de permettre une décision adaptée au métier.

## Conclusion

La cybersécurité du froid commercial exige de comprendre **les chemins qui relient un accès numérique à un pouvoir de modification**, puis les conséquences de ce pouvoir sur les équipements, les denrées et les interventions.

Une installation correctement cloisonnée peut encore concentrer des privilèges excessifs ; un superviseur disponible peut masquer une chaîne d'alerte défaillante ; une plateforme commune peut, selon ses autorisations, se limiter à la consultation ou permettre d'agir sur plusieurs sites. **Ces différences se vérifient dans les configurations, les droits et le fonctionnement réel.**

**Qui peut agir, sur quoi, jusqu'où, et quelles protections demeurent indépendantes si cet accès est compromis ?** C'est à partir de ces réponses que l'exploitant, le frigoriste, l'informatique et la cybersécurité peuvent prioriser des mesures proportionnées et préserver la continuité du froid.

### Sources et portée de l'article

- [CERT-FR — Alerte CERTFR-2026-ALE-012, version du 16 octobre 2026](https://www.cert.cyber.gouv.fr/alerte/CERTFR-2026-ALE-012/).
- [ANSSI — Guide d'hygiène informatique : 42 mesures (2017)](https://messervices.cyber.gouv.fr/guides/guide-dhygiene-informatique).
- [ANSSI — Recommandations de sécurité relatives à la GTB/GTC (2026)](https://messervices.cyber.gouv.fr/guides/recommandations-gestion-technique-batiment).
- [Equans / Axima Réfrigération — Froid commercial pour la GMS et gestion technique centralisée](https://www.equans.fr/nos-solutions/refrigeration/froid-pour-la-grande-distribution).
- [Danfoss — Passerelle X-Gate et intégration aux systèmes de gestion de bâtiment](https://www.danfoss.com/fr-fr/products/dcs/electronic-controls/products/x-gate-gateway-solution/).
- [ANSSI — La cybersécurité des systèmes industriels : mesures détaillées](https://messervices.cyber.gouv.fr/guides/la-cybersecurite-des-systemes-industriels-mesures-detaillees).
- [Danfoss — Remote Connection to AK-PC Pack Controller : fonctions de régulation et télémaintenance (2024)](https://assets.danfoss.com/documents/latest/375642/AQ486315982105en-000101.pdf).
- [Danfoss — AK-SM 800A Series : Session Control et recommandations de mise à jour](https://www.danfoss.com/en/service-and-support/downloads/dcs/adap-kool-software/ak-sm-800a/).
- [Danfoss — Tech Insider, version AK-SM 800A R4.3.1 (août 2025)](https://assets.danfoss.com/documents/latest/525773/AO535443524000en-010101.pdf).
- [Copeland — XWEB PRO 1.12, politique de mots de passe (septembre 2025)](https://webapps.copeland.com/dixell/Content/Pages/AdvertisingMaterials/ProductNews/PN03-september-25-XWEB-PRO-v.1.12.pdf).
- [Copeland — XWEB PRO, bulletin de sécurité (février 2026)](https://webapps.copeland.com/dixell/Content/Pages/SystemSoftwareUpdate/XWEB-PRO/Security%20update%20%20XWEB-PRO%20-%20Product_Bulletin_February_2026.pdf).
- [Copeland / Dixell France — Recommandations pour une utilisation sécurisée des superviseurs XWEB (14 octobre 2026), avec compléments français](https://dixell.fr/wp-content/uploads/2026/10/Recommandations-pour-une-utilisation-securisee-des-superviseurs-XWEB-v1.0.pdf).
- [Copeland — XWEB EVO, fin de support logiciel (mars 2026)](https://webapps.copeland.com/dixell/Content/Pages/SystemSoftwareUpdate/XWEB30005000-EVO/XWEB%20EVO%20EoS%20-%20Product_Bulletin_March_2026.pdf).
- [Copeland/Dixell — XWEB PRO Operating Manual r1.2](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XWEBPRO/XWEBPROV1.2OPERATION/XWEB-PRO-OPR-EN.pdf).
- [Copeland/Dixell — XR60CX, notice d’utilisation FR : paramètres `US` et `SEt`](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XR-CX/XR60CX/XR60CX-FR.pdf).
- [Copeland — Bonnes pratiques de sécurité pour les supervisions XWEB (2024)](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XWEB/SECURITYBESTPRACTICES/Product_Bulletin.pdf).
- [ANSSI — Authentification multifacteur et mots de passe, coffre-fort de mots de passe](https://messervices.cyber.gouv.fr/documents-guides/anssi-guide-authentification_multifacteur_et_mots_de_passe.pdf).
- [Union européenne — Règlement (CE) n° 37/2005, contrôle des températures des aliments surgelés](https://eur-lex.europa.eu/legal-content/FR/TXT/?uri=CELEX%3A32005R0037).
- [AFNOR — NF EN 12830:2018, exigences relatives aux enregistreurs de température](https://www.boutique.afnor.org/fr-fr/norme/nf-en-12830/enregistreurs-de-temperature-pour-le-transport-le-stockage-et-la-distributi/fa187120/81468).

Cet article de sensibilisation et de méthode prolonge l'[étude indépendante Danfoss/XWEB v1.0](https://github.com/lxrider/danfoss-xweb-case-study/blob/main/ETUDE.md), dont il reprend les distinctions entre accès, autorisation, écriture acceptée et effet physique. **Au niveau des incidents, la provenance de la première écriture, l'usage d'une identité multisite et l'éventuelle compromission d'un prestataire restent indéterminés.** Les capacités constructeur citées sont documentées pour des familles et versions précises ; leur déploiement chez les victimes reste inconnu. Les guides ANSSI relatifs à la GTB/GTC et aux systèmes industriels orientent les contrôles, à adapter aux configurations frigorifiques.

Cet article a fait l’objet d’une **relecture documentaire interne**. Une revue technique indépendante par un frigoriste ou un intégrateur habilité reste à obtenir.

Les retours critiques et contributions documentées de professionnels du froid et de la cybersécurité sont bienvenus, publiquement ou en privé lorsque la confidentialité l'exige, **sans divulguer d'identifiants, journaux bruts ni configurations de sites tiers**.

---

*Version 1.0 — 22 octobre 2026 — publication initiale. Auteur : lxrider. © 2026 lxrider — Tous droits réservés pour le texte original. Schémas vectoriels conceptuels ; illustration d’ouverture générée, non documentaire. Les marques et documents cités demeurent la propriété de leurs titulaires respectifs.*
