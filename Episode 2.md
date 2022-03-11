# Episode 2

Last episode was the first episode and I gave a high-level view of Haskell.  Specifically I elaborated on the four terms you hear it described as, statically-typed, functional, pure, and lazy.  I also went into detail about the utxo model, compared it to the accounting model and then described the extended utxo model that Cardano uses to facilitate smart contracts.  Additionally, I talked about monoids, what they are, what they’re good for, highlighted the ones Lars uses in the Plutus Pioneers lectures and gave additional examples.  Lastly I described my development environment and why the combination of Qubes OS and NixOS is compelling.

For this episode I want to start with the overloaded term, Plutus, distinguish the different components.  I want to discuss on-chain code including datums, redeemers, script context and validators and then describe the process of setting up a code editor for Plutus development.  Finally, I want to end the episode by explaining Functors, the most familiar in the Functor, Applicative, and Monad hierarchy.

Plutus is overloaded and refers to a whole bunch of different components so I want to start out by distinguishing each.

I’ll start at the top, the Plutus Platform includes everything.  It supports application development, authoring, testing, and runtime support.  The Plutus Platform is composed of the Plutus Foundation and the Plutus Application Backend or PAB.  For today’s episode I will elaborate on the Plutus Foundation and not on the PAB.

The first component of the Plutus Foundation is Plutus Tx.  Plutus Tx is a simplified subset of Haskell that’s compiled to Plutus Core with template Haskell.

With Plutus Tx I want to start at the beginning specifically the custom Prelude module.  I’ll describe what the Haskell Prelude is, discuss problems with Haskell’s Prelude.  And finally I’ll discuss more abstract concepts regarding programming languages including partial and total functions.

The Prelude module can be found in the base package.  Prelude is the foundation of the Haskell language containing the basic types including Boolean, Numeric types, Char and String, Maybe, Either, the list type and list operation as well as IO.

The Prelude module is updated conservatively, but since Haskell’s inception in 1990 there have been improvements in programming that haven’t been incorporated yet into the Prelude.  The most important advancement has been the identification of partial and total functions.

So what are partial and total functions?  Partial functions are functions that lie to themselves and to you.  The most famous example is found in Haskell’s Prelude module, the head function that works with lists.  head’s function signature accepts a list and returns the first element of that list.  head behaves as expected except when it’s passed the empty list.  When it’s passed the empty list it throws an exception.

Let me try to describe it pictorially audibly.  Descriptions of functions often involve the domain set represented as an oval on the left, a co-domain set, represented as an oval on the right and a mapping, represented by an arrow from the domain to the co-domain.

Imagine your input set being three elements, the empty list, a list with the True Boolean value and a list with the False Boolean value.  What’s the mapping that head produces?  The list with the True value maps to True while the list with the False value makes to False.  But what does the empty list map to?  There is no corresponding value in the output.

This description can also be applied to better understand pure functions vs impure ones.  Imagine the domain is just the numbers one and two and the co-domain is True and False.  An impure function would have the one map to both True and to False.

Notice how a partial function is subtly different than an impure function.  Impure and Partial functions are a pernicious source of bugs and in addition to being pure, the Plutus Prelude excludes partial functions.  

The Plutus Prelude also includes the inlinable attribute to every function.  This is due to a Template Haskell constraint.  Normally all Template Haskell code needs to be written inline to be compiled however that would be cumbersome with real Plutus validation scripts.  With the inlinable attribute validation logic can be separated and delegated to helper functions.

The second component of Plutus Foundation is Plutus Core.  The Plutus read the docs state that Plutus Core is a variant of lambda calculus and is designed for simplicity, determinism, and to allow careful cost control of program execution.  Using lambda calculus makes it an easy compilation target for functional programming languages, and allows us to have a simple, formally verified evaluator.  I’m reading the documentation almost verbatim because it continues.  Plutus core is our assembly language.  Trust me, you don’t want to see any! Dealing with that is the compiler’s job.

The Plutus Core documentation is somewhat sparse because as with most other assembly languages reading and understanding Plutus Core can be enlightening but is not required for application development.  However, my intent with this podcast is to dive a little deeper into topics for my own education and for others, so I decided to take look into the the Plutus Core language specification.

From reading over the specification I was able to learn more about the design motivation and connect it’s design to cloud based serverless services, something I’m more familiar with.

