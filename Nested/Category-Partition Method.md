https://dl.acm.org/doi/pdf/10.1145/62959.62964

Communicated in the above paper.

Relevant quote:

"The total number of
frames generated from an unrestricted specification is
equal to the product of the number of choices in each
category."

The input space of a function is separated into "categories," or interesting sets. (Can these be overlapping? Seems like yes.) A category is a set of inputs / dimension that are interesting in some way. For example, if the input is a list of integers, the list size could be a category. "Choices" are specific example of a category, in the list size case, some choices could be: 0 elements (empty), list with 1 element (small # of elements), list with 1,000 elements (large number of elements).

These categories then get combined via cartesian product to generate test cases. Constraints can be placed on their interaction to further curate the test cases.

A generator tool can be used to take the category / choice specification and create test cases according to the constraints as well. 
