+++
title = "Cybersécurité du froid commercial : qui peut agir sur vos installations, et jusqu'où ?"
description = "Des accès aux consignes de froid : comprendre les droits, les chemins d'action et leurs conséquences."
date = 2026-10-22
draft = false
template = "doc-page.html"

[extra]
article_version = "1.1 — révision éditoriale publiée"
author = "lxrider"
rights = "© 2026 lxrider — Tous droits réservés"
+++

Dans un magasin, plusieurs intervenants accèdent aux installations frigorifiques. Le frigoriste consulte les températures, l'intégrateur configure la supervision et le prestataire informatique administre les accès réseau.

Mais **quelles sont leurs capacités d'action réelles ?** Consulter une température, modifier une consigne, inhiber une alarme ou atteindre un autre système ne demandent pas les mêmes droits.

Cette analyse s'intéresse aux **capacités d'action**, pas seulement aux équipements visibles sur Internet : quels chemins sont accessibles, quelles permissions permettent d'agir et jusqu'où s'étendent les conséquences possibles ?

Les incidents d'octobre 2026 concernant des supervisions Danfoss et Copeland/Dixell ont rappelé l'importance de ces questions. Le [CERT-FR](https://www.cert.cyber.gouv.fr/alerte/CERTFR-2026-ALE-012/) recommande notamment de supprimer les accès de gestion directement exposés sur Internet. Les informations publiques ne permettent pas pour autant d'établir un chemin initial unique de compromission.

Le [guide pratique de sécurisation des supervisions](https://lxrider.github.io/publications/securiser-supervision-frigorifique/) explique comment protéger les accès distants. Ici, la question est différente : **que pourrait faire une identité compromise et qu'est-ce qui limiterait son pouvoir ?**



## 1. Quels chemins mènent aux installations ?

Le premier travail consiste à reconstruire les accès. Ceux que tout le monde connaît, mais aussi ceux qui ont pu être ajoutés au fil des interventions.

Un superviseur peut être joignable depuis le réseau du magasin, un VPN, un PC froid, une box avec redirection de port ou un routeur cellulaire. Une plateforme extérieure peut donner accès à plusieurs établissements. Et un changement de prestataire ne fait pas nécessairement disparaître tous les anciens chemins.

Sur le terrain, les fonctions sont réparties entre des **régulateurs**, qui assurent la conduite locale, et des **systèmes de supervision**, qui collectent des informations et peuvent, selon les configurations et les droits, transmettre des commandes. Un Danfoss AK-SM et un régulateur AK-PC n'ont pas le même rôle. Les familles XWEB présentent elles aussi des fonctions différentes selon les générations.

Dans certaines installations, la gestion technique centralisée du froid (**GTC**) échange avec une gestion technique du bâtiment (**GTB**) ou une supervision multisite. La connexion mérite d'être examinée, mais **un échange d'informations ne prouve pas qu'un système peut commander l'autre**.

**Par quels chemins les équipements sont-ils joignables, et qui maîtrise ces chemins ?** Un schéma simple aide à poser les bonnes questions, avant de vérifier les liaisons et les permissions sur le site réel.

<figure class="research-figure">
<a class="figure-open" href="00-exemple-architecture-capacites.svg" target="_blank" rel="noopener" aria-label="Ouvrir l'exemple d'architecture">
<img src="00-exemple-architecture-capacites.svg" alt="Schéma fictif d'un accès via VPN et PC froid jusqu'à la supervision et aux régulateurs, avec chemin hypothétique vers la vidéosurveillance en pointillés." loading="lazy" decoding="async">
</a>
<figcaption>Exemple d'architecture — Du PC froid aux régulateurs. Le chemin vers la vidéosurveillance est une hypothèse à vérifier, pas une interconnexion constatée.</figcaption>
</figure>

## 2. Se connecter donne-t-il le pouvoir de modifier ?

Imaginez un compte capable d'ouvrir l'interface d'un superviseur. On sait qu'un accès existe. On ne sait pas encore ce que ce compte peut changer.

Pour qualifier une capacité d'action, quatre vérifications sont nécessaires : l'équipement est-il **joignable**, l'identité est-elle **reconnue**, possède-t-elle les **droits nécessaires**, et l'ordre est-il **accepté et appliqué** par le régulateur ?

<figure class="research-figure">
<a class="figure-open" href="02-acces-supervision-lxrider.svg" target="_blank" rel="noopener" aria-label="Ouvrir la figure 1">
<img src="02-acces-supervision-lxrider.svg" alt="Les quatre vérifications entre un accès réseau et une action sur le procédé : accessibilité, authentification, autorisation et application." loading="lazy" decoding="async">
</a>
<figcaption>Figure 1 — Entre l'accès réseau et l'action sur le froid, quatre vérifications.</figcaption>
</figure>

Une consultation de température, un acquittement d'alarme, une modification de consigne et une opération d'administration n'ont pas les mêmes conséquences.

La documentation de [XWEB PRO](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XWEBPRO/XWEBPROV1.2OPERATION/XWEB-PRO-OPR-EN.pdf) distingue par exemple la lecture des paramètres, leur modification et l'envoi de commandes. Chez [Danfoss](https://www.danfoss.com/en/service-and-support/downloads/dcs/adap-kool-software/ak-sm-800a/), les niveaux de permission et les services disponibles dépendent également du produit, de la version et des réglages.

Ces documentations montrent ce que les systèmes *peuvent* autoriser. **Les capacités d'action effectives dépendent des comptes, des droits et des réglages propres à chaque site.**

C'est le principe du **moindre privilège** : attribuer à chacun uniquement les droits nécessaires à sa mission. Consulter des températures ne nécessite pas de modifier des consignes ; intervenir sur un magasin ne justifie pas des droits d'écriture sur tous les sites. Lorsque les équipements le permettent, séparer consultation, maintenance et administration réduit le pouvoir d'un compte compromis.

Il faut aussi distinguer la possibilité technique du droit d'intervenir : un technicien peut disposer d'un compte puissant pour la maintenance, sans être autorisé à modifier tous les paramètres pendant un simple diagnostic.

## 3. Jusqu'où une modification peut-elle se propager ?

Une même identité peut parfois modifier un équipement isolé ; ailleurs, elle dispose de droits sur plusieurs régulateurs, une centrale, voire plusieurs magasins. Son périmètre d'action ne s'arrête pas nécessairement au superviseur.

Le *blast radius* désigne le **périmètre potentiellement affectable** par une compromission : les fonctions modifiables, les équipements et les sites concernés, mais aussi les autres systèmes éventuellement accessibles depuis un équipement compromis.

Prenons un exemple précis. Sur le régulateur [Dixell XR60CX](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XR-CX/XR60CX/XR60CX-FR.pdf), le paramètre **SEt** définit une consigne, tandis que **US** peut fixer sa limite supérieure. Si une même autorité peut modifier la consigne **et** sa limite, cette limite ne constitue plus une protection indépendante contre cette autorité.

Cela ne signifie pas que toutes les installations utilisant ce régulateur sont exposées. Les droits effectifs, les protections physiques et la configuration doivent être vérifiés.

La même prudence s'applique aux plateformes multisites. **Voir plusieurs magasins sur un tableau de bord ne signifie pas pouvoir les modifier tous.** À l'inverse, un compte autorisé à écrire sur plusieurs sites concentre une capacité d'action qu'il faut connaître et limiter.

### Et si l'accès dépassait le froid ?

Le **mouvement latéral** désigne la progression d'un adversaire d'un système compromis vers d'autres systèmes. C'est une possibilité à examiner dans les architectures où un **PC froid** ou un superviseur communique avec d'autres réseaux du magasin. Exemple : un chemin vers une infrastructure de vidéosurveillance. Cette question ne se limite plus aux températures ou aux régulateurs. Elle concerne aussi les capacités d'action sur les systèmes voisins. [MITRE ATT&CK](https://attack.mitre.org/tactics/TA0008/) documente ce type de progression.

**Une communication possible ne démontre pas qu'un mouvement latéral soit réalisable.** Il faut vérifier les règles de filtrage, les services exposés, les comptes utilisables et les permissions obtenues. L'accès à l'interface Web d'un superviseur ne prouve ni un accès à son système d'exploitation ni un pouvoir d'administration sur la vidéosurveillance.

D'autres actions méritent la même analyse : altération de paramètres, inhibition d'alarmes ou suppression d'historiques, **si les droits et les fonctions disponibles les rendent possibles**. Ces exemples sont des hypothèses de capacités, pas des faits établis pour les incidents d'octobre 2026.

Il faut ensuite relier chaque capacité au procédé : quelles consignes peuvent changer ? Quels dégivrages, seuils ou programmes sont concernés ? Quelles sécurités resteraient indépendantes ?

Une protection mécanique peut préserver un compresseur sans garantir la température des denrées. Le risque se mesure donc aussi au regard du **service que l'installation doit assurer**.

## 4. Si la supervision disparaît, qui surveille le froid ?

Un incident numérique ne produit pas toujours un arrêt immédiat des installations. Certains régulateurs peuvent continuer de fonctionner localement lorsque la connexion Internet ou le superviseur est indisponible.

En revanche, **le froid peut continuer à être produit alors que personne ne reçoit plus les alarmes à distance**.

<figure class="research-figure">
<a class="figure-open" href="03-regulation-alarmes-lxrider.svg" target="_blank" rel="noopener" aria-label="Ouvrir la figure 2">
<img src="03-regulation-alarmes-lxrider.svg" alt="Régulation locale, supervision et transmission des alarmes : fonctions liées mais distinctes, avec exemple de surveillance indépendante." loading="lazy" decoding="async">
</a>
<figcaption>Figure 2 — Le froid peut continuer alors que les alarmes ne remontent plus.</figcaption>
</figure>

Je distingue donc trois situations : perte de liaison extérieure, indisponibilité du superviseur et rupture des communications avec les régulateurs. Leurs conséquences varient selon l'architecture et l'autonomie locale des équipements. La figure illustre des fonctions possibles, pas le comportement garanti d'un site particulier.

La surveillance des températures de conservation mérite une attention propre. Si ses seuils, sa transmission ou ses comptes d'administration dépendent du système qui pilote le froid, une modification de paramètres peut aussi rendre une alerte inefficace.

Lorsque le risque le justifie, **une chaîne de mesure et d'alerte indépendante** peut réduire cette dépendance : sonde distincte, seuils validés, transmission maîtrisée et astreinte réellement joignable. Encore faut-il vérifier l'ensemble, y compris lors des dégivrages, en cas de panne réseau et lorsque les moyens habituels ne sont plus disponibles.

La reprise soulève une autre question : **quel état restaure-t-on ?** Une sauvegarde récente peut contenir des paramètres déjà altérés. [Dixell France](https://dixell.fr/wp-content/uploads/2026/10/Recommandations-pour-une-utilisation-securisee-des-superviseurs-XWEB-v1.0.pdf) précise en outre que, pour XWEB PRO et EVO, une sauvegarde générale du superviseur ne conserve pas les paramètres des régulateurs ni les historiques de fonctionnement.

La vérification du retour à la normale doit donc aller jusqu'aux équipements et jusqu'à la réception effective des alertes par l'astreinte. Un véritable PCA/PRA demanderait une analyse complémentaire.

## 5. Que vérifier, sans compliquer inutilement le métier ?

Une évaluation utile ne commence pas forcément par un nouvel outil ou un test intrusif. Elle peut partir de quelques questions concrètes :

- **Accès :** savons-nous comment chaque intervenant rejoint les installations, y compris par les chemins secondaires ?
- **Moindre privilège :** qui peut lire, modifier une consigne, changer ses limites ou envoyer une commande, et ces permissions sont-elles nécessaires ?
- **Périmètre :** quels autres équipements, magasins ou systèmes critiques seraient atteignables depuis l'accès considéré ?
- **Continuité :** que perdons-nous si un superviseur, une liaison ou la télésurveillance devient indisponible ?
- **Maîtrise :** qui peut couper un accès, restaurer une configuration fiable et confirmer que les alarmes arrivent à destination ?

Le frigoriste connaît le procédé et les contraintes de maintenance. Le prestataire réseau maîtrise les communications qu'il administre. Le spécialiste en cybersécurité relie les **expositions, identités, privilèges et chemins d'action**, y compris entre systèmes, pour mesurer les conséquences possibles. Ces regards se complètent, sans imposer les mêmes compétences à chacun.

La réponse doit rester proportionnée aux moyens de l'entreprise. Supprimer un ancien accès, réduire un privilège ou tester une alarme peut avoir plus de valeur que multiplier les équipements de sécurité. Quand un matériel obsolète ou une dépendance critique empêche de maîtriser le risque, il faut en revanche envisager des mesures compensatoires ou son remplacement.

L'objectif est de produire **quelques décisions vérifiables et maintenables**, plutôt qu'un catalogue de préconisations que personne ne pourra appliquer.

## Conclusion

La question de départ paraît simple : *qui peut agir sur nos installations frigorifiques ?*

La réponse se construit en reliant les accès, les identités, les droits d'écriture, les équipements réellement commandables, les chemins possibles vers d'autres systèmes et les protections qui resteraient actives en cas de défaillance.

C'est dans ces relations que se trouvent les chemins de compromission possibles et les concentrations d'autorité les plus importantes à maîtriser.

**Connaître ces chemins permet de protéger ce qui compte : le froid, les denrées et la capacité d'intervenir.**

### Références essentielles

- [CERT-FR — Alerte sur les équipements de supervision frigorifique, octobre 2026](https://www.cert.cyber.gouv.fr/alerte/CERTFR-2026-ALE-012/).
- [ANSSI — Recommandations de sécurité relatives à la GTB/GTC](https://messervices.cyber.gouv.fr/guides/recommandations-gestion-technique-batiment).
- [Danfoss — Documentation AK-SM 800A et contrôle des sessions](https://www.danfoss.com/en/service-and-support/downloads/dcs/adap-kool-software/ak-sm-800a/).
- [Copeland/Dixell — XWEB PRO, manuel d'exploitation](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XWEBPRO/XWEBPROV1.2OPERATION/XWEB-PRO-OPR-EN.pdf).
- [Copeland/Dixell — XR60CX, notice française](https://webapps.copeland.com/Dixell/Content/Pages/Manuals/XR-CX/XR60CX/XR60CX-FR.pdf).
- [Dixell France — Recommandations de sécurisation XWEB, octobre 2026](https://dixell.fr/wp-content/uploads/2026/10/Recommandations-pour-une-utilisation-securisee-des-superviseurs-XWEB-v1.0.pdf).

Pour les éléments techniques de l'incident et leurs limites : [étude indépendante Danfoss/XWEB v1.0](https://github.com/lxrider/danfoss-xweb-case-study/blob/main/ETUDE.md).

*Les parcours et schémas sont conceptuels. Les capacités décrites proviennent de documentations techniques ; leur activation sur une installation particulière doit être établie. Les vecteurs et droits utilisés lors des incidents d'octobre 2026 restent partiellement inconnus.*

---

*Version 1.1 — 22 octobre 2026 — révision éditoriale publiée. © 2026 lxrider — Tous droits réservés. Schémas conceptuels, à confronter aux configurations réelles.*
