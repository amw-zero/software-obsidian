Current software is hopelessly unreliable. It's no surprise, given the results that [[Software Analysis]] show us: programs become complicated extremely quickly. The number of possible inputs can be astronomical. 

One system for dealing with this is input-space partitioning, of which the [[Category-Partition Method]]

I see two main paths for getting reliable software: formal verification, and [[Randomized Exploratory Testing]].

For reliablity over time,  [[Effective Testing]] is essential. Not only do test cases need to catch bugs when a change is happening, but they also can't hamper refactoring too much, otherwise they are very costly.

