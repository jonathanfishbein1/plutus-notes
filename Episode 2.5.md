# Episode 2.5

I had originally wanted to talk about Applicative Functors, Nix and NixOS as well as deploying on-chain code.  However, in the past few weeks I have been re immersed into the JavaScript ecosystem and the experience has triggered more general thoughts related to programming style, coding ecosystems, and personal growth.  I also wanted to relate these thoughts back to the discussion about Haskell being an appropriate language to pick for Cardano.  The last episode was pretty technical so I wanted to indulge in a tangent and talk more generally.

The overarching point I’d like to make is that there are different types of hard.  Haskell is hard but losing all your money is harder.   That end result, losing all your money, is dictated by a series of decisions about which hard to embrace.

In previous episodes I’ve talked about the difficulty debugging imperative programs and the utility of a typed functional language to reduce that burden.  For this episode I want to talk about another aspect of hard.  Haskell is hard but the fatigue induced from the myriad of different language versions, language primitives, design patterns, libraries, frameworks, and tools found in imperative ecosystems is also hard.  Haskell has a steep learning curve, the concepts are abstract and take time to mull over but they are they are the synthesis of the best ideas out of the chaos of imperative languages, formalized and consolidated.  

To elaborate on that point I’d like to start at the beginning, I want to share my coding experiences the successes and the failures, describe what was hard and when, and share how I formed my current views and perspectives about programming.

My first experiences coding was using something called WinBatch, basically just a proprietary windows bash programming language.   It’s concrete, used to manipulate files and automate existing Windows applications.  In that way it was a great way to learn to program.  

My initial attempts at improving my code were awful in hindsight.  I attempted to modularize code by breaking it up into files.  However WinBatch has no concept of a module so there was really no way of calling code from one file to another.  Instead I would write data to a file from the caller and then the callee would open the file and read the data.  It was totally uncontrolled side effects and my program would crash consistently because of IO malfunctions.

Winbatch was simple, concrete the perfect language for someone just learning but as I progressed programming in Winbatch became harder.  My scripts were unreliable.  They were imperative, not typed, had uncontrolled IO, not modular and not composible.

Eventually I converted the WinBatch over to C#.  C# is harder than Winbatch in the sense that the concepts are more abstract it’s being a typed Object Oriented language, 

The first win with C# I could separate my code more effectively and pass data around without having to do IO additionally I had the added benefit of static types.  Nevertheless, my program was still a long list of imperative instructions, that was still not modular and not composable.

The next evolution was using objected oriented principles.  Objected oriented programming emphasizes bundling and compartmentalizing data and behavior into a unit.  In that way your program is composed of objects and the program flow is their interactions with each other as opposed to a series of instructions.  It’s more abstract than plain imperative code and in that way harder but the colocation of data and behavior broke the problem domain into smaller pieces.  I was able to break up a larger problem into smaller problems, solve them, and then compose the pieces together.

There are many articles that discuss the pitfalls of OO, fragile inheritance hierarchies, mutation with parallelized programs.  But I never experienced any of these issues, instead my transition from OO to FP was much more mundane.  I moved logic out from the class definition into static functions attached to mimic pure functions, explicitly passed in parameters and marked each parameter as a value type, an adhoc way to make a program more immutable by coping reference types.

At the same time I was working with C# I was also learning JavaScript by developing a chrome extension that could track visitors over time to your okcupid dating profile.  

The progress of learning JavaScript was particularly tedious.  I started out querying the page, scraping the data off.  Eventually I moved on to querying the API, advanced on to jQuery thinking that was a major productivity boost.  I dabbled in the murky world of object oriented JavaScript learning and implementing the Prototype, Constructor, and the combination Constructor/Prototype patterns again What made the JavaScript OO particularity interesting was that the Constructor Prototype pattern was a pattern to mimic OO inheritance and I was beginning questioning the necessity of OO in the first place.

Additionally, I started creating smaller function, composing them into larger ones and using them in conjunction with map, filter, and fold with the help of utility libraries underscore, lodash and finally rambda.  Again this is a more abstract way to program.  I am making functions unrelated to each other and composing them together.  In that way the logic got simpler, as it got simpler, I moved them from the static methods of classes into composition root.

My next experiences was developing a website.  I progressed through Angular 2, React and then found Elm.  There were a lot of beginner mistakes, I was convinced that Angular 2 would be good because it was the newest framework of 2016, React was 2015, not a too in depth analysis.  I quickly realized it wasn’t so great.  

I loved TypeScript and I felt it reduced a lot of errors that I would normally encounter but there was a loop hole in Angular 2 as far as parent child component communication, they communicated over a HTML string and I don’t remember all the details this was years ago two variable names had to line up.  I went 48 hours and only after I posted the question on StackOverflow I realized that one of the variable names had a capital P and the other one didn’t.  Sort of a wall of shame on my StackOverflow page.

