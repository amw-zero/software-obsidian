My hypothesis is that most accurate estimate for the amount of time that a change will take to make is that it's directly proportional to the number of decisions in some way. Note that I think it's purely related to the quantity of decisions, not the complexity of each decision. It is probably quadratically related as well, and not linearly, i.e.

time = d^2 

where d is the number of decisions to be made.

Example:

When consuming an API, if there are multiple ways to retrieve the same data, this requires a decision. When there is an inconsistency or a vague part of the requirement, this will also lead to a decision. When the change would require modifying several different components, and it seems like the design won't handle the change well at all, this requires decisions for whether or not to refactor, as well as how to refactor. 

All of these decisions add up to much more than the raw time to implement a given behavior, and we don't do a good job at quantifying or planning for them.