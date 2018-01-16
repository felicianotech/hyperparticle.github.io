---
layout: post
title: >-
  The Principle of Bivalence: Can There Be More Than Two Truth Values?
---

# **Work In Progress!**

## Thoughts

Gödel has shown that there exist statements in mathematics that are true, yet unprovable. We must be very careful of the assumptions that Gödel's proof makes, as only when they are assumed true does the theorem hold. Here's a thought: one fundamental assumption with this proof (and mathematics in general) is that all math statements can either be true or false, known as the principle of bivalence. This binary relationship means that one implies the negation of the other (a implies not not a).

Humans can contradict themselves regularly, yet the universe doesn't implode. Perhaps our reasoning is a natural consequence of something deeper and more fundamental than bivalence (see dialetheism for the view that some statements can be true and false simultaneously). What if we create a system not based on this binary view of the world? What could this system look like? There exist logic systems not based on bivalence, which include paraconsistent logic and many-valued logic (can be countable or uncountable, e.g., n-valued logic, fuzzy logic, probabilistic logic). Suszko's Thesis shows that any n-valued logic can be expressed using bivalent logic, but fuzzy/probabilistic logic is still an open question. We must be careful to note that uncountable numbers cannot be represented with 100% accuracy, both using classical or a quantum computation. Does this mean that the discrete nature of computation ultimately boils down to a very expressive bivalent logic? Or maybe an intermediate approximation of real-valued logic that is somehow more powerful?

## Related Musings

### Metamathematics

What is mathematics? A simple question, yet mathematicians disagree so much that there is no official definition. What intrigues me is *why* we do mathematics in the manner that we do it in. To unpack that a bit: in school we are taught the foundations of logic, i.e., there are two truth values, basic operations on these true values, and some basic axioms to help solve certain logic problems. This kind of reasoning has its usefulness, certainly, but there are many other ways of formulating a logical system.

When creating a new logical system, we must be careful to consider the meta-logic we are using to solve the logic in the first place. For instance, if we use probabilistic logic as our universe, reverting back to bivalent logic to make logical inferences between mathematical propositions can fundamentally affect how the axiomatic system works. As explained in Douglas Hofstadter's GEB, this ability to pop-out of systems and talk about logic itself within logic is strange indeed. This recursive looping to jump out of the incomplete theory to construct a more complete meta-theory is both profound and useful. In this framework, there is no truth, there is no falsity, there is only reason to reason about itself. 

In my view it is the rigidness of mathematics that we have imposed on ourselves is the very thing that is preventing us from creating a logical AI system that should transcend these very rules in the same way humans do. To create a system that is truly intelligent is to generalize so well that the system has generalized generality itself and rendered it obsolete. This is the key idea that I believe is missing in modern "AI" techniques. A machine learning algorithm is only as good as the data you give it, and does not generalize beyond what it has seen to imagine completely new and arbitrary scenarios. Part of the problem is that this kind of system is very hard to define, perhaps undefinable in simple terms as to be an emergent phenomenon after sufficient complexity (and luck) is reached.

### A different logical system

Somehow rectifiers (ReLUs) are more effective than the sigmoid function. Rectifiers have the activation function, 
```
y = max(0, x),
```
where `y = x` for `x > 0` and `y = 0` for `x < 0`. Could a system of logic be devised for this function?

1. There exists falsehood `x = 0` or truth `x > 0` for a real value `x in [0, inf]`.
2. For any pair of truths `a,b`, if `a > b`, `a` has greater truth than `b`.

### Useful logical systems

We want to describe some logical systems as being more _useful_ than others. The notion of usefulness can be intuitively thought of as the ability for it to solve practical problems in the real world (but is this the right way to think about it?). Consider the logical system with one lone axiom:
```
The only theorem that exists is this one.
```
Not very useful, is it? Yet it is both consistent and contradiction-free.

## Links
- [Principle of bivalence](https://en.wikipedia.org/wiki/Principle_of_bivalence)
- [Law of exculded middle](https://en.wikipedia.org/wiki/Law_of_excluded_middle)
- [Many-valued logic](https://en.wikipedia.org/wiki/Many-valued_logic)
- [Paraconsistent logic](https://en.wikipedia.org/wiki/Paraconsistent_logic)
- [Fuzzy logic](https://en.wikipedia.org/wiki/Fuzzy_logic)
- [Dempster–Shafer theory](https://en.wikipedia.org/wiki/Dempster%E2%80%93Shafer_theory)
- [Suszko's Thesis](https://plato.stanford.edu/entries/truth-values/suszko-thesis.html)
- [Dialetheism](https://en.wikipedia.org/wiki/Dialetheism)
- [Principle of explosion](https://en.wikipedia.org/wiki/Principle_of_explosion)

## Further Reading

- [Intuitionistic logic](https://en.wikipedia.org/wiki/Intuitionistic_logic)
- [Intermediate logic](https://en.wikipedia.org/wiki/Intermediate_logic)
