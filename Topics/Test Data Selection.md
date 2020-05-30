# How to best select test inputs?
Do the inputs communicate the desired behavior celarly?

Do the inputs catch bugs before a change is made? (prevention)

Do the inputs catch bugs after a change is made? (regression)

Do the inputs prevent change?

# What is a program?
A program maps input data to output data via a control flow graph.

Example program:

Create a program that prints the letter "A" if x is 5 or x is 17, otherwise it prints the letter "B." 

This program maps a number x to the letters "A" or "B".  

if (x == 5 || x == 17) {
  print("A")
} else {
  print("B")
}

x and y are the inputs, and the control flow graph is this:

O [Root]
|-- O [A] (x == 5 || x == 17) "A"
|-- O [B] (else) "B"

It has exactly 2 paths: Root --> A, Root --> B.

As an example, here are the invocations of this program:

program(x = 5)    // "A"
program(x = 4)   //  "B" 

Rollercoaster - start the input data at the beginning, rides down the path according to its value

There are two paths, and we traversed both of them with these two test cases. But, this implementation would be equally valid for these test cases:

if (x == 5) {
  print("A")
} else {
  print("B")
}

Run the test cases through that program

This does not meet the desired behavior. When we write a program, we are constructing a control flow graph along with the inputs that it can process. We have to focus on both the inputs and the paths in order for the program to work as expected.

The program should print "A" when x is 5 or 17, so for the tests to be really effective, they have to test for both of these. Even though there are only two paths in the program, we require 3 tests.

------------

An interactive programs is an infinite loop that processes input and displays the result of each interaction:

Ruby:

loop do
  render(process(input)))
end

JS:

while (true) {
  render(process(input()))
}

Pseudocode:

loop {
  input |> process |> render
}

# The Futility of Exhaustive Testing
Our example program is a function from an integer to two letters. The domain is the set of all integers, which is infinite, but on a computer is practically 2^64 (19 zeros). The range of letters the program outputs is { "A", "B" } - only 2 possible outputs. To exhaustively test this, we would need to have more test cases than there are grains of sand on planet earth (7.5x10^18)

Combinatorial explosion, product rule

f(x, y) 

2^64 * 2^64 = 2^128

Futile endeavor and ultimately a fool's errand, sometimes

# Common Sense Partitions
^ Sort of a bad argument

"Domain analysis"

Full input domain = { 1, 2, 3, ... infinity } (real numbers)

Obviously for this program there are only 3 partitions though: { 5 }, { 17 }, { any number except 5 and 17 }

The problem with these is that they require understanding the requirements of what we're building in order to make the partitions. 

# Composite Types: Product and Sum Types
First order predicate logic, conjunction + disjunction

https://en.wikipedia.org/wiki/Logical_connective#/media/File:Logical_connectives_Hasse_diagram.svg

Product type:

{
  rank: int
  suit: suit
}

So called because they represent the product of all of the constituent types, i.e int * suit, AND

Sum type:

enum suit [:hearts, :diamonds, :clubs, :spades]

So called because they represent the sum of the individual cases, OR, i.e. there are only 4 possible suit values.

Enum values almost always are treated all alike or all different. 

Tip: When testing enum values, use different cases for each of because they are inherently distinct.

Space is a great example:

space = {
  suite: "Suite 10A",
  type: [:retail]
}

n(string) is infinite, but only a single partition { all strings }. No special rules for strings
n(space type) is 3, [:retail, :office, :industrial]

# It's all about the inputs
Graphs are O(n^2)

Input combinations are O(2^n)

# The Tests I'd Write

90% of app is forms and tables, and profiles. aka commands and queries, and there's a frontend and backend component, i.e.

|           | Form | Table | Profile |
|------|------|-------|---------|
| Frontend | | | |
| Backend | | | |

6 different categories of behavior, modeled as pure functions:

Frontend table:

makes network request to server, displays results:

table(dataService) => 

Frontend