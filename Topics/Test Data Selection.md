# Delivering Mail
I want to talk about selecting test data for test cases, because it's a deceptively deep topic. But first, I want to tell a quick allegory:

You are a mail person, tasked with delivering mail along a specific route. Each day, new pieces of mail come in, and you drop them off at their proper destinations. You go five years without a delivery error - new pieces of mail get routed to the correct mailboxes each time. Today, however, you see a piece of mail for an address that you don't recognize: 150 Shady Lane. You're in a rush, so you just bundle it with the mail for 152 Shady Lane, the house with the bright red front door that you pass every day. 

(The route is a call graph. The mail are the side effects of the program.)

# Pattern Language
- Bubble up the inputs
- Specify the types
- Form the domain
- Sculpt the partitions

**Bubble up the inputs**: Often, there is hoardes of implicit state, buried deep within nested objects. To be explicit about what the true inputs are, write the behavior as a pure function where all inputs must be passed in. This makes the domain explict.

**Specify the types**: Once the inputs are known, create type definitions for them. This makes us explicitly list any constraints / boundaries, which is absolutely necessary in order to know the full universe of possibilities of each input. 

**Form the domain**: Once the types are known, specify the total input domain.

**Sculpt the partitions**: This is the magic 

# How to best select test inputs?
Do the inputs communicate the desired behavior clearly?

Do the inputs catch bugs before a change is made? (prevention)

Do the inputs catch bugs after a change is made? (regression)

Do the inputs prevent change? (deeply nested data)

# What is a program?
A program maps input data to output data via a control flow graph (created by Frances Allen), producing side effects along the way.
An interactive program simply does this, but incrementally, producing output data and side effects at each iteration

Example program:

Prints the letter "A" if the input is 5 or 17, otherwise it print the letter "B." 

This program maps a number x to the letters "A" or "B".  

```
if (x == 5 || x == 17) {
  print("A")
} else {
  print("B")
}
```

x inputs, and the control flow graph is this:



O [Root]
|-- O [A] (x == 5 || x == 17) "A"
|-- O [B] (else) "B"

It has exactly 2 paths: Root --> A, Root --> B.

As an example, here are the invocations of this program:

```
program(x = 5)    // "A"
program(x = 4)   //  "B" 
```

Rollercoaster - start the input data at the beginning, rides down the path according to its value

There are two paths, and we traversed both of them with these two test cases. But, this implementation would be equally valid for these test cases:

```
if (x == 5) {
  print("A")
} else {
  print("B")
}
```

Run the test cases through that program

This does not meet the desired behavior. When we write a program, we are constructing a control flow graph along with the inputs that it can process. We have to focus on both the inputs and the paths in order for the program to work as expected.

The program should print "A" when x is 5 or 17, so for the tests to be really effective, they have to test for both of these. Even though there are only two paths in the program, we require 3 tests.

Test cases don't inherently prove that a program does what's expected. They simply show that the data for the case does what's expected *for that case.* There is always the chance that input data that you haven't accounted for in the test cases does not map to the correct output or actions. Back to the mail metaphor, you went 5 years without a mistake. But you haven't done your job perfectly just because you delivered one piece of mail correctly. Your job is to deliver *all* pieces of mail correctly, every time they come in. The mail is like input data to a program, and your route is like the call graph. 

# The Futility of Exhaustive Testing
Our example program is a function from an integer to two letters. The domain is the set of all integers, which is infinite, but on a computer is practically 2^64 (19 zeros). The range of letters the program outputs is { "A", "B" } - only 2 possible outputs. To exhaustively test this, we would need to have more test cases than there are grains of sand on planet earth (7.5x10^18)

Combinatorial explosion, product rule

f(x, y) 

2^64 * 2^64 = 2^128

Futile endeavor in aggregate, sometimes possible though. Know how to count combinations	

# Composite Types: Product and Sum Types
First order predicate logic, conjunction + disjunction

https://en.wikipedia.org/wiki/Logical_connective#/media/File:Logical_connectives_Hasse_diagram.svg