On-chain code is executed by core nodes that process transactions.  Network security could be threatened by a denial of service attack where a malicious user submits code that consumes excessive amounts of processor time or memory.  This threat is overcome by transaction fees levied whenever a node processes on-chain code.  The fee is payed in advance and if at any point during the execution of the transaction the amount of resources consumed exceeds the amount of resources paid for then the transaction is immediately terminated and the fee is forfeited.

On-chain code is similar to cloud-based serverless functions where computation cost is also measured in processor time and memory usage.  The cloud providers solution to errant processes is to include a timeout usually about 5 minutes or so.  The timeout seems to be an alternative albeit courser solution to too long-running or too memory intensive computations.

Another detail I gleaned from reading the Plutus Core specification is that Plutus Core is a typed language however, before deployment to the network the typed Plutus Core language is serialized into an untyped variant to save space.

OK so you have the Plutus Platform that encompasses everything composing Plutus Foundation and the Plutus Application Backend.  Plutus Foundation is composed of Plutus Core and Plutus Tx.  Plutus Tx is a subset of Haskell that features it’s own updated Prelude module.  Plutus Tx is compiled to Plutus Core via Template Haskell.  Plutus Core is a compilation target, an assembly language based on lambda calculus that has a typed and untyped syntax but being an assembly language isn’t required to know in depth.

On-chain consists of a validation script that determines whether or not a spending transaction is allowed to spend any of the utxos at the given script address.  That determination process is governed by three parameters of the validator including the datum, redeemer, and script context. 

The datum is a piece of data specified by the party who created the output that sits at the script address.  In the Plutus Pioneer lectures, in the vesting example, the datum is a Haskell record type with two fields the beneficiary represented by a public key hash and a deadline represented by a POSIX time.  That makes sense for a vesting contract where funds can only be unlocked by a specific person after a designated amount of time.  Datum values are also used to carry state with contracts that involve multiple transactions.

The redeemer is a piece of data specified by the party spending the output.  The simple of examples of redeemers are usually the public key hash of the spender or the current POSIX time.  However, both of these can be discovered through the validation context.  More realistic examples use the redeemer as an endpoint similar to an HTTP endpoint.  The redeemer is represented as an abstract data type, basically as a finite set of choices.  When the contract is executed the validation code associated with one of the redeemer values is executed.

The validation context is the representation of the spending transaction.  It includes the public key hash of the user submitting the transaction, the time slot the transaction is sent in as well as the input utxos and output utxos.

An interesting perspective is to compare the scope of validation scripts of Cardano scripts to that of Bitcoin and Ethereum scripts.  Bitcoin scripts or just Bitcoin transactions have limited scope being restricted that that of only the redeemer, the redeemer being the digital signature of the party wishing to spend the utxo.  

On the other hand Ethereum validation script scope is global having access to the entire blockchain, redeemer, spending transaction and every other transaction ever.  

Cardano validation script scope threads that needle being that of the spending transaction.  Not as restrictive as Bitcoin but yet not as global as Ethereum.

The validation’s scripts arguments, the datum, redeemer, and script context have the same underlying type called Data.  It is a generic JSON like type designed to be encoded easily.

Validators can be parameterized allowing for runtime arguments to be passed in.  The implementation of them uses something called an Applicative Functor.  Later in this episode I’ll be describing what a regular Functor is and next week I’ll continue with Applicative Functors.

Setting up a coding environment for Plutus can be a little tricky.  In what follows I will describe the tools I used to get setup using NixOS and vscodium.

On NixOS, nix is installed by default, no surprise there.  Vscodium with the extensions Haskell and Haskell Syntax Highlighting, GHC, and cabal-install can be downloaded and installed from nixpkgs.  Additionally install Niv, a command line tool for keeping tack of Nix project dependencies.  This will get the initial user environment set up.

A Plutus project can be started in the standard Haskell way by running cabal init and additionally adding a cabal.project file to the root.  Plutus code is not on Hackage and so has to be referenced first in the cabal.project file before it can be referenced as a dependency in your project’s cabal file.

To incorporate Nix into the project use IOG’s new tool haskell.nix.  haskell.nix is an upgraded cabal2nix and allows Nix to manage the dependencies of a Haskell project as well as build it.

