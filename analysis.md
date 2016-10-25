# Description du projet
Pour ce projet, nous devions utiliser un projet pré-existant. Notre choix s'est arrêté sur un API de Slack amateur. Slack est un service de clavardage et notification pour équipe de développement informatique. Plusieurs raisons nous ont ammené a ce choix: le projet est amateur, donc plusieurs améliorations seront possible. Le projet est fait en Java, ce qui a l'air d'être le langage le plus fonctionnel aved Ptidej. Le projet est relativement facile a compiler. Le projet est pas trop grand ni trop petit. Le projet contient des tests, mais ceux-ci seront ignorés pour l'analyse qui suit.

# Analyse du projet
Pour commencer, voici une analyse générale du projet, tirée de Ptidej (commit ID : f811903). Cette analyse est accomplie sans les classes de test. 

Number of classes: 60
Number of ghosts: 135
Number of interfaces: 64
Number of association relationships: 33
Number of aggregation relationships [1,n]: 5
Number of aggregation relationships [1,1]: 83
Number of composition relationships: 0
Number of container-aggregation relationships [1,n]: 18
Number of container-aggregation relationships [1,1]: 42
Number of container-composition relationships: 0
Number of creation relationships: 82
Number of use relationships: 15
Number of fields: 260
Number of methods: 696
Number of message sends: 1249


## Design Motifs
Ptidej indique la présence d'un Singleton dans la classe SlackChatConfiguration. De plus, il indique qu'un Memento existe, sans préciser dans quelle classe.

Ce sont les deux seuls motifs trouvés par Ptidej. Pourtant, le code contient plusieurs Factories (SlackSessionFactory et ChannelHistoryModuleFactory) et Builders (SlackSessionFactoryBuilder et SlackPreparedMessage).

## Design smells
Ptidej trouve les design smells suivant pour le projet :

Long parameter list
SlackMessagePostedImpl, SlackChannelImpl, SlackBotImpl, SlackPersonaImpl, SlackUserImpl, SlackWebSocketSessionImpl

ComplexClass
AbstractSlackSessionImpl, SlackWebSocketSessionImpl, SlackAttachment, SlackFile, SlackJSONMessageParser, SlackPersonaImpl

LazyClass
SlackJSONFieldsFormatter, SlackBotImpl

Pour la suite des analyses, nous concentrerons nos efforts sur les ComplexClass, car nous voudrons analyser leur impact sur l'exécution du programme (voir sections Identification du problème et Analyse qualitative du projet). Plus particulièrement, nous nous concentrerons sur AbstractSlackSessionImpl et SlackWebSocketSessionImpl.

## Métriques:

Les métriques analysées ici se rapportent aux classes qui ont été jugées complexes par les design smells. Seulement certaine métriques ont été choisies pour appuyer l'analyse présentée dans la section Analyse du projet.

**Faire un graphique**
LOC pour le projet* : 5473

Entity	LOC*	McCabe*	CBO	CIS	DCC	LCOM1	LCOM2	NAD	NOA	NOF	NOM
SlackWebSocketSessionImpl	793	147	2	29	2	1632	729	37	2	37	43
SlackJSONMessageParser	403	103	0	2	0	812	377	1	1	1	30
AbstractSlackSessionImpl	374	74	0	63	0	3782	1829	28	2	28	63
SlackFile	157	43	0	0	0	0	0	0	0	0	0
SlackAttachment	131	41	0	0	0	0	0	0	0	0	0
SlackPersonaImpl	131	19	1	18	1	306	135	18	2	18	19

*Malheureusement, Ptidej ne retourne aucun résultat pour LOC et McCabe, qui sont deux métriques pouvant apporter beaucoup d'information. Ainsi, nous avons fait rouler une instance de Sonarqube (version 6.1) pour obtenir ces résultats.

## Quality
Plutôt que d'afficher ici un tableau montrant L'Effectiveness, l'Extendability, la Flexibility, la Functionality, la Reusability et l'Understandability, nous avons choisi de calculer la moyenne de toutes les classes pour chaque métriques. Voici le résultat:

Effectiveness	Extendibility	 Flexibility	Functionality	Reusability	Understandability
0.2272294194	 0.3662900188	0.1167647807	55.83174603	126.8730159		 -84.29916952

En tournant notre attention spécifiquement vers les classes plus complexes (et donc plus intéressantes pour nous, voir Design Smells pour explication du choix), et qu'on compare leur resultat à la moyenne du projet (Vclasse - Vmoyenne), on obtient:

