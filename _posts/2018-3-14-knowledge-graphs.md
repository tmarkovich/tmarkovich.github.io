---
layout: post
title: "Knowledge Graphs for Enhanced Machine Reasoning at Forge.AI"
category: engineering
comments: true
---
## Introduction

Natural Language Understanding at an industrial scale requires an efficient,
high quality knowledge graph for tasks such as
[entity resolution](https://en.wikipedia.org/wiki/Record_linkage#Entity_resolution) and [reasoning](https://en.wikipedia.org/wiki/Reasoning_system).
Without the ability to reason about information semantically, natural language
understanding systems are only capable of shallow understanding. As the
requirements of machine reasoning and machine learning tasks become more complex,
more advanced knowledge graphs are required. Indeed, it has been previously
observed that knowledge graphs are capable of producing impressive results when
used to augment and accelerate machine reasoning tasks at small scales, but
struggle at large scale due to a mix of data integrity and performance issues.
Solving this problem and enabling machine driven semantic reasoning at scale is
one of the foundational technological challenges that we are addressing at [Forge.AI](https://forge.ai).

<!--more-->

To understand the complexity of this task, it's necessary to define what a
knowledge graph is. There are many academic definitions floating around, but
most are replete with jargon and impenetrable. Simply said, a knowledge graph
is a graph where each vertex represents an entity and each edge is directed and
represents a relationship between entities. Entities are typically proper nouns
and concepts (e.g. Apple and Company, respectively), with the edges representing
verbs (e.g. Is A). Together, these form large networks that encode semantic
information. For example, encoding the fact that "Apple is a Company" in the
knowledge graph is done by storing two vertices, one for "Apple" and one for
"Company", with a directed edge originating with Apple and pointing to Company
of type "isA". This is visualized in Figure 1:

![]({{ site.url }}/assets/apple.svg)
<p align="center">
<strong>Figure 1:</strong> Visualized simple knowledge graph representing the fact that "Apple is a
Company"
</p>

A knowledge graph encodes many facts, each through the use of a directed edge.
Each vertex can have many facts connected to it, making this ultimately a
directed multigraph. This type of representation provides an intuitive way to
reason about queries. For example, from the knowledge graph represented in
Figure 1 we can reason about the question "Is apple a company?" by simply
walking through the graph, starting at "Apple" and walking to "Company", testing
edges and concepts along the way. In production, knowledge graphs tend to be
quite large and complex with millions or billions of edges. Such a large amount
of knowledge allows us to use these graphs to easily reason about semantic
connections for tasks such as enriching business relevant data and resolving
entities. At Forge.AI, we perform these tasks as part of our NLP / NLU pipeline
for extracting individual events from unstructured text into a machine-readable
format.

With a working definition of a knowledge graph in hand, we will next explore
some of the use cases that we've found for the knowledge graph here at
Forge.AI. Then, we'll explore the graph infrastructure to understand what
powers these use-cases. Finally, we'll discuss part of our road map to explore
what's next for Forge.AI and its knowledge graph.

## Use Cases
It's worth grounding our conversation of the knowledge graph in a few use-cases
before we jump too deeply into a detailed discussion of the infrastructure, how
it works, and where we're going. In general, a knowledge graph can be used for
a wide range of applications including entity resolution, dependency analysis,
filtering, and machine reasoning. In the ensuing discussion, we will focus on
entity disambiguation and dependency analysis, two of the many tasks that we
use the knowledge graph for at Forge.AI.

### Entity Disambiguation
While simple to state, the problem of entity disambiguation is one of the most
frequent problems that we need to solve when reasoning about a document. While
this problem is fairly straightforward to handle in cases where the relevant
ontology is known and fully enumerated, it can quickly become difficult when
that is not the case. To explore how we can handle this problem with the
knowledge base, let's consider the problem of determining which "Apple" is
being referenced in the quote below:

> "Though the company doesn't break out individual unit sales by model, Apple says it sold 77.3 million iPhones — a decrease from the 78.2 million iPhones it sold in the same period in 2017."

Obviously, this is "Apple" the corporation, not "apple" the type of fruit. How
did our brains determine this? We used contextual clues! We know that the
Apple Corporation sells the iPhone because the type of fruit is incapable of
selling anything. Based on these contextual clues alone, we are able to perform
this task nearly instantaneously using our reasoning.The ForgeAI knowledge
graph works in the same way: when we seek to disambiguate an entity, we provide
the knowledge graph with a set of co-located entities that provide the graph
with the appropriate context. However, machine learning systems do not work
like our brains do and for a machine learning system to reason with context, we
need a knowledge graph. Our knowledge graph then searches for all versions of
"Apple" on the full graph and constructs small graphs that include contextual
information as can be seen in Figures 2 and 3. Note, this is a noisy string
search that is capable of finding versions of the initial search term that may
differ from the original string or contain the search string as a substring. We
also keep a look up table of known aliases for each of our entities, where
aliases can be things like CIK codes or ticker symbols.

![]({{ site.url }}/assets/er2.svg)
<p align="center">
<strong>Figure 2:</strong> Visualized excerpt from the Knowledge Graph that pertains to the entity Apple
the fruit.
</p>

![]({{ site.url }}/assets/er1.svg)
<p align="center">
<strong>Figure 3:</strong> Visualized excerpt from the Knowledge Graph that pertains to the entity Apple,
the consumer electronics corporation.
</p>

With these small graphs in hand, the knowledge graph then uses machine reasoning
to determine which of the entities is truly being referenced. There are many
strategies to doing this but we have found that a greedy algorithm which seeks
to maximize the overlap between the contextual entities passed in and the small
graphs under consideration is effective.

### Dependency Analysis
Another major task that we've found the knowledge graph to be useful for is
dependency analysis. That is, to determine the relationship between two or more
entities. This is most useful when attempting to determine whether an extracted
event is something that a customer would care about, given their stated
interests. To make this concrete, let's consider the following news story in
regards to a customer that is interested in news events relating to Samsung:

> "Russia’s Norilsk Nickel has teamed up with Russian Platinum to invest $4.4bn to develop mining projects in Siberia, which contains some of the world’s richest deposits of platinum and palladium. The two companies will form a joint venture to develop projects in the Taimyr Peninsula in Russia’s far north with an aim to become the world’s largest producer of the precious metals, they said Wednesday."

It's certainly not obvious to me how this story is connected to Samsung. The
question at hand is to determine whether this news event is related to Samsung
and, if so, the nature of that relation so we can determine whether or not to
pass this event to our customer. We begin by constructing small graphs around
each of the entities. With these graphs in hand, we then compute a path given
Dijkstra's algorithm between each of the marked endpoints. An example of such
a path is given in Figure 4.

![]({{ site.url }}/assets/ir1.svg)
<p align="center">
<strong>Figure 4:</strong> Visualized excerpt from the Knowledge Graph that pertains to the relationship
between the Norilsk platinum group metals mine in Siberia, Russia and Samsung.
</p>

What we see in Figure 4 is that the knowledge graph believes that Iridium is a
Platinum Group Metal, and that Platinum Group Metals are mined in Norilsk. We
also see that the Knowledge Graph believes that Iridium is used in Organic
Light Emitting Diodes (or OLEDs), which just happen to be used in Samsung
phones. Therefore, this news event is likely relevant to our customer. In fact,
this event is highly relevant to our customer’s interest in Samsung because
Iridium is incredibly important to the production of OLED screens due to its
ability to make a blue LED. Indeed, Samsung has even funded researchers at MIT
and Harvard to explore alternatives to Iridium for OLED screens.

This type of dependency analysis is illustrative of the power of a well formed
knowledge graph and it is critical for machine enabled semantic reasoning. It's
easy to imagine this type of dependency analysis having uses not only in the
financial services industry, but also in work as wide ranging as supply chain
risk assessment and nuclear nonproliferation applications -- just to name a few.

## Graph Infrastructure
In addition to standard graph features, we choose to endow each fact that is
stored in the knowledge graph with the time at which the edge was added and a
confidence for that edge. The time dependence intuitively follows from the
observation that the totality of human knowledge grows and changes over time.
Ultimately, this makes the graph dynamic, which is a natural feature of human
knowledge itself.

There are a small number of facts that I'd be willing to bet my life on --
something like Auston Matthews is a Toronto Maple Leaf -- and a great many facts
that I'd be willing to bet $20 dollars on -- for example, the Boston Massacre
happened in 1770. Both are true but, due to the amount of information that I've
recently read, I know considerably more about the former than the latter and,
therefore, am more confident about it. Motivated by this, we have designed our
knowledge graph such that each edge has weights which we choose to interpret as
confidences. This data enables us to capture the inherent uncertainty necessary
to model a fast changing world and to reason about the validity of queries. By
virtue of the graph being probabilistic, we are able to embrace true Bayesian
reasoning as we attempt to evaluate a query, as well as provide query specific
priors to up or down weight an assertion based on the origin (e.g. a company's
own statements about a new product release should be up-weighted over twitter
rumors).

One of the most exciting engineering challenges of knowledge graphs is their
size. It is not uncommon to have a knowledge graph with more than 1 billion
facts and 50 million vertices; this can easily require hundreds of gigabytes of
RAM. Even more concerning than the memory requirements is the computational cost
of computing even basic graph properties such as the path length between
vertices. We have taken two complementary approaches to ensure that our graph
algorithms are as quick as possible. First, because our edges are interpreted
as probabilities, it is possible to set a probability cutoff beyond which we
are not interested in graph connections. This allows us to only consider graph
algorithms over highly restricted subsets of the graph, which provides us with
major algorithmic improvements. Second, we have engineered the data structure
to remain as cache coherent as possible by representing our knowledge graph as
a sparse three rank tensor in an attempt to optimize the per-fact throughput
through the CPU.

We also have a clear route towards efficient parallelization by exploiting what
we are terming the "galactic structure" of the graph. While this is not a
general feature of all graphs, we have observed that there are highly connected
clusters of vertices that are only weakly connected to one another. Intuitively,
this makes sense. For example, consider domains such as the Toronto Maple Leafs
and modern particle physics -- there is little overlap between these fields and
therefore no need to reason over a graph that contains both clusters of highly
interconnected vertices when reasoning about [Dave Keon](https://en.wikipedia.org/wiki/Dave_Keon), the Toronto Maple Leafs
legend. This galactic structure provides us with a promising route towards
efficient parallelization using commodity hardware.

## Where are We Going?
We've just started to teach the knowledge graph and show it how to perform basic
reasoning. The following are some of the many additional features that we are
adding that will ensure the accuracy, robustness, and efficiency of the graph
long into the future.

### Probabilistic Reasoning
Giving the knowledge graph the ability to reason probabilistically about the
validity of facts allows it to hold conflicting facts or hypotheses and
evaluate them later in the presence of more evidence. This can additionally be
used to evaluate nuance of queries. This can be achieved by using techniques
such as softening the axiomatic constraints that power the machine reasoning
engine and building ontology-specific Bayesian models. We anticipate that using
these techniques should make our knowledge graph more resilient to internal
errors.

### Automatic Fact Checking
Of course, if we have a collection of facts that we intend to use as our
internal source of truth to augment business data, we should ensure that this
set of facts is correct. With our current knowledge graph size, we can perform
this fact checking using a mix of manual spot checking and axiomatic constraint
testing (e.g. a person can only be born in one country). This is the standard
technique for evaluating the correctness of knowledge graphs. As with most
machine learning tasks, this is incredibly person intensive and, therefore,
expensive. Additionally, it's difficult to scale this technique to large graphs.
To address these issues, we're excited to explore techniques related to
hinge-loss Markov random fields that are directionally aware. In addition to
being efficient, this allows us to look at a fact such as "Florida namedAfter
Flo Rida" and swap the directionality, instead of having to first infer that
we need to delete this edge and then infer that the reverse edge should be
present.

### Automatic Graph Enrichment
Because it's simply not possible to have humans continually teach the
knowledge graph, our system is being constructed to be capable of learning
facts on its own. There are many ways to do this including: tracking
unexplained queries, generalizing local and global graph features to infer new
facts from patterns, and using semantic information. Intuitively, this might
look like finding patterns such as "Companies tend to have a CEO" and one of
the companies in our graph does not currently have a CEO. Therefore, we should
enrich this region of the graph specifically relating to the specific company
and the existence of the CEO. To achieve this, we are actively exploring
modifications of techniques such as the path rank algorithm and graph embedding
methods as well as information retrieval techniques from the internet and other
sources. This is proving to be an exciting path of inquiry.

### Graph Dynamics
Modeling the influence of specific edges on the connectivity of two marked
vertices in a graph is fundamental to understanding network resilience. In the
context of a knowledge graph, this provides us with information about the
influence of this fact. Intuitively, if we imagine that the vertices in our
graph are cities and the edges roads, with the edge weights corresponding to
the width of those roads (e.g. 0.1 is a one lane road and 1.0 is a 6 lane super
highway), then the time to travel between two different cities indicates the
strength of their connection. With many alternative routes and many wide
highways, we can say that those cities are tightly connected. Mathematically,
the problem can be thought about in terms of a two point correlation function
for a collection of random walks over the graph. These are discrete random
walks whose dynamics can be modeled with a discrete Green's function. By taking
advantage of the connection between discrete Green's functions on a graph of
random topology and discrete Laplace equations, we've preliminarily found that
it is possible to evaluate the influence of changing an edge. We're excited to
formalize and harden this connection and expose these measures to aid in
producing more advanced models.

The knowledge graph at Forge.AI is a crucial element of our technology stack
and it has exciting potential for further development. We look forward to
sharing with you further insights in the coming months.

Note: This post was originally published on the Forge.AI blog:
[https://www.forge.ai/blog/knowledge-graphs-for-enhanced-machine-reasoning-at-forge.ai](https://www.forge.ai/blog/knowledge-graphs-for-enhanced-machine-reasoning-at-forge.ai)
