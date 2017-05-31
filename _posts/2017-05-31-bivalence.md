---
layout: post
title: 'The Principle of Bivalence: Are There Only Two Truth Values?'
---

# **Work In Progress!**

## Thoughts

Gödel has shown that there exist statements in mathematics that are true, yet unprovable. We must be very careful of the assumptions that Gödel's proof makes, as only when they are assumed true does the theorem hold. Here's a thought: one fundamental assumption with this proff (and mathematics in general) is that all math statements can either be true or false, known as the principle of bivalence. This binary relationship means that one implies the negation of the other (a implies not not a).

Humans can contradict themselves regularly, yet the universe doesn't implode. Perhaps our reasoning is a natural consequence of something deeper and more fundamental than bivalence (see dialetheism for the view that some statements can be true and false simultaneously). What if we create a system not based on this binary view of the world? What could this system look like? There exist logic systems not based on bivalence, which include paraconsistent logic and many-valued logic (can be countable or uncountable, e.g., n-valued logic, fuzzy logic, probabilistic logic). Suszko's Thesis shows that any n-valued logic can be expressed using bivalent logic, but fuzzy/probabilistic logic is still an open question. We must be careful to note that uncountable numbers cannot be represented with 100% accuracy, both using classical or a quantum computation. Does this mean that the discrete nature of computation ultimately boils down to a very expressive bivalent logic? Or maybe an intermediate approximation of real-valued logic that is somehow more powerful?

## Related Musings

Somehow rectifiers (ReLUs) are more effective than the sigmoid function. Rectifiers have the activation function, 
```
y = max(0, x),
```
where `y = x` for `x > 0` and `y = 0` for `x < 0`. Could a system of logic be devised for this function?

1. There exists falsehood `x = 0` or truth `x > 0` for a real value `x in [0, inf]`.
2. For any pair of truths `a,b`, if `a > b`, `a` has greater truth than `b`.

## Links
- [Principle of bivalence](https://en.wikipedia.org/wiki/Principle_of_bivalence)
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
