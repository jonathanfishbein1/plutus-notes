I wanted to fix some errors I’ve made in these posts and describe pitfalls I’ve experienced that offer an additional perspective on some of the topics I’ve discussed previously.  

In Episode 2 I suggested that the Plutus Prelude is composed of only total functions.  That is incorrect, the Plutus Prelude contains partial functions similar to the original Haskell Prelude as evidenced by inclusion of the head function in the Plutus Prelude.

For the purposes of learning Plutus the takeaway is that other Haskell Preludes exist to cater to the specific domain they are being written for.  Relude is retrofitted to exclude partial functions in favor of total ones, the Plutus Prelude is retrofitted with functions having the inlinable property applied to them to facilitate Template Haskell compilation.

In Episode 1 I described the big-picture attributes of Haskell, namely that being a statically-typed, functional, pure, and lazy language.  I described each attribute and it’s advantages.  The lazy attribute was the last one I described because it was the one I was least familiar with.  

I used an infinite list because I did not know the length of the list at compile time.  That code contributed to a major bug I encountered.

`indexSequence :: (RandomGen g, Foldable t) => g -> Int -> t a -> [Int] 
indexSequence g numberOfIndexes listOfItemsToGenerateRandomIndexesFrom = 
	(take numberOfIndexes . nub) $ randomRs (0, length listOfItemsToGenerateRandomIndexesFrom - 1) g`

The code above is supposed to generate a list of random indexes in a list.  It’s parameters are a random generator, the number of indexes to return and the list of items to generate random indexes from.   
This code contributed to a bug but wasn’t the only problem.  The inputs to this function were wrong such that the numberOfIndexes argument was greater than the listOfItemsToGenerateRandomIndexesFrom argument length.  The reason for that bug was my mistake that I don’t want to elaborate on because it’s a little embarrassing.  That being said following the logic of what happens in this function is enlightening when in this situation.  
For demonstration purposes lets say that numberOfIndexes is equal to 10 and the length of listOfItemsToGenerateRandomIndexesFrom is 5.  the randomRs function will generate an infinite list of random number ranging from 0 to 4.  the nub function will filter out duplicates and the take function will take 10 values from that generated list.  But a deduped list composed of the numbers 0 through 4 will never have 10 values to draw from.  It’s an infinite loop

Haskell removes common sources of infinite loops in code such as `while(true)` or `for (var i = 0; i > 0; i++)` because neither while loops nor for loops are Haskell primitives.  However, Haskell also seems to introduce a new source of them.

The next pitfall I’d like to describe is regarding by misuse of Monads.  The signature for Monad’s bind function is `m a → (a → m b) → m b`.  As you can see the bind function and the function passed into the bind function have the same result type, `m b`.  I found myself attaching the next bind call to the `m b` returned within the function passed into the bind.  Doing this results in indented code as if you hadn’t used Monads and instead pattern matched on the result.

Consider the example used in the last post where two functions return Maybes and the second function sequentially depends on the first.  The first function gets the name of a person from a dictionary with a specified ID and if that’s successful then another function gets the date of birth of that person from a different dictionary specified by their name.  Additionally, for demonstration purposes the original ID is wrapped in a Maybe type.

The function signatures are as follows

`getName :: ID → Maybe String`

`getDOB :: String → Maybe DOB`

The non-idiomatic code is below

    MaybeID
      bind (id → getName id  
	           bind (name → getDOB name)  
	    )

As you can see I’ve attached the subsequent bind after getName within the passed in function.  To use monads idiomatically the above code should really be

    MaybeID
    bind (id → getName id)
    bind (name → getDOB name)

Originally I did not like Haskell’s do notation because I thought it obscured the Monad’s bind mechanism.  I have changed by opinion on that because I experienced this issue.  So for example the corresponding do notation syntax would be the following.

    Id ← MaybeID
    name ← getName Id
    DOB ← getDOB name


In Episode 3 I described how I was using the Either String Bool as a return type for the Plutus validation conditional functions instead of just Bool as a means to communicate why an error occurred.  

The following is a little hard to follow and for good reason, the approach is more mechanical than it needs to be, there is a higher-level approach I’ll elaborate on below, and there is unnecessary redundancy!  

I had an isValid function that accepted as a parameters the potential Bools returned from each validation condition functions and only returned True if all Boolean arguments passed in were True.  In the main validation function I lifted the isValid function into the context of the Either String Applicative Functor and then applied the isValid function wrapped in the Applicative Functor context to each of the validation conditional functions returned Either String Bool values using apply.

I replaced the lower level mechanics of using pure and apply with the sequence function.  The signature is `sequence :: (Applicative f) => [f a ] → f [a]`.  That is, given f is an Applicative Functor, sequence takes a list of Applicative Functors with the given type and returns the same Applicative Functor with a list of the given type.  I encourage you to read about the sequence function from the book Learn you Haskell for Great Good where there is an in depth discussion on it.

For this particular example the validation conditional functions returning `Either String Bool` are taken together, a list of Either String Bool and the resulting value after sequence is applied to the list is an Either String [Bool].  A Right constructor is returned if all of the individual validation conditionals were also the Right constructor and the Left constructor if any were not.

Additionally, I realized that the Bool result type of the `Either String Bool` was redundant because the Either type represents an error by returning a Left constructor.  I converted the return type of the validation conditional functions to `Either String ()`.
