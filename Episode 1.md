# Episode 1

I am currently learning how to develop Plutus smart contracts and I wanted to share my experiences doing so.  Also, my goal for this podcast is to provide other developers learning Plutus an additional resource and a good way to review core concepts.

Podcasts have been a great tool for me learning to program and in particular learning functional programming so much so that I wanted to take the initiative in the Cardano community to provide that resource.  I would listen to LambdaCast in the car, at the gym, out for a walk and I can’t recommend that podcast enough especially if you are also learning about functional programming.  I will drop a link to their podcast in the show notes.

This is the first episode so it’s just me but I would love to get other developers also on their learning journey to have a dialogue instead of this first monologue hopefully seasoned Plutus developers as well as IOHK engineers will also join.  

This podcast is designed for developers looking to learn Plutus.  I’ll go into detail concepts necessary for developing smart contracts with only the bare minimum of references to Cardano going to the moon and all of us buying lambos.

I’m trying my best but I’m not a professor so if you notice any mistakes in the content send me a DM on Twitter @JonathanFishbe6

For the first podcast I wanted to give myself a brief introduction, give a description of of Haskell I wanted to talk about it, a 10,000 foot view of it, in particular how you often hear it described as a statically-typed, functional, pure, lazy language.  I want to go over what each of those terms mean., discuss the utxo and eutxo models, In the Plutus Pioneer lectures, a few words pop up every episode including Monoid, Functor, Applicative, and Monad I want to get to all 4 but in this episode I want to elaborate on Monoids.  Finally I want to talk about my local environment I use for Cardano development

I started out as a C# desktop developer, moved on to .NET web applications.  For jobs I stayed within .NET, with hobby projects I explored functional programming.  My first few experiences with functional programming were fp inspired JavaScript libraries such as rambda and TypeScript.  I also had the opportunity to develop with Elm which is similar to Haskell in most ways.  Elm has been described as a toy Haskell and that’s certainly one perspective, another is that the more abstract concepts from Haskell are excluded from Elm in favor of a more simple, get to work, programming interface and language.  I certainly had a great experience using it and I think the learning material is very approachable.

Recently I have been working with Cardania, an NFT playing and trading card game.  I developed the vending machine using Haskell as a way to prepare for Plutus.  And although the vending machine has had it’s ups and downs I still was able to learn a lot about Haskell and Cardano.

So that’s me and what I’m up to.  Now I wanted to talk more in depth about Haskell.

Haskell has the reputation as being a hard language to master, but I would best describe it as unfamiliar.  In many ways it’s easier than enterprise languages, fewer opportunities to mess up and fewer design patterns to learn but at the same time there is a learning curve associated with Haskell.

I want to give a detailed overview of Haskell and I think a good way to start is elaborating on the four descriptions you often encounter when reading about it,  These are going to be pragmatic definitions and examples, not super academic.  These descriptions are statically-typed, functional, pure, and lazy.

The general consensus is statically-typed languages can provide compile time errors where dynamic languages fail with an error at runtime.  The cost associated with static-typing is more boilerplate to identify types and write down type definitions.  With dynamic typing development can be fast, a critical feature in business to dominate a market while static-typing development is considered slower and steadier.

Haskell can straddle both sides because it features a powerful type inference engine under the hood that allows for statically-typed code without requiring verbose type definitions and so resembles the terseness of a dynamically typed language.

What may be new to developers is that within the statically typed language universe there is a spectrum.  For example in C# and Java, often times a function returns an Int or Null.   But Null is not an Int and other code that relies on the result of the function being an Int can error if it encounters a Null.  And so you end up with Null checks littered all over your code.  And that seems awfully similar to checking types in a dynamically typed language.  Haskell does not feature Null in the language, anywhere, and so in a sense is more statically-typed than other statically-typed languages

The next description of Haskell is Functional.  For the me the most practical definition of a functional language is one in which that uses map, filter, and fold as the workhorses of the language.  But to use these three workhorses a language needs to support two features, namely functions as values as well as higher-ordered functions.  

What does that mean? well functions as values means that you pass around functions similar to any other type.  You can keep them in arrays, assign them to variables and pass them into other functions.  Higher-ordered functions are functions that accept as parameters other functions, map and filter are great examples.  Map works over lists and accepts a function that transforms a list element, map also takes a list of those elements and returns a new list with the transformed values.  Filter is similar except that it accepts a particular kind of function argument, namely a predicate that returns a Boolean.  

