Refactoring is very tricky, because your ability to refactor depends on the state of the tests. If the tests are too coupled to the implementation, you will need to change the tests along with the implementation code. This will make the change more time consuming, as it will involve more decisions: [[Decisions and Time]].

I wonder how to make tests less fragile. I think it has something to do with the black swan effect: [[Probability]], i.e. the more costly a change is to make, the more we should plan for it to happen. Because even if it is improbable, the cost if it does happen is very high.

It also always seems like whatever tests you have, you want the opposite when refactoring. When you are changing low level details, you wish you had high level tests. When you are changing high level structure, you wish that edge case checking is preserved.

[[Test Case Migrations]]
What if there is a way to have both sets of tests, but only use one or the other depending on the refactoring task you're doing?