Sum type:

type spaceType = 
  | Retail
  | Industrial
  | Office

So called because they represent the sum of the individual cases, OR, i.e. there are only 3 possible space types

Enum values almost always are treated all alike or all different. 

Tip: When testing enum values, use different cases for each of because they are inherently distinct.

Product type:

type occupancyStatus = 
  | Occupied
  | Vacant

type space = {
  occupancyStatus: occupancyStatus
  spaceType: spaceType
}

So called because they represent the product of all of the constituent types, i.e n(occupancyStatus) * n(spaceType)
= 2 * 3 = 6

# Tests for Everyday Features

### Permissioning
**Logic**: A user has access to view a deal if:
- they have access to all of the deal's properties
- and their asset role on all properties has the can_view_deals_renewal = true, or the deal's dealType is "New".

**Types**:

type assetRole = {
  canViewDealsRenewal: bool
}

type user = { }

type property = {
	assetRoles: map(user -> assetRole)
}

type dealType =
	| New
	| Renewal
	| Extension
	| expansion 
	| Termination
	| Modification
	... (14 total) ...

type deal = {
	dealType: dealType,
	properties: list(property)
}

user -> assetRole
deal -> dealType

n(deal) = n(dealType) * n(list(property)) = infinite
n(user) = irrelevant 

 ==> Pundit.policy!(user, deal).show?
 
 let canShowDeal(user, deal) => bool
 
 First, we consider the data graph that this model provides. A deal can have multiple properties, and each of those properties has a set of users with specific access levels to them. In the "user has access" dimension, a user can either:
 - have access to all deals on a property
 - not have access to all deals on a property

Dimensions / Equivalence classes:
property count: 1, many
properties that user has access to: all, some, none
deal type: each kind (14)
properties with can_view_deals_renewal: all, some, none

Total:
2 * 3 * 14 * 3 = 252 cases
only 5 deal types:

Optimize by separately testing deal type mapping:
2 * 3 * 1 * 3 = 18 cases + 13 testing each deal type = 31

Examples:

1 property, access to all properties, new deal, can_view_deal_renewal on all properties
many properties, access to some, deal type new, can_view_deal_renewal on some

Simplified logic:

```
def show?(user, deal)
  has_access_to_all_assets = deal.properties.all? do |property|
    property.asset_role(user).present?
  end

  new_deal = deal.deal_type.name == 'new'

  can_view_deals_renewal_on_all_properties = deal.properties.all? do |asset|
    asset.asset_role(user).can_view_deals_renewal
  end

  has_access_to_all_assets && (new_deal || can_view_deals_renewal_on_all_properties)
end

```

 ### Application / Networking Logic

90% of our apps are forms, tables, and profiles. aka commands and queries, and there's a frontend and backend component, i.e.

|           | Form | Table | Profile |
|------|------|-------|---------|
| Frontend | | | |
| Backend | | | |

6 different categories of behavior, modeled as pure functions. These really aren't algorithmic - they are largely about data transfer and display. 

Let's look at the signature for the frontend side of a table:

### Frontend table:

The primary behaviors are making a network request to retrive spaces from the server, and displaying formatted results. Frontends are largely about *application logic* - interacting with system components to allow the creation, fetching, and displaying of user-input data. So in a frontend, your domain is the application itself, i.e. the interaction between the client and server.

With that in mind, here are some foundational types that the table component will use:

```
// Application - distinct from View
// --------------------------------

type spaceTableFilter =
  | All
  | Available
  | Unavailable
  
n(spaceFilter) => 3

type httpRequestError = 
  | Timeout
  | Timeout
  | Message(string)

type result('t, 'e) = 
  | Success(t)
  | Error(e)

// View
// -----

// View Model
type spacesTable = {
  error: error,
  displaySpaces: [displaySpace]
}


type displaySpace = {
 suite: string,
 askingRent: string
}

type space = {
  suite: string,
  type: spaceType,
  minimumRent: option(float),
  maximumRent: option(float)
}

n(space) = infinity !!
```

