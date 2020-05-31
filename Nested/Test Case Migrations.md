There should be a way to automatically merge / select test cases between software modules. 

let useCase = (input, repository, translator) => [data]

Here, we have a use case which is a function of a repository adn a translator. The repository fetches data from a http server, and the translator takes in strings and translates them to the user's current locale settings. There are two main strategies for testing a function like this: 

1) Test entirely via the useCase function, which will test the repository and transator implicitly. 
2) Test the respository and translator separately, and stub their values in the useCase tests

With #1, there can be a swift explosion of input data combinations. In order to test the repository and translator control flow paths we'd have to specify input data for every branch and conditional. When the repository or translator are used as dependencies in other functions - the same input data combinations will have to be supplied to every function that used them if we test them in the same way.

With #2, we run the risk of creating the wrong abstractions, or at least want to change them over time. Once we have written the test cases against a specific design, the test is now coupled to that design.

#1 and #2 have the same number of test cases. 

What if we could merge the test cases from #2 back up into the test cases for #1. Then, a refactor can be performed, and finally the cases can be moved back down to the dependencies.

The process is as follows:

1) Move the test cases from a dependency up to a consumer of the dependency
2) Modify the design
3) Move the test cases back down into the dependency