The bug with Angular 2 was annoying enough for me to consider alternatives.  I looked at React but React also had some problems associated with it.  I wanted to use React, I knew they used JSX but I wanted to add some typing support to my JSX files so I wanted to use something called TSX and I wanted to use Redux but that brought up some additional questions should I use Redux-thunk, Redux-promise, or Redux-saga.  Also I was using JSPM because that was what Angular 2 was using  should I switch to Webpack because that was more in the React ecosystem.  And there were all these other questions such as should I use ImmutableJS, should I switch to Flow from TypeScript.

It’s this JavaScript fatigue where you have all of these libraries, frameworks, and tools all going off in different directions and it’s hard to get them all to work together.  With Elm the whole greater than the sum of its parts.  I guess the point that I would make is that you can sort of program in a style very similar to Elm using more native JavaScript libraries and frameworks but it’s not going to be the same.  Elm coherently packages up all these technologies to make it a much more pleasant experience.

For the back-end of the website I perhaps made my biggest mistake.    I had little to no database experience so I contemplated the differences between a document, relational, and graph database.  From what I gathered a graph database was a relational database with a simpler more explicit model, types and their relationships to each other as opposed to the more mechanical, more implicit tables and reference keys.

Coming from the .NET world I picked Azure as the cloud provider.  Azure had Cosmos DB, a database designed to support different database models including graph and document.  However, the graph model only included an untyped query language.  If only I had gotten over that hangup, my backed could have been 100 lines of untyped code.  Instead I used the document database model and architected a colossal typed monstrosity.  I guess the silver lining though was a cautionary tale to make things as complicated as they need to be and no more.

Functional programmers can move beyond idealists to ideologues when advocating FP so I mention this story as a way to ground myself.

Over the past week I’ve been working with the Nami wallet and the nami-wallet-api.  I am integrating it into the the Cardania website so that you can purchase tokens with a one-click buy button.  The current implementation isn’t non-custodial, meaning it will still communicates with the vending machine transitively, but it is a big step towards that goal.

Nami wallet is a chrome or brave extensions where you can hold funds.  Extensions can inject context into the web page, in this case Nami wallet is making accessible a wallet object that holds the wallet state including, address and utxos.  

Installing the nami-wallet-api onto a web page allows it to access and interact with the users wallet.  

Incorporating the nami-wallet-api proved to be challenging.  I was getting errors while using Webpack, switched to Parcel and still got the errors.  Eventually figuring out it was a package version problem going back to Webpack filling in the webpack.config.js.  From there I folded in TypeScript added a tsconfig.json file and then wrestled with the multitude of module formats of JavaScript, CommonJS, AMD, ES 6, System, the list goes on.

The point isn’t so much the troubleshooting but the underlying problem that the JavaScript ecosystem is chaotic.  Should I use var, let, or const, for in, for loops, or map?  Named functions, anonymous functions, immediately invoked function expressions, or arrow functions? Which module syntax should I use immediately invoked function expressions, CommonJS, AMD, System, ES 6 and If I use CommonJS which bundler should I use browserfy, webpack, or parcel?  How should I handle my dependencies npm, yarn, bower?  Should I add typings and if so which one TypeScript or Flow?  Then there is the explosion of design patterns, frameworks, package managers, packages, libraries and development tools.  

Elm provided good answers to these questions.  Should I use var, let, or const? You don’t have a choice all values are immutable, for in, for loops or map, for in and for loops don’t even exist in the language.  While Elm and Haskell have named and anonymous arrow functions the naming doesn’t give the function special semantics, both are just values.  Which module system? There’s only one.  Which typings? Already apart of the language.  There’s no design patterns, there’s no frameworks, one package manager, and there aren’t hundreds of thousands of packages.

The JavaScript ecosystem is a concrete example of a chaotic ecosystem but the point can be generalized for all imperative languages and their ecosystems.  I think the overarching point I’d like to make is that all the pitfalls and deadends of predecessor languages are solved in Haskell and they are solved once.  

I like to think of the surface area of an imperative impure program is huge.  There are so many spots a bug could be, it’s a full time job just to fix it.  Similarly, the surface area of JavaScript programming, language, syntax, frameworks, tools, and libraries is also enormous and also a full time job to stay up to date.

Conversely the learning curve is steep with Haskell but the material has a small surface area.  The runtime bug surface area is minimized and as is the combination explosion of design patterns, frameworks, package managers, packages, libraries and development tools

I think this brings us to the final point I’d like to make. In the scheme of things it’s the least important. Re architecting the global financial system, banking the unbanked, empowering people with real ownership of their money, augmenting the exploitative gaming model are the most important.  But at least for me and probably for many other developers is personal development.  I don’t want to be spending hours debugging undefined is not a function similarly I don’t want to be relearning JavaScript every six months if I can help it.

This was episode 2.5, I have an episode 3 planned but I think I will plan another interim episode.  The third cohort of the Plutus Pioneer program starts starts next week and I figure it’s a good opportunity to discuss preparations for it.  So look forward to that coming out soon.
