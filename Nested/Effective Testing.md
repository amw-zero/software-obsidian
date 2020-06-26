Another subjective topic is what makes a test "effective"? There are many dimensions to consider. 

- Does the test case catch a bug with the code in its current state? 
- Does the test case catch a bug when the code is changed in the future?
- Does the test case enable refactoring?
- Does the test case take a long time to run?
- Does the test case inspire confidence about the software as a whole doing what is intended?

Some hypotheses:

- Tests should be focused at system boundaries. This is to ensure they are not thrown away in a redesign. i.e., fully isolated unit tests that stub out all dependencies can't be "effective" in this definition. There are infinite ways to restructure designs without modifying external behavior (and I mean really external, i.e. visible to a user). And externally visible / consumed behavior is really all that matters. If a design change requires modifying hundreds or thousands of tests, that change just became more costly. 
- However, internal system boundaries almost certainly have to be made. Otherwise all testing would be done from outside, and this would definitely lead to testing a gigantic, extremely complex system instead of testing individual parts. My guess is that it makes the most sense to have a few internal system boundaries, say less than 10. An example might be a data access layer, or a networking layer. 	