Classe	Effectiveness	Extendibility	Flexibility	Functionality	Reusability	Understandability
SlackWebSocketSessionImpl	2.124992294	-0.9722222222	4.626478463	6.108253968	13.37698413	-13.31983414
SlackJSONMessageParser	0.1303976993	0.02777777778	0.133235219	0.1682539683	0.376984127	-8.030915225
AbstractSlackSessionImpl	0.7155828845	0.02777777778	1.614716701	13.14825397	29.87698413	-18.61535967
SlackFile	-0.06960230071	0.02777777778	-0.11676478	-0.3917460317	-0.87301587	0.8790847753
SlackAttachment	-0.06960230071	0.02777777778	-0.11676478	-0.3917460317	-0.87301587	0.8790847753
SlackPersonaImpl	1.330397699	-0.4722222222	3.133235219	3.568253968	7.876984127	-5.720915225

Cela nous permet d'identifier les outliers. Dans ce contexte, il semble que les classes les plus frappantes sont AbstractSlackSessionImpl et SlackWebSocketImpl car leur Effectiveness, Flexibility et Functionality et Reusability sont élevés, mais leur Extensibility et Understandability sont faibles. Cela nous dit que ces classes sont bel et bien à risque de problèmes.


## Code Smells
Le code smell le plus fréquent est ChildClassDetection, en voici donc son analyse.

ChildClassDetection.png

Parmi ceux-ci, seulement SlackJSONMessageParser se retrouve également dans la liste des ComplexClass.


# Identification du problème de fonctionnement/conception
La classe AbstractSlackSessionImpl est volumineuse et complexes.

# Analyse qualitative du projet

## Appels des classes complexes entre elles
La classe AbstractSlackSessionImpl contient plusieurs méthodes recevant en entrée SlackAttachment, une classe également identifiée comme complexe. Elle contient aussi un paramètre SlackPersona, dont l'implémentation est jugée complexe.

En analysant les références VERS AbstractSlackSessionImpl, on trouve la classe SlackWebSocketSessionImpl qui extend carrément AbstractSlackSessionImpl, ce qui la rend très complexe. SlackJSONMessageParser a de nombreuses méthodes recevant un SlackSession en entrée. Cependant, la référence vers la session est généralement utilisée pour simplement faire un findUserById ou findChannelById, qui sont des opérations assez simples.

SlackWebSocketSessionImpl a quant à elle une méthode faisant référence à SlackJSONMessageParser, deux références à des SlackAttachment dans l'input de méthodes et plusieurs références à des SlackPersona.

Il y a donc quand même quelques références d'une classe complexe à l'autre qui pourraient être des pistes de problèmes.

## Recursivité
On ne trouve pas de références de AbstractSlackSessionImpl vers une elle-même, ce qui est bon signe, car cela évite la récursivité. Il ne semble pas y avoir d'appels de méthodes permettant de créer une boucle, non plus. Il en va de même pour SlackWebSocketSessionImpl.

## Asynchronisme
La classe AbstractSlackSessionImpl contient de nombreuses méthodes publiques permettant de créer des Listeners. Cela cause une complexité difficile à évaluer, car on se retrouve avec des processus asynchrones. Par contre, l'utilisateur doit manuellement créer ces Listener, car les méthodes les créant ne sont jamais appelées, ou sont appelées par des méthodes n'étant elle-même jamais appelées. L'utilisateur implémentant un Bot doit donc lui-même créer ces Listeners et prendre conscience qu'il ajoute une asynchronicité à son application.

La classe SlackWebSocketSessionImpl, quant à elle, envoie de nombreux messages par des dispatch. Ceci dit, ces messages sont destinés aux listeners établis dans AbstractSlackSessionImpl, la réflexion est donc la même que pour le paragraphe précedent.

## Analyse des métriques
L'analyse des métriques révèle que la classe AbstractSlackSessionImpl est d'une certaine complexité, mais cela est à prendre avec un grain de sel, car de nombreuses fonctionalités sont simplement des findXById qui itèrent sur les éléments et les testent pour les retourner ou non. De la même manière, SlackWebSocketSessionImpl a de nombreux switch case, qui sont la meilleure manière d'accomplir ce qu'ils essaient de faire dans ce contexte.

# Améliorations pour ptidej
Tooltips pour définitions des boutons des métriques (déjà disponible dans le code avec getDefinition())
Ptidej retourne comme quoi Memento existe mais ne dit pas dans quelle classe.

## Research questions
Quelle sont les hypothèses pour l'étude empirique. (Ex:  C'est une classe complexe, mais la complexité vient des redirecitons donc ce n'est pas une complexité grave.)
