Starting a couple of months ago, CIn-UFPE's Software Engineering Laboratory started promoting seminars from its members. The goal is to make laboratory members aware of work being conducted by other members. So far, I've greatly enjoyed these seminars. In one of them, I learned a bit about cloud computing and also about electronic government (which seems to be an area of research that sits between Computer Science and Political Science) from Prof. [Vinicius Garcia](http://viniciusgarcia.com). Last week we had one given by Prof. [Alexandre Mota](https://sites.google.com/site/acmrecife/). One of the things I admire about Alexandre is that he is a hard core Formal Methods with a remarkable track record of collaboration with industry. 

Alexandre's talk started with an overview of formal methods. According to Alexandre, formal methods are a combination of notations, tools, and a semantics based on mathematics. Their use can help developers to ascertain that there is no ambiguity, imprecision and (for some systems) incompleteness in a system specification. This is the **good** side of formal methods. The **bad** side is that, to get from a specification to a correct system, there is a myriad different paths and each one of them is tough to traverse. And this is assuming that a formal specification exists. Going from an informal specification to a formal one is a very difficult problem in and of itself. It requires a thorough validation and a specialist. 

Alexandre also discussed the process of program refinement, going from a formal specification to executable code in a way that guarantees that the generated code satisfies the specification. According to him, there are two paths to go from formal specification to a correct system: (i) by data and operation refinement; and (ii) by [refinement calculus](http://www.cs.ox.ac.uk/publications/books/PfS/) (using a set of refinement rules). In the former approach, we're transitioning from a specification *S0* to a specification *S1* and *S1* is not determined by pre-established laws. One needs to propose and verify each refinement. It is possible to apply more broad-scoped transformations using this approach. In the latter, the transformations one can apply to the specification are constrained by the laws one is using. This is good because the proof obligations are relatively straightforward. The bad part is that it is more constraining and requires very experienced engineers. The "search" in refinement calculus may never end. Both approaches require searching for the specifications and for the theorems that must be proved. It is a search problem and leads us to the problem of program synthesis. 

After presenting a (critical) view of formal methods, he narrowed down the scope of his talk, which focused on the subject of program synthesis. The problem of program synthesis can be summarized as follows: Build a system that, given a specification and optionally a set of program fragments, produces as output a program that satisfies (*refines*?) the given specification and also includes the given program fragments. Program synthesis is a search problem because a large part of the work performed by a program synthesis system consists of searching through a space of candidate programs and checking whether they satisfy the specification. This problem is tough. In fact, it is [SAT](https://en.wikipedia.org/wiki/Boolean_satisfiability_problem), the most famous [NP-complete](https://en.wikipedia.org/wiki/NP-completeness) problem.

The area of program synthesis started a long time ago, before computer. In 1932, famous soviet mathematician [Andrey Kolmogorov](https://en.wikipedia.org/wiki/Andrey_Kolmogorov) already talked about algorithms with proofs that come from constructive mathematics. This is very distant from what is currently discussed as program synthesis. A particularly important system in this area, which appeared much latter, was the [Kestrel Interactive Development System](http://www.kestrel.edu/home/projects/kids/) (KIDS). KIDS supported program synthesis from a complex program specification by means of a series of manually applied refinement steps (maybe it couldn't be strictly called a program systhesis system) and, according to its homepage, has been used to develop many applications in such diverse areas as scheduling, combinatorial design, sorting and searching, computational geometry, pattern matching, and mathematical programming. KIDS came close to a commercial breakthrough, which is impressive. 

According to Alexandre, there are three main approaches to program synthesis: 

- development of synthesizers that are specialized in a particular domain and which employ domain-specific languages. An example of such approaches is the use of the VHDL language to  These approaches are scalable but constrained to specific kinds of systems. 

- development of synthesizers that translate a particular language into SAT/SMT language. General purpose, but usually focusing on specific problems, such as security. 
DSL => SAT/SMT Solver

Ex., Sketch, by Armando Solar-Lezama. Given specification and a code fragment, Sketch fills in the code that the programmer did not provide. Ex.: 

harness void doubleSketch(int x){ 
  int t = x + ??
  assert t = x + x;
}

The focus is on expressions. Finds vector indexes, parts of loops, concurrent data structures, bit streams. 

- synthesizers that take as input a general purpose high-level specification
  + This is Alexandre's line of work
  Follows along the lines of Saurabh Srivastava's work (U. Maryland), the VS3 approach. 
  Basic idea of Srivastava's work: tough to read and long specifications. Alexandre, Juliano and a student didn't manage to make the tool run!
  
The PSAtCIn Approach
- Alloy* Synthesizer
- Isabelle/HOL
- DSL that targets C# code. 

Much simpler code: 
Vars x, y, z: int
Pre:
Post: x'= y and y' = x
  
z = x;
x = y;
y = z;

Another one:

Vars a, b: int
Pre:
Post: a' = gcd(a,b)
Sketch: while (a!= b)

Generates Euclid's algorithm.

What's under the hood:
  Alloy*
  Syntax and semantics of IMP
  Correctness predicate

DSL => Alloy* (BNF + semantics) => Relational Solver (Kodkod)

There's an Eclipse plugin supporting this approach.
(photo)

What PSAtCIn features:
 - Scope search (all approaches need this but only this one has this in a "simple and easy way")
 - Sketching
 - Full sketching
 - Semantics-based program synthesizer (Just us -- this approach can be extended for different languages as long as they have formal syntax and semantics)
 
Current challenges: 
- scope of the atoms (for the Alloy specs.). They want to provide this kind of information automatically in the future.
- data domains

The way solvers such as Alloy work creates some obstacles:

pre:
post:
x' = y AND y' = x 

this can be refined to SKIP if x == y. So, the following pre is necessary:
x != y and x != t and y != t

The tool can do this implicitly to avoid burdening users of the tool. 