What are the advantages of using map, filter, and fold instead of while loops, or for loops?  Well, it’s an abstraction, instead of allocating a looping variable, incrementing that looping variable and iterating until the end of the array, that’s all baked into map, filter, and fold.  Additionally, certain error types disappear such as off-by-one errors, when you mistakenly iterate past the last index of a list, this is a runtime error in imperative languages, although, it’s fairly easy to diagnose and fix, functional languages simply aren't capable of generating them.

I think most programmers would be familiar with static vs dynamic typing comparisons and intuitively understand how within the family of static languages there is a spectrum of static-ness.  Also I think most programmers are aware of map, filter, and fold, that style of programming has been creeping into JavaScript, C#, and Java.  The next two concepts are less familiar.

What makes a pure language and what attributes of a language would violate purity?  Strictly speaking a pure language is one in which for each and every function if you send in the same arguments you are guaranteed to get exactly the same result.  It’s a simple definition but there are quite a few ways purity can be violated.  For example, any function that returns the current time, or a random number is not returning the same value on each invocation and is therefore impure.  Obviously, randomness and time are important features of any program but they are handled a little differently in Haskell.

There are other ways purity can be undermined.  Mutable variables can cause impurity, if you have a function that simply returns the value of a global mutable variable every invocation of that function can return a different value given the same inputs.  This also manifests itself with global mutable collections.  If a function just returns the value of a global mutable collection every invocation of that function can return a different collection.  So in Haskell in addition to randomness and time being handled specially, all variables are immutable including collections.

There’s another very important aspect of purity and that’s communicating with the outside, over the web, to a database, or to a file.  Purity would be violated if there was uncontrolled input/output.  If you had a function that returned the result of a query to a database that function can return a different value on each invocation because the database can be updated from anywhere.  Input/output is handled similarly to time and randomness basically, these functions are isolated to a separate portion of your program from the majority pure portion of the program  I like to think of an impure program as similar to a Rube Goldberg machine where code in one portion of a program can affect code in an entirely different portion or spooky action at a distance.

The last Haskell description is lazy.  I know in C# there is something called LINQ, Language integrated Querying, it’s a querying syntax that works on top of an ORM or object relational mapping.  Developers  write C# SQL queries that are converted into an abstract syntax tree, then serialized, sent to the database and executed when specifically asked for.  There is a delay in the definition of the expression and it’s execution, it’s delayed execution and is described as lazy.

And that design makes sense, the goal of communicating between application code such as C# and the database using SQL is to leverage the optimized querying capabilities of the database and to reduce intercommunication over slower networks.  Lazy execution guarantees that the code is only executed when the query is fully formed.

I still don’t understand the full implications of lazy evaluation when it is the default such as in Haskell.  However, programming just a few months ago I was able to exploit the feature by constructing an infinite list.  It sounds impossible but Haskell being lazy only realizes the list up until the point necessary.  Again it’s an abstraction, to not have to explicitly worry about the length of a list when the length is unknown at compile time.

Hopefully, I’ve provided practical definitions and examples of what static-typing, functional, pure, and lazy all mean in general and specifically to Haskell.

I think the majority of Haskell can be picked up quickly coming from other languages particularly if you are coming from a statically-typed language and are accustomed to a functional style of programming.  However, there is some lexicon in Haskell that is down right foreign sounding.  These terms include Monoid, Functor, Applicative and Monad.  Later in the podcast I thought I would shed some light on Monoid, it’s important in Haskell, it shows up in the Plutus lectures and it’s pretty interesting.

But first I wanted to dive deeper into understanding the underlying Cardano model, to do so I attempted to compare it to relational databases.  After all a blockchain can be viewed as a decentralized database, viewed in this way we can analyze it using the canonical ways of analyzing a centralized transaction-oriented database.

In relational databases tables can have a one-to-one, one-to-many, and many-to-many relationships with each other.  The canonical example is the relationship between an Employee and a Department.  A small and flat organization can be represented by a one-to-one relationship between the tables.  Each employee works in one department and each department has one employee.  In contrast, the table relationship in a big hierarchical organization would be a one-to-many where each employee is assigned to one department and departments have many employees.  Finally, the many-to-many relationships would be a good model for a startup where employees have many roles.  So for example, a department would have many employees but also an employee might be assigned to many departments.

This was the context I was applying to the underlying Cardano model to decipher differences between keys, addresses, utxos and wallets.  I’ll start with keys, there are two types of keys a public key called the verification key and the private key, or secret key, or signing key.  An address is the hash of the public key.  There is a one-to-one relationship between the two.  One public key corresponds with one address and vice-versa. 

