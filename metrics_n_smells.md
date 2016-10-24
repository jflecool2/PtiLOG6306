

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