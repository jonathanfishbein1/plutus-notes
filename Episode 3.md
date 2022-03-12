# Episode 3

I’ve decided to go with a written form, although a podcast is convenient at times for listeners, I feel the written form better suits the content.
In the last episode I distinguished the different Plutus components. I discussed on-chain code, described the process of setting up a development environment for Plutus and ended the episode by explaining Functors.  Additionally, I had an intermediate episode, Episode 2.5 where I discussed what I felt were different versions of hard.  Haskell is hard, it’s unfamiliar and abstract but imperative programming and in particular JavaScript is also hard.  The myriad of different language primitives, design patterns, libraries, and frameworks is overwhelming.  Two different types of hard
In this post I want to describe the steps for deploying the on-chain portion of a contract.  I want elaborate on NixOS and the Nix Package Manager, adjacent technologies, but that are useful and fully embraced by IOG.  Finally I want to ascend the typeclass hierarchy and describe what an Applicative Functor is and it’s relationship to on-chain code.
I think one of the most tricky concepts for me to understand was how to deploy a Plutus smart contract.  Plutus smart contracts aren’t really deployed to the blockchain.  Instead, The Plutus Tx script is compiled to Plutus Core, hashed and then that hash become the address that funds dedicated to the script will be stored and accessed from.  Transactions that wish to spend the output stored at the script address include the script and from there the script is executed by nodes.

Currently the most actionable way to “deploy” a Plutus contract is using the cardano-cli.  Unfortunately, a translation is necessary from Plutus types to cardano-cli types.  The Haskell Cardano.Api module is the source code of the cardano-cli and can be used to convert a Plutus script, it’s datum, and it’s redeemer to analogous cardano-cli types.  From there the execution of the right cardano-cli commands with the right arguments in the right order should get you a “deployed” script .  It’s laborious, mind-numbing and frustrating but doable.

Nix is a powerful tool however, there is a steep learning curve with it, similar to Haskell.  I wanted to describe it top down  starting with NixOS, moving onto the nix package manager, and finally the nix expression language.  NixOS is enabled by the nix package manager which is enabled by the nix expression language but I I think understanding the value proposition of NixOS is most accessible means to start learning about Nix.

From a ten-thousand foot view NixOS is an operating system where the entirety of your system is specified in a configuration file.  All packages installed, services started and stopped are controlled via the configuration file.  The configuration.nix file is like an incantation that springs forth an operating system.

I’ve used NixOS as a development machine for a few months and I’ve recently started using it as a server where it competes with other tools including Docker.  Containerization has been popular over the last few years because of the portability and reproducabilty of containers.  However, they are only as portable and as reproducible as the developer creating them.  It’s still very easy to remote into a docker container and imperatively edit configuration files and install applications.  NixOS is very hostile towards mutability disallowing the installation of software via more conventional means by having a read-only file system.

NixOS is facilitated by the Nix Package Manager.  To better understand the Nix Package Manager I think it helps to understand the problem.  Suppose you have a project with ten dependencies and each dependency has four versions.  The number of unique combinations of packages and versions is 4^10, about one million combinations.

Prior package managers solved this problem differently.  The node package manager installs every dependency of a project and their dependencies in separate folders.  This causes an exponential expansion of storage because there is no sharing of project or package dependencies.

Earlier versions of Haskell’s cabal arguably had a worse solution, notoriously called cabal hell.  Cabal would keep project dependencies in a global mutable store.  Each package was just referenced by name and if you wanted to update a package to the newer version you risked breaking other projects that relied on the older one.  

This was worse than it sounds because packages in the global mutable store would have dependencies also in the global mutable store.  If you updated a package that was dependended on by other packages you would risk breaking those too.  The advantage of the cabal approach was that there was a lot of dependency sharing among projects and packages

The Nix Package Manager takes the strengths of each approach, having the isolation of npm while also sharing capabilities of cabal

The best way I’ve found to think about the Nix Package Manager is as a graph database visualization.  There exist four types of nodes, packages, user environments, profiles, and users.

Packages are a node that dependent relationships on other packages.  For example, openssl is a dependency of git.  A user environment is a node that has a contains relationship with packages.  For example a user environment can contain git.   A profile is a node that corresponds to a user environment at a certain time also called a generation.  A user is a node that contains many profiles.

From the top a user is a node containing many profiles.  Each profile corresponds to a user environment .  Each user environment contains packages and each package depends on other packages.

