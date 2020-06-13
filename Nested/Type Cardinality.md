The cardinality of a type is how many discrete values the type can represent. The type can be viewed as a set, and each discrete value is a member of that set. 

For example, the cardinality n(Int) is infinite conceptually, though on a real machine it is the word size, most likely 2^64. What about a composite type, such as:


type rank = | Ace | Two | Three | Four
  | Five | Six | Seven | Eight | Nine | Ten
  | Jack | Queen | King
  
 type suit =
   | Hearts
   | Diamonds
   | Clubs
   | Spades
   
type card = {
  rank: rank,
  suit: suit
};

n(rank) is 13, and n(suit) is 4. n(card) is 13 * 4 = 52, which is the number of cards that are in a deck of playing cards. So this type can exactly represent the set of all playing cards.