Addresses contain utxos there exists a one-to-many relationship between addresses and utxos.  An address can have multiple utxos attached to it while each utxo is identified with only one address.  

A wallet is a container for multiple addresses and these addresses can have multiple utxos attached to them.  In Daedalus a wallet can have multiple addresses shown on the Receive tab, under the hood of Daedalus there is one root public/private key and then there are derivative public keys generating derivative addresses.

I think keys are familiar to most, addresses are intuitive as are wallets.  But what are utxos.  Utxo is an abbreviation for unspent transaction output.  It’s the output of a prior transaction.  A piece of data sitting at an address with a value in Ada.  Each utxo is identified by the hash of the transaction that produced it as well as an index value corresponding to it’s position in the list of utxos produced by the creating transaction.

The relationship between addresses and utxos is one-to-many.  However, two utxo can have the same transaction hash id but sit at different addresses.  The identical transaction hash id corresponds to the fact that they were produced in the same transaction.  Their individual index values distinguishes them as different utxos.

The defining characteristic of the utxo model is that in a transaction the entire utxo has to be consumed.  The transaction creates new output utxos.  For example, if Bob has a utxo with 10 Ada and wants to pay Alice 5 Ada then the full 10 Ada is submitted in the transaction.  5 Ada go to a new utxo that belongs to Alice and 5 Ada go back to Bob but now at a new Utxo.  This is similar to how physical money, coins and bills are spent.  Bills and coins are consumed fully and change is returned to the buyer.

The alternative to the utxo model is the account based model used by Ethereum.  The real world analogy of the account based model would be a debit card.  When paying with a debit card, money is withdrawn from an account and debited into another.

What are the advantages and disadvantages of each model? Again I like to refer to what I know and the canonical way of analyzing centralized relational databases has been it’s conformity to ACID principles.  ACID is an acronym standing for Atomicity, Consistency, Isolation, and Durability.  In particular, I think the utxo model exemplifies atomicity and isolation.

The immutable nature of utxo aligns well with atomicity.  There is no intermediate steps of a transaction, either the transaction was successful and there are new utxos or it failed and the input utxos remain unchanged.  In contrast, in an account based model there are intermediate steps, namely the payer account needs to have the funds withdrawn and the account balance updated and the payee needs to be debited.  Atomicity can be achieved and it is achieved but those guarantees have to applied via an external mechanism, it’s not fundamental to the transaction.

The utxo model also aligns with the ACID principle of isolation.  Recalling the analogy of utxos being like cash.  Your physical wallet contains many paper bills and coins similarly, your Cardano wallet contains many utxos.  When you engage in a transaction in the physical world you only remove the bills and coins necessary to pay for the transaction, the other bills and coins in your wallet remain untouched.  Similarly when submitting a transaction in Cardano, only the utxos required for the transaction are accessed all other utxos owned by the buyer are not accessed.  In comparison the account model has one account state that gets accessed on each transaction.

eutxo is an abbreviation for the extended unspent transaction output and adds the ability for smart contracts on Cardano.  The eutxo model augments the utxo model by adding addresses determined by the hash of a Plutus script in addition to addresses determined by the hash of a public key.  

In the regular utxo model address output is unlocked via a digital signature or the private key of the owner of those utxos.  The eutxo generalizes the concept of unlocking address funds by allowing the funds to be unlocked with arbitrary logic.  This is achieved through a script that verifies a combination of three factors, the first being a piece of data sitting at the utxo called the datum, the second is also a piece of data called the redeemer coming from the consuming transaction, and the third is the script context being all the inputs, their associated utxos as well as the new outputs of the transaction.

OK so that was an introduction to the utxo model, a comparison with the account model and the eutxo model.

So now I wanted to talk about some of the more unfamiliar language you encounter when starting out with Haskell and that are mentioned in the Plutus Pioneers lectures starting out with Monoid.

A few years ago I was excitedly telling a friend about functional programming and how it was based on math.  He said but you can do math in Java and certainly the math utilities are based on Math and I realized I had no idea what I what I was talking about.  What exactly did saying functional programming is based on Math mean.  A few years later I know a little more.  Saying Haskell is based on Math, is lacking in detail, Haskell is based on the organization of Math, the taxonomy, the hierarchy, the classifications of sets and operations on sets

