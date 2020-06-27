# 3: Unearthing Concepts
“If the problem is solved, and you choose to refactor now rather than later, you pay the opportunity cost of not being able to work on other problems. Spending time "improving" code based purely on aesthetics may not be the best use of your precious time.”

“Code that needs to be changed must be changeable.”

“Conditionals breed”

# 5: Separating Responsibilities
“However, there’s a big difference between a conditional that selects the correct object and one that supplies behavior. The first is acceptable and generally unavoidable. The second suggests that you are missing objects in your domain.”

Factories vs. objects

“Primitive Obsession is when you use one of these data classes to represent a concept in
your domain.”

“It’s easy to imagine creating objects that stand in for things, but the power of OO is that it lets you model ideas.”

So different from the “objects model the real world” mentality

“First, the existing Bottles tests become the safety net for this new class. They were originally written as unit tests, but using them to indirectly test BottleNumber transforms them into a kind of integration test.”

Talk about tests and refactoring - tests can prevent refactoring 

“One of the best things about immutable objects is that they are easy to understand and reason about. These objects never start out one way and then secretly morph into something else. You can be confident that what you see at creation time is always what you get later.”

“The benefits of immutability are so great that, if it were free, you’d choose it every time. “

# 6: Achieving Openness