The vscodium Haskell extensions use the Haskell Language Server  launching a background service for each Cabal project that answers questions to support common IDE features such as code navigation, completion, documentation, and refactoring.

However when using Nix to build and manage a project, the vscodium Haskell extensions don’t work out of the box.  To remedy this, there is another project called haskell-hls-nix.  Haskell-hls-nix includes a wrapper that invokes the Haskell Language Server from within a Nix shell.

Nix has a large surface as does apparently the interface between Haskell and Nix.  On the next episode I will be talking about Nix and hopefully in a future episode I will be knowledgeable enough to comment on Haskell Nix integration.  For now though I’m hoping that’s a good enough introduction to setting up NixOS with vscodium and Haskell extensions as well as migrating to Nix to build and manage the project and updating the extensions to work with Nix.

I want to end this episode by describing Functor

So what is a Functor? A Functor is a structure that takes one type parameter, that has a specific function called fmap and fmap adheres to a couple of rules about how it should behave.

There are many datatypes that qualify as Functors.  The List type, the Maybe type as well as IO.  All of these types have one type parameter and all have an associated fmap operation.

The most well know example of a Functor is a list where the structure is a list and the fmap function is map.  Map behaves such that for each element of a list apply the given function to the element and return a new list consisting of the new elements.

All Functors have a fmap function however, the implementation of that fmap function varies.  For example, The Maybe type is a Functor it’s fmap implementation consists of applying the given function to the value in the Just if it is a Just otherwise if the value is a Nothing then simply re returning the Nothing value.

Describing examples of Functors is helpful but it’s also helpful to list counter examples that seem like they could be Functors but fail to do so for one reason or another.  

The first counter example is a dictionary type, or list of key value pairs.  This type fails to be a Functor because it has two type parameters, a type parameter for the key and a type parameter for the value.  For the more pedantic, Yes a dictionary can be converted into a Functor if you specify concretely one of the type parameters and all though a Dictionary fails to be a Functor out of the box, it can be made an instance of a Bifunctor, but I’ll save those details for a later episode.

When I was initially writing the transcript for this episode I accidentally included the String type as an example of a Functor but String type fails to be a Functor and offers to be an enlightening counter intuitive counterexample.

In Haskell the String type is defined as a list of characters that’s what a String is
So for example if you mapped the isUppercase function over a String you get in return a List of Booleans and so a List of Characters has turned into a List of Booleans and a list of Booleans is no longer a String

From the definition of String, being a list of Characters, there’s already an indication that it might not be a Functor because the type is fully specified, there’s no type parameters while a Functor should have exactly one.

However, mapping over Strings comes up often in programming so what gives?  Functor is just to general to describe a String instead String is a monomorphic Functor.  It’s fmap signature would be (a→a) function followed by a (f a) returning an (f a).

Each typeclass Functor, Applicative, and Monad have rules that each of their respective functions must follow.  For Applicative and Monad the laws aren’t all that intuitive but for Functor they are simple enough that they can be explained audibly.

The first law that the fmap function must follow is that fmapping the identity function over the Functor is equivalent to applying the identity function to the Functor itself.  That is to say that fmap id Functor is equivalent to id Functor.

The second law is that given two composible functions a mapping of the first function followed by a mapping of the second function over the Functor is equivalent to the composition of those two functions and one mapping.  

For example, suppose you have two composible functions, length and isEven, length has the signature String → Integer and isEven has the signature Integer → Boolean.  Additionally you have a Maybe String with PlutusCast as the value inside it’s Just constructor.  For the maybe type to conform to the second Functor law mapping length followed by mapping isEven would have to be equivalent to first composing the length and isEven functions together and then mapping that resultant function over the Maybe.

In the Plutus Pioneer lectures Lars uses fmap on the IO Functor instance.  IO is most similar to a Promise in other languages.  The instance implementation for the fmap function of IO will wait until the IO resolves and then map the given function over the result.

Hopefully that’s a crash course on Functors.  You can find a more depth discussion on Functors in the respective Lambda cast episode

In this episode I distinguished the different Plutus components.  I discussed on-chain code including and described the process of setting up a development environment for Plutus.  Finally, I ended the episode by explaining Functors.

For the next episode I want to take my first pass at explaining Nix.  Additionally, I want to describe the Applicative Functor.

If you enjoyed the episode and think it would be valuable to others please consider retweeting me and sharing the cast.
