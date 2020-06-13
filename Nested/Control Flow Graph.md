A program can be modeled as a control flow graph. Each node is a "basic block," or a series of atomic instructions, and each edge is a branch in code - either a conditional or a function call. Furthermore, function calls can have a direction associated with them, i.e. a "Command" or "Query." Commands "push" data further downard in the CFG, whereas quereies "pull" data upwards. Each edge can also be represented by a type, which is the type of value that flows along that edge.

     Int   String
O ---> O --->

Knowing the type of value that flows along the edge is useful, because from the type you can know the full set of values that the type can represent. [[Type Cardinality]]