We create a function which requests spaces from the server according to the specified filter. The key here is that a network request can always fail, so we return a result that can either contain space data, or be an error:

```
let spacesRequest = (filter: spaceTableFilter) => result([space], error)
```

We create another function that makes the request and exposes formatted data to the View. In React-land, this normally happens inside of the component, or a ViewController on iOS, or a Fragment on Android. But I've extracted it so that we can analyze it independently and outside of a UI context. Though it's not a common pattern here, It's also a good idea to do this in a real application:

```
type spaceRequestFunc = (spaceTableFilter) => result([space], error)
let spaceTableUseCase = (filter: spacesFilter, request: spaceRequestFunc) => spacesTable
```

This function takes in a filter, and a request which uses that filter to fetch data, and returns a table value which has a list of spaces or an error that the view can render directly. Let's pull the actual making of the request outside of the use case function, and pass the result in: 

```
let result: result([space], error) = spacesRequest(All);
let spaceTableUseCase = (result) => spacesTable
```

Let's pause here. This signature says that, the display spaces that we give to the view are dependent on the result of the request to the server. This intuitively matches what we said the function should do. If the result is successful, we get the returned spaces. If the result is not successful, we get a descriptive error. This is an important point - the API response is the main input to this behavior, and the filter is just a means at retrieving this data. 

Back to the full signature:

```
let spacesTable = (filter: spaceFilter, request: (spaceFilter) => [space]) => spacesTable
```

In order to test this, we need to pass in a fake request function:

```
let testSuccessfullyFetchingSpaces = () => {
  let sucessfulRequest = (filter) => [{
    suite: "100A",
	type: Office,
	minimumRent: 1000,
	maxiumumRent: 5000
  }];
  
  let table = spacesTable(Office, successfulRequest);
  
  expect(table.spaces[0].type).to.eq("1000 - 5000 sf");
};
```

The purpose of this test is to ensure that we apply the proper formatting of the asking rent. In the case of a data table, the table view model is the interface that the view uses to display data. If we test that that is correct, the only remaining code that runs is markup and a few simple if checks. This is the "Humble Object" pattern - where you separate the difficult / complex logic into a helper class / function, and the code that interfaces with an external library (i.e. React) is very simple and needs a small integration test, or even no test at all.

Now, how do we become confident in knowing that this table works as expected for all inputs? As we showed, this table is dependent on a request from the server, which makes it virtually dependent on the spaces that come back in that request. How many possible `space` values can there be, and what are the interesting partitions of spaces?