To explain what a Monoid is let me start at the beginning, at a Magma.  What is a Magma, it’s spelled the same way as the lava from volcanoes, in this context it refers to a set, any type of set, a set of numbers, a set of letters, a set of shapes  The definition of a magma includes a binary operation between elements of the set and results in another element of the set, it’s closed as it’s called in Abstract Algebra.  That’s the definition of a Magma, a set, and a binary operation that’s closed.  

A magma isn’t too useful but if you go one level up the hierarchy you get to something called a Semigroup and in the Plutus lectures, Lars mentions these.  A semigroup is everything a magma is with the additional property that the binary operation is associative.  In the Plutus lectures Lars refers to the binary operation as mappend, that’s append with an m in front.  I believe that’s a relic, and was attached to the Monoid type before Semigroup was added to the language.

Monoid is one level up from Semigroup and is everything Semigroup is with the additional requirement that the set needs a neutral element, a mempty element.  Lists are monoids, with the operation being concatenation and the neutral element being the empty list, similarly with strings except the neutral element is an empty string.  Integers and addition form a monoid with the empty element being zero while Integers and multiplication form a different monoid with the empty element being 1.

What advantages can we reap knowing a type is a monoid?  Well if a type is a monoid and you have a ton of those monoid values and you have to condense all those monoid values into 1 you can employ a divide and conquer strategy and know it will work because the binary operation is associative.  You can reason about the type in an abstract way.

In the Plutus lectures a few monoids are mentioned including the Value type and the Last monoid.  The Value type is the underlying type defining value, or money, or tokens on Cardano.  It’s a map of maps where the first key is a currency symbol and the key of the inner map is a token name.  The neutral or empty value of the Value type would be the empty map while the append operation would be a composition of two Map inserts one for the outer map and one for the inner.

The Last monoid is also mentioned which is just a wrapper around the Maybe type.  Here Nothing would be the neutral value because Nothing appended to any other Last value wont change the value.  The append operation would be a little more involved with four scenarios, if both values are Nothing return Nothing, if the first value is Nothing, the second one Just return Just, if the first value is Just and the second one is Nothing return Just and the final case is that both values are Just and in that case return the last Just as the type name suggests.

The next level up from a monoid is a group.  I wont get into group theory only mention it because of all the algebraic structures in the hierarchy, group is probably the most famous notably developed by Evariste Galois who tragically died at the age of 20 in a duel.

I want to end the podcast by talking briefly about my development environment.  Currently I’m running Qubes OS, Qubes is a security focused operating system but I’m using it primarily because it’s very suitable to running multiple virtual machines.  

For example I have my Daedalus wallets contained within their own virtual machines isolated from my personal tasks and coding development environment.  

It’s an additional layer of security to protect my Ada because at the end of the day we are the only ones responsible for it.

For my coding environment I have a dedicated virtual machine running NixOS.  Nix is used extensively in the Cardano ecosystem.  Lars mentions the Nix package manager in the first lecture as the most reliable way of building Plutus.

My goal was to set up a development environment where I had everything I needed and absolutely nothing I didn’t, similar to an isolated science experiment.  I’ve taken that idea further and instead of using a bloated desktop manger such as gnome or KDE on the NixOS VM, I’m running a more bare bones i3 window manager.

With NixOS my environment is declarative.  I specify in the nix configuration file how I want my system to be set up and then build it as opposed to the more imperative Linux OS such as Ubuntu where you start with the initial download and over time you install and uninstall and after the six months or so the system is very different than the one you initially started with and it’s not totally clear how it’s drifted

For code editing I’m using vscodium and I recently updated the Haskell extensions to the latest version and although I pushed them to the unstable channel for NixOS packages for some reason that hasn’t been reflected, that’s something I have to figure out.  Currently I’m building NixOS from the master branch.

So that’s about it from me.  Today I gave a high-level view of Haskell specifically elaborating on the four terms you hear it described as, statically-typed, functional, pure, and lazy.  I also went into detail about the utxo model, compared it to the accounting model and then described the extended utxo model that Cardano is using to facilitate smart contracts.  Additionally, I talked about monoids, what they are, what they’re good for, highlighted the ones Lars uses in the Plutus Pioneers lectures and gave additional examples.  Lastly I described my development environment and why the combination of Qubes OS and NixOS is compelling.

For the next podcast I want to start with on-chain code, datums, redeemers, script context and validators as well as describing Functor, the next most approachable definition required for Haskell development.

I am looking for other developers to join me on this podcast, a dialogue is much more enjoyable to listen to.  If you are interested send me a message on Twitter.