Hopefully, that’s a good introduction to NixOS and the nix-package manager.  There’s still a lot to say about Nix in general including the nix expression language, nixpkgs, and NixOps but that can be for another post.

Applicative Functors are definitely a little more challenging to wrap your head around than a regular Functor.  In fact Applicative Functors were introduced well after both Functor and Monad were a part of Haskell.  Similar to how Semigroup was added after Monoid.

Mainstream languages have lists and arrays with map functions similar to Functors and in addition they have Promises which is one concrete implementation of an abstract Monad.  However, mainstream languages don’t have a common, easily identifiable analog to the Applicative Functor.

An Applicative Functor is a structure that has two functions associated with it, the first being pure and the second one being apply.  

Pure is the easiest to understand.  Pure takes a value and puts it inside the Applicative Functor.  It’s signature is (a) → (f a).  For example, Maybe is an Applicative Functor and it’s respective pure function would be the Just constructor.  List’s pure function would be inserting the element into an empty list.  For the Either type the implementation of pure would be the Right constructor. 

Apply is more difficult to understand and so to better explain it I wanted to briefly described curried functions and partial application. Both these terms are used synonymously but it helps to understand the difference.  
Haskell is a curried language meaning that functions that you would normally think of as taking several parameters actually only take one parameter and return a function that takes the next parameter and so on.  So while in a language such as C# if you had a function that took two parameters and called it with only one you would get a compiler error.  In comparison, in Haskell calling a two parameter function with one argument would result in a function that can accept the final parameter.  The act of applying a multi-parameter function to an argument in Haskell is called partial application.  Curried is an adjective, and describes that languages ability to partially apply functions to arguments.

Understanding curried functions and partial application is critical for understanding the Applicative Functor’s apply function.  The signature for apply is f (a → b)  → (f a) → (f b).  That is, it takes a function wrapped inside an Applicative Function, an Applicative Functor and returns a new Applicative Functor with the function applied to the value.

Suppose you have a divide function and in order to prevent division by zero you return a Maybe, with the quotient inside a Just if the divisor is not zero and Nothing if it is.  Suppose you want to add two resultant quotients.  Both quotients are wrapped in Maybes.  You could pattern match the first one, pattern match the second one and if both are in Just constructors, sum them and if either one is a Nothing return Nothing.  

Alternatively you can wrap the add function inside a Just constructor resulting in a Just (+) this is the f (a → b).   You can then apply the wrapped addition function to the first quotient value resulting in a partially applied plus function wrapped inside a Maybe.  Finally, you can apply the wrapped partially applied addition function to the final quotient resulting in the full application of the addition function and the calculated sum inside a Just.

In Lecture 4 Lars uses the Maybe and Either String Monads to accomplish a similar case.  For his example the operation that may fail is String parsing instead of Integer division.  Functor is a higher level abstraction built on top of an Applicative Functor and similarly Applicative Functor is a higher level abstraction build on top of Monads.  Monads are more powerful than Applicative Functors in the sense that Applicative Functors can be implemented in terms of the Monad’s return and bind functions.  Using higher-level abstractions is nice when it’s available and in the lecture 4 Lars could have used an Applicative Functor instance of the Maybe and Either String types to implement the foo’ functions.

Seeing the word apply in conjunction with a function called pure or lift is a dead giveaway that the type you are dealing with is an Applicative Functor.  For example in the lectures the Haskell validator and policy functions are wrapped inside a Template Haskell type.  Runtime parameters are first lifted into the type and then the policy function is applied to those arguments.  I don’t know what the Template Haskell type is but I know it is an Applicative Functor.

An Applicative Functor shines for validation inside a validator.  For example a Plutus validator will have multiple conditions that need to be met for the script to return true.  Returning a Boolean works and if it fails the traceIfFalse function shows where the failure occurred but it doesn’t show why it did.  

I found it very helpful to return Either String from all helper functions that evaluate Plutus conditions.  The nature of the apply function for the Either String Applicative Functor instance is to return Right (a) if all function arguments are Right (a) or return Left String if any of the function’s arguments are Left String.  If a Left String is encountered the first one encountered is returned and the Applicative Functor “short circuits” to return that Left String.  If there is an error I get exactly the error message at the point where it failed.

Hopefully that’s a crash course on Applicative Functors.  You can find an in depth discussion on Applicative Functors in the respective Lambda cast podcast episode.

In this post I described deploying code on-chain, NixOS and the nix package manager as well as Applicative Functors.  

For the next post I want describe off-chain code including the Emulator Trace and Contract Monad as well as describe a Monad in the general as I understand it.