Well, n(space) = n(string) * n(spaceType) * n(option(float) * n(option(float)). This is surely an effectively infinite number - infinte strings, infinite floats. Exhaustive testing is out the window.

But, there's nothing interesting about the suite strings, we just display them directly. This is very common in information applications that just store and retrieve data. The interesting values are minimum and maximum rent though, which are optional which means that they can possibly be null. How should this range string be constructed in the presence of null values? 

Let's think about the possible states that this can be in. A sub-function of our full use case can be though of as:

```
let rangeString = (minimumRent: option(float), maximumRent: option(float)) => string
```

The range takes in two optional float values, minimumRent and maximumRent. The *Rule of Product* tells us that there are 4 cases of data here since each value varies independently and each value has 2 possible states (null or a valid float). This can be shown in a truth table:

00
01
10
11


| minimumRent | maximumRent |
|----------------|----------------|
|       null     |     null       |
|      null      |   5000.0      |
|     1000.75 | null |
|  100.75 | 5000.0 |

There are 4 interesting cases here: both null, only max rent present, only min rent present, or both present. We should test each one. This also shows how nullability increases the complexity of code - it effectively adds a branch everywhere there's an optional value, and branches increase exponentially - 2 null values = 4 branches, not 2. Nulls make code exponentially more complex.

#### Key Takeaways:
* We need to decide which variables *interact*. Since the range is composed of two values, they influence each other. The range is determined by all of the combinations of both of these values. 
* The input for frontend features is largely the API response. We should focus testing efforts there by stubbing and mocking responses.

---------------------------------------

Note - the view is a function that accepts this function:

function View(query, spacesTable) => html

### Backend Table:

On the backend of a table, the server needs to retrieve spaces from the database according to the filter provided in the request:

```
type spaceRepositoryFunc = (spaceTableFilter) => result([space], databaseError)
let spaceRepository = (filter: spaceTableFilter) => result([space], databaseError)
let spacesIndex = (filter: spaceTableFilter, repository: spaceRepositoryFunc) => result([space], error)
```

The first thing that jumps out is that the space repositroy function  has the exact same signature as the client request:

```
let spaceRepository = (filter: spaceTableFilter) => result([space], databaseError)
let spacesRequest =   (filter: spaceTableFilter) => result([space], error)
```

This shows that simple information features are primarily about moving data back and forth between client and server, and the client function simply defers to the response of the server request.

They are tested very differently though. The client's responsibility is to simply handle the `result` that the server returns, but it cares about the values of `spaces`. It also uses the filter param for to construct different query params for the request URL.

The server uses the filter param to modify the SQL query to the database, but other than that it doesn't care at all about the returned `space`s. It simply forwards the results of the query. However, an additional responsibility of the server is authorization.


```
let spacesIndex = (filter: spaceTableFilter, repository: spaceRepositoryFunc) => result([space], databaseError)
```

function spacesIndex(query) => [space]
function spacesIndex(page, pageSize) => [space]

What is missing here? How can we transform a page number into a set of spaces? 

The  spaces exist in the database, so this function always executes in a "data context", which in this case is a list of spaces. Making this explicit:

function spacesTable(page, pageSize, existingSpaces) => [space]

page: int
pageSize: int
existingSpaces: [space]

n(page) = infinity
n(pageSize) = infinity
n(existingSpaces) = infinity

Exhaustive testing: simply not possible

But, by the requirements, the page represents an offset into the list of existing spaces, and the page size represents the number of spaces to return.

Test cases:

# Boundaries
I want to talk about boundaries now. The significant boundares in this example were:

- Client -> Server
- Server -> Database

Each of these boundaries means that the number of test cases effectively doubles. For example, for the client server boundary, we have the tests where we stub out the server, but everywhere we stub out the server we have a corresponding test that verifies that the server returns the correct value for that input.

But, there is hope, which is that the boundaries are separated by a value. To reiterate, the client request function has the following signature:

```
let spacesRequest = (filter: spaceTableFilter) => result([space], error)
```

But in isolated tests, since we don't control the server, we stub this value to simply return the result that we want. There can be infinitely many test cases on the server that result in different `result` values, but we don't care how they're calculated. We only need to know the interesting values that the `result([space], error)` can take on, and stub those.

A boundary like this almost always exists at the process level. By this I mean, a "system" is really a collection of communicating OS processes. The client, server, and database are all separate processes running on different machines, communicating over the internet. 


# Common Sense Partitions
^ Sort of a bad argument

"Domain analysis"

Full input domain = { 1, 2, 3, ... infinity } (real numbers)

Obviously for this program there are only 3 partitions though: { 5 }, { 17 }, { any number except 5 and 17 }

The problem with these is that they require understanding the requirements of what we're building in order to make the partitions. 


# It's all about the inputs
Graphs are O(n^2)

Input combinations are O(2^n)

# In, Out, On, Off
Once partitions are determined, check edge cases


# Advice
* Know how many values a type can represent
	* Product type and sum type rules 
* When a type can represent a huge number of values, try and create a more strict type that represents fewer values
	* This avoids primitive obsession, and also drastically reduces the number of possible input values


# What about the other stuff?
How do you test HTML, CSS, animations, db query performance.

A non-trivial % of bugs comes from "non-functional" system requirements. This is only talking about logical bugs. Logical correctness is our first goal, something can be performant but logically wrong and it is now valueless. Of course, we want perfect harmony of the two, but performance testing has other techniques and characteristics.

# Thoughts
Values as boundaries - when you put a value in between two components, you can stub out the value and not worry how it's computed. 

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