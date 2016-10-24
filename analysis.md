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
Les classes AbstractSlackSessionImpl et SlackWebSocketImpl sont volumineuses et complexes.

# Analyse qualitative du projet
AbstractSlackSessImpl - Complexe et pas understandable (LOC, understandability)
Nombre d'appels par classe complexe ou VERS ces classes complexes
Récursivité?
Number of message sends elevé
Voir si il y a beaucoup d'interactions entre les classes avec complexite élevée
Métriques sont à prendre avec un grain de sel - pour le web socket, le McCabe est elevé, mais beaucoup de cela provient des Switch cases qui sont bien gérés.


# Améliorations pour Ptidej
Tooltips pour définitions des boutons des métriques (déjà disponible dans le code avec getDefinition())
Ptidej retourne comme quoi Memento existe mais ne dit pas dans quelle classe.

## Research questions
Quelle sont les hypothèses pour l'étude empirique. (Ex:  C'est une classe complexe, mais la complexité vient des redirecitons)


# Annex
Source : An Exploratory Study of the Impact of Code Smells on Software Change-proneness
Code smells definitions: 
AbstractClass: This code smell is characteristic of the Speculative Generality Antipattern. This odor exists when we have generic or abstract code that isn’t actually needed
today. Such code often exists to support future behavior, which may or may not be necessary in the future.
ChildClass: This code smell occurs when the number of methods declared in a class and the number of it’s declared attributes is very high. It is a symptom of poor object
decomposition. The public interface of the class differing greatly from the one of its super-class. This code smell characterises the Tradition Breaker antippatern.
ClassGlobalVariable: This code smell occurs when a class declares public class variable that are used as “global variable” in procedural programming.
ClassOneMethod: This code smell occurs when a class has only one method.
ComplexClassOnly: This code smell is present when a class both declares many fields and methods and which methods realise complex treatments, using many if and switch
instructions. Such a class is probably providing lots of services while being difficult to maintain and fragile due to its complexity.
ControllerClass: This odor is present when a class monopolises most of the processing done by a system, takes most of the decisions, and closely directs the processing of
other classes.
DataClass: This code smell is present when a class contains only data and performs no processing on these data. It is composed of highly cohesive fields and accessors.
FewMethod: This code smell characterise Lazy classes that declare few methods.
FieldPrivate: This code smell is present when many private fields are declared. It’s generally symptomatic of the Functional Decomposition antipattern.
FieldPublic: This code smell is symptomatic of the Class Data Should Be Private antippatern. It occurs when the data encapsulated by a class is public, thus allowing client
classes to change this data without the knowledge of the declaring class.
FunctionClass: This code smell occurs when we have a main class, i.e., a class with a procedural name, such as Compute or Display. It is symptomatic of the Functional
Decomposition antipattern.
HasChildren: This code smell describes classes with many children.
LargeClass: This odor concerns classes that are trying to do too much. These classes do not follow the good practice of divide-and-conquer which consists of decomposing a
complex problem into smaller problems. These classes also have low cohesion.
LargeClassOnly: This code smell concerns classes with a very high number of attributes and/or methods defined.
LongMethod: This odor is a method with a high number of lines of code. A lot of variables and parameters are used. Generally, this kind of method does more than its name
suggests it.
LongParameterListClass: This odor corresponds to a method with high number of parameters. This smell occurs when the method has more than four parameters. Long lists
of parameters in a method, though common in procedural code, are difficult to understand and likely to be volatile.
LowCohesionOnly: This code smell characterises the lack of cohesion in a class.
ManyAttributes: This code smell occurs when the number of attributes declared in a class is too high.
MessageChainsClass: This code smell is present when you see a long sequence of method calls or temporary variables to get some data. This chain makes the code dependent
on the relationships between many potentially unrelated objects.
MethodNoParameter: This code smell occurs when a class declares methods with no parameter.
MultipleInterface: This code smell occurs when a class implements a high number of interfaces. It is generally symptomatic of the Swiss Army Knife antipattern.
NoInheritance: This odor is present when inheritance is scarcely used.
NoPolymorphism: This odor is present when polymorphism is scarcely used.
NotAbstract: This odor occurs when a developer haven’t yet seen how a higher-level abstraction can clarify or simplify his code.
NotClassGlobalVariable: This odor manifest itself in the anipattern Anti-Singleton when a class declares public class variable that are used as “global variable” in procedural
programming. It reveals procedural thinking in object-oriented programming and may increase the difficulty to maintain the program.
NotComplex: This code smell characterises classes performing “atomic” functionality, with little complexity.
OneChildClass: This code smell occurs when a class does not have child class.
ParentClassProvidesProtected: This code smell occurs when a subclass does not use attributes and/or methods protected inherited by a parent.
RareOverriding: This code smell occurs when a class rarely overrides inherited attributes and/or methods.
TwoInheritance: This odor characterises a hierarchy with a depth greater than two.


Metrics
http://www.ptidej.net/publications/documents/IJCIS14.doc.pdf
The metric suite (lines 9-11) encompasses both static and dynamic metrics. The
static metric suite includes (but is not limited to) the following metrics: number of
methods declared (NMD), number of incoming references (NIR), number of outgoing
references (NOR), coupling (CPL), cohesion (COH), average number of parameters in
methods (ANP), average number of primitive type parameters (ANPT), average number of accessor methods (ANAM), and average number of identical methods (ANIM).
The dynamic metric suite contains: number of method invocations (NMI), number
of transitive methods invoked (NTMI), response time (RT), and availability (A).
The DSL shown in Figure 2 can be extended by adding new metrics and–or
values. Later in Section 4.6 (see Table 6), we specify and detect three new SOA
antipatterns, which require four new metrics. We also add these metrics to our
DSL. The new metrics (line 11) include: number of services encapsulated (NSE),
total number of parameters (TNP), number of interfaces (NI), and number of utility
methods (NUM). Utility methods provide facilities with logging, data validation, and–
or notifications, rather than any business functionalities

Possiblement redondant :  http://www.ptidej.net/publications/documents/TSE09.doc.pdf
A set of metrics was identified during the domain analysis,
including Chidamber and Kemerer metric suite [53], such as
depth of inheritance DIT, lines of code in a class LOC CLASS,
lines of code in a method LOC METHOD, number of attributes
in a class NAD, number of methods NMD, lack of cohesion
in methods LCOM, number of accessors NACC, number of
private fields NPRIVFIELD, number of interfaces NINTERF,
or number of methods with no parameters NMNOPARAM. The
choice of the metrics is based on the taxonomy of the smells,
which highlights the measurable properties needed to detect a
given smell. This set of metrics is not restricted and can be
easily extended with other metrics.