# Description du projet
Nous avons choisi d'analyser une librairie Java qui sert d'API pour Slack, une application de chat en équipe. Le projet, trouvé ici https://github.com/Ullink/simple-slack-api, contient donc des fonctionalités pour créer un bot permettant d'interagir avec l'API de Slack.

Le projet contient des tests, mais ceux-ci seront ignorés pour l'analyse qui suit.

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

Pour la suite des analyses, nous concentrerons nos efforts sur les ComplexClass, car nous voudrons analyser leur impact sur l'exécution du programme (voir sections Identification du problème et Analyse qualitative du projet).

## Métriques:

Les métriques analysées ici se rapportent à celles qui ont été jugées complexes par les design smells. Seulement certaine métriques ont été choisies pour appuyer l'analyse présentée dans la section Analyse du projet.

LOC pour le projet* : 5473

Entity	LOC*	McCabe*	CBO	CIS	DCC	LCOM1	LCOM2	NAD	NOA	NOF	NOM
SlackWebSocketSessionImpl	793	147	2	29	2	1632	729	37	2	37	43
AbstractSlackSessionImpl	374	74	0	63	0	3782	1829	28	2	28	63
SlackJSONMessageParser	403	103	0	2	0	812	377	1	1	1	30
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
AbstractSlackSessionImpl	0.7155828845	0.02777777778	1.614716701	13.14825397	29.87698413	-18.61535967
SlackJSONMessageParser	0.1303976993	0.02777777778	0.133235219	0.1682539683	0.376984127	-8.030915225
SlackFile	-0.06960230071	0.02777777778	-0.11676478	-0.3917460317	-0.87301587	0.8790847753
SlackAttachment	-0.06960230071	0.02777777778	-0.11676478	-0.3917460317	-0.87301587	0.8790847753
SlackPersonaImpl	1.330397699	-0.4722222222	3.133235219	3.568253968	7.876984127	-5.720915225

Cela nous permet d'identifier les outliers. Dans ce contexte, il semble que les classes les plus frappantes sont AbstractSlackSessionImpl et SlackWebSocketImpl car leur Effectiveness, Flexibility et Functionality et Reusability sont élevés, mais leur Extensibility et Understandability sont faibles. Cela nous dit que ces classes sont à risque de problèmes.


## Code Smells
Le code smell le plus fréquent est ChildClassDetection, en voici donc son analyse.

ChildClassDetection.png

Parmi ceux-ci, seulement SlackJSONMessageParser se retrouve également dans la liste des ComplexClass.


# Identification du problème de fonctionnement/conception
La classe AbstractSlackSessionImpl est volumineuse et complexes.

# Analyse qualitative du projet
Nombre d'appels par AbstractSlackSessionImpl vers d'autres classes complexes ou de d'autres classes complexes vers AbstractSlackSessionImpl
Y a-t-il de la récursivité?
Voir si il y a beaucoup d'interactions entre les classes avec complexite élevée
Number of message sends elevé
Métriques sont à prendre avec un grain de sel - pour le web socket, le McCabe est elevé, mais beaucoup de cela provient des Switch cases qui sont bien gérés.


# Améliorations pour ptidej
Tooltips pour définitions des boutons des métriques (déjà disponible dans le code avec getDefinition())
Ptidej retourne comme quoi Memento existe mais ne dit pas dans quelle classe.

## Research questions
Quelle sont les hypothèses pour l'étude empirique. (Ex:  C'est une classe complexe, mais la complexité vient des redirecitons donc ce n'est pas une complexité grave.)
