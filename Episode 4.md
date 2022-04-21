In the last post I described the Applicative Functor, gave a brief description of deploying on-chain Plutus code and explained NixOS and the nix package manager

In the this post I want to start a series on Monads where I build from the ground up the foundation for describing the Plutus specific Monads.  The Plutus Monads are Monads but they are combo Monads, Swiss army knifes of Monads that can obscure the fundamentals.

A Monad is a structure that has two functions associated with it, the first being return and the second being bind.

Return is exactly the same as Applicative Functor’s pure.  It takes a value and puts it inside the Monad.  It’s signature is (a) → (m a)

Bind is more difficult to understand, it’s signature is (m a) → (a → m b) → (m b).  Bind takes a Monad as the first argument and a function that takes the first Monad’s internal value as an argument and returns a new Monad.

The definition is useful but can be difficult when trying to get an intuitive feel for Monads.

Monads are described as for use when writing imperative code in Haskell.  However, I find that description vague because the meaning of imperative code is ambiguous.  Is imperative code sequential code, statement oriented code, code with mutability and side effects, or all of the above?  My understanding is that imperative code encompasses all those attributes.  But Monads, fundamentally are designed to execute code that has sequential dependencies.

The prototypical example of a Monad in imperative languages is a Promise where code only executes when a value is returned.  Promises capture the sequential dependence of Monads because network lag extenuates the concept.  However, there are more types of sequentially dependent code then just Promises and other Monads for capturing the sequential nature of the code.  

For example, Maybe is also a Monad and you can have two computations both returning Maybes and the second computation sequentially depends on the first.  An example might be get the name of the person from a dictionary with a specified ID and if that’s successful then get the date of birth of that person from a different dictionary specified by their name.

The problem that Monads solve can be explained by a problem encountered in JavaScript called callback hell also called the pyramid of doom.  In JavaScript a more fundamental/primative approach to asynchronous code than Promises is to use callbacks, that is functions nested inside other functions to guarantee sequential execution.  This quickly devolves into code that is heavily indented forming a sideways pyramid shape.  The code pattern is the same for each function, execute this code, when complete pass the result on to the next function.  

In Plutus Pioneer lectures Lars is generating the pyramid of doom using Maybes and Eithers when he pattern matches on the first parsed value, if that’s successful then on the second, if that’s successful than on the third.  It’s a lot of code for exactly the same pattern.  Monads capture the pattern resulting in less code.

In comparison, the arguments of the function wrapped in an Applicative Functor are independent of each other.  In the last post I described how in Lecture 4 of the Plutus Pioneer Program Lars could have used an Applicative Functor instance of the Maybe and Either String types to implement the foo’ functions that’s because all three parsing operations are independent of each other.
 
When I was writing this post I realized that I had used Monad’s bind in an implementation when I could have used the Applicative Functor’s apply function instead.  Prior to working with Plutus I was developing a Linear Algebra package for Elm.  One of the main types was a Vector which was a newtype wrapper around a list.  There is a operation on Vectors called the tensor product which is essentially the Cartesian product of the elements of the first vector with those of the second.  My original implementation involved using bind, or andThen as it’s called in Elm, in conjunction with vector scalar multiplication as the function that returns a Monad.  Conceptually you can think of the steps as, iterate over the first vector, calling vector scalar multiplication for each element of vector one where vector’s one element is the scalar and vector two is the vector being multiplied.  Each iteration through vector one returns a new Vector and these vectors are concatenated together 

An alternative is to implement the tensor product using apply, andMap in Elm.  Conceptually the steps are map multiplication over the first vector resulting in a vector with partially applied multiplication functions as elements and apply this vector to the second vector.

Both implementations work there are competing aspects as to which one is higher-level.  In the first implementation it’s using bind which is lower-level than apply however, it’s also using vector scalar multiplication as the internal operation which is more descriptive of the desired operation.  In the second implementation the operation is just multiplication, less descriptive and lower level than vector scalar multiplication however, apply is used which is higher-level than bind.    

Initially when I was learning about Object Oriented programming and specifically about inheritance I got stuck on which class was the sub class and which class was the super class.  From a functionality perspective a sub class is a super set of the functionality of a super class while a super class is a subset of the functionality of sub class.  That was the wrong intuition.  A sub class is subset of the super class in the sense that every sub class is an instance of the super class.  For example if the sub class is a Cat then the super class would be Animal.

An analogous conversation can be had about the relationships between Functor, Applicative Functor and Monad.   Monads are a subset of Applicative Functors and Applicative Functors are a subset of Functors.  There is a set of types that can be Functors but not Applicative Functors and there is a set that can be Applicative Functors but not Monads.  Functors are a superclass of Applicative Functors and Applicative Functors are a super class of Monads

From a functionality perspective Monads are a super set of the functionality of Applicative Functors and Applicative Functors are a super set of the functionality of Functors.  

Functor’s map can be implemented in terms of Applicative Functor’s pure and apply.  To see how pure f <*> x equals fmap f x.  Applicative Functor’s apply can be implemented in terms of Monads return and bind f (a → b) <*> (f a) equals f (a → b) >>= (\(a → b) → (f a) >>= (return ((a → b) a)).

This was the first post about Monads.  It was general but I hope to get to more Plutus specific ones later on.
