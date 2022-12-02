

[![img](https://github.com/f1f47a23/AWESOME/blob/main/docs/solidity-eth/why-solidity-sucks.jpg)](https://medium.com/crypto-security-top/the-problem-with-solidity-be7e6c277a58)

#  💩 WHY SOLIDITY SUCKS? 💩

<br><br>


      
### NOTES 1
      
	
	
int_19h on July 20, 2017 

Just skimming through the Solidity docs, I see a lot of unwise decisions there aside from the weird visibility defaults.

All state is mutable by default (this includes struct fields, array elements, and locals). Functions can mutate state by default. Both are overridable by explicit specifiers, much like C++ "const", but you have to remember to do so. Even then, the current implementation doesn't enforce this for functions.

Integers are fixed-size and wrap around, so it's possible to have overflow and underflow bugs. Granted, with 256 bits of precision by default that's harder to do than usual... but still pretty easy if you e.g. do arithmetic on two inputs.

Operators have different semantics depending on whether the operands are literals or not. For example, 1/2 is 0.5, but x/y for x==1 and y==2 is 0. Precision of the operation is also determined in this manner - literals are arbitrary-precision, other values are constrained by their types.

Copy is by reference or by value depending on where the operands are stored. This is implicit - the operation looks exactly the same in code, so unless you look at declarations, you don't know what it actually does. Because mutability is pervasive, this can can have far-reaching effects.

Map data type doesn't throw on non-existing keys, it just returns the default value.

The language has suffixes for literals to denote various units (e.g. "10 seconds" or "1000 ether"). This is purely syntactic sugar, however, and is not reflected in the type system in any way, so "10 second + 1000 ether" is valid code.

Statements allow, but do not require, braces around bodies. This means that dangling "else" is potentially an issue, as is anything else from the same class of bugs (such as the infamous Apple "goto fail" bug).

Functions can be called recursively with no special effort, but the stack size is rather limited, and it looks like there are no tail calls. So there's the whole class of bugs where recursion depth is defined by contract inputs.

Order of evaluation is not defined for expressions. This in a language that has value-returning mutating operators like ++!

Scoping rules are inherited from JS, meaning that you can declare variables inside blocks, but their scope is always the enclosing function. This is more of an annoyance than a real problem, because they don't have closures, which is where JS makes it very easy to shoot yourself in the foot with this approach to scoping.


	
	
vog on July 20, 2017 | next [–]

I never understood why they chose such a hacky language (an a VM model that encourages these kinds of languages), and expect people to write supposedly secure (in the sense of: obviously correct!) code with it.

Any remotely popular functional programming language created over the last years shows a better design (and taste) than this one.

And if that only attracts a certain type of programmers? (pun intended) That is, programmers valuing languages like Haskell/OCaml/F#/Kotlin/Rust, who would only ever touch C with verified compilers and 5 different static analysis tools?

Well, that's exactly kind of people you to attract to write your security-sensitive code.

	
	
avaer on July 20, 2017 | parent | next [–]

99 out of 100 questions.

Solidity is ostensibly designed to let people write smart contracts for Ethereum.

More realistically, it is a marketing tool for enabling and onboarding people onto the Ethereum platform, which Ethereum benefits monetarily (enormously so) from. Security and design are secondary goals to the extent that they help prevent disasters which hurt adoption or churn developers away.

Through this lens it's not a mystery why the language is "hacky". Perhaps being a good language is not even the driving goal.

	
	
vog on July 20, 2017 | root | parent | next [–]

> 99 out of 100 questions.

Curious question of a non-native speaker: What does this phrase mean? (in general, and in this context)

	
	
brian_cloutier on July 20, 2017 | root | parent | next [–]

Don't feel too bad, I am a native speaker and I didn't recognize this phrase either.

	
	
jaytagdamian on July 20, 2017 | root | parent | prev | next [–]

http://persuasive.net/what-is-the-answer-to-99-out-of-100-qu...

	
	
boycaught on July 21, 2017 | root | parent | next [–]

Brilliant... and so true!

	
	
thegavsie on July 20, 2017 | root | parent | prev | next [–]

The answer to why most things are done is... Money !

	
	
samblr on July 20, 2017 | root | parent | prev | next [–]

>> it is a marketing tool for enabling and onboarding people onto the Ethereum platform

+1 : My feelings after going thru basics of ethereum.

	
	
jes5199 on July 20, 2017 | root | parent | prev | next [–]

a marketing tool that gets your money stolen is probably counterproductive in the long run

	
	
erikpukinskis on July 21, 2017 | root | parent | next [–]

From a Hegelian perspective, the best thing for Ethereum is for it to race back and forth across dialectics as quickly as it can. There will be many money stealing events as we discover how to do cryptocomputing safely. Better to get them over with now while the market cap is small and people know not to rely on it for anything too serious.

If they were to be as safe as possible, there would be less news, and they would push the traversal of those dialectics further into the history, when more people are using Ethereum and these kinds of events can do more damage.

It's beta. Don't worry about it. Come back in 10 years when the Heroku of Ethereum is released. Buy a couple Ethers now so you'll be rich then.

	
	
user5994461 on July 21, 2017 | root | parent | prev | next [–]

The guys who just stole $30M and $70M beg to disagree.

	
	
beaconstudios on July 21, 2017 | root | parent | prev | next [–]

but so is making developers write smart contracts in haskell, severely limiting the number of developers who can jump in and start coding, leading to a lack of adoption.

	
	
federicobond on July 20, 2017 | root | parent | prev | next [–]

Being a good, secure language for writing smart contracts is absolutely one of the most important goals for Solidity. I can say so because I have been involved as a regular contributor to the compiler for more than a year. If onboarding a lot of people to the platform quickly were the goal instead, they would have ported some crippled version of Java or JS and called it a day.

The problem is that it's just not easy to get all those details right from day 1, and interest in Ethereum has grown way faster than its capacity to produce a reliable, mature set of tools around it.

	
	
mrkgnao on July 20, 2017 | root | parent | next [–]

To pick just one: mutable state by default is very difficult to justify as being a decision intended to promote security in a tool that is literally meant to construct financial/contractual instruments.

	
	
federicobond on July 22, 2017 | root | parent | next [–]

Not that difficult. There is not much point in creating immutable contracts since they act like singletons.

This is different from the kinds of objects that you would instantiate in a language like Java, where best practices suggest defining as immutable unless you have a good reason not to.

	
	
int_19h on July 22, 2017 | root | parent | next [–]

Just because a contract is mutable doesn't mean that everything needs to be.

For starters, not all functions on the contract are supposed to be mutating. Marking those that are as such would catch accidental mistakes when unnecessary mutations happen.

Then there's the issue with structs, collections etc all also being mutable by default, which is separate of contracts.

Finally, none of this actually requires any mutability at all. A contract could treated as a pure function that takes initial state as input, and returns the new state as output. Such a function can be written without any mutations at all, by constructing the new state piece by piece from the old one.

Sure, it would be a more verbose (although functional languages usually offer shortcuts like "give me a copy of this object, but with these fields set to different values", making it not so verbose after all). But on the other hand, it means that you have to explicitly consider the effect of every operation of the contract on every bit of state. "Don't repeat yourself" is not always a good thing - when it comes to security, you often do actually want to make people do redundant things, e.g.: http://www.atlasobscura.com/articles/pointing-and-calling-ja....

	
	
twblalock on July 20, 2017 | root | parent | prev | next [–]

These things are not just details. Things like scope, mutability, and operator precedence are core language design decisions.

	
	
LeonidasXIV on July 20, 2017 | parent | prev | next [–]

> Well, that's exactly kind of people you to attract to write your security-sensitive code.

Unsurprisingly, Tezos is actually written by OCaml people who seem to value the correctness of the contract language.

	
	
cvsh on July 20, 2017 | parent | prev | next [–]

>Well, that's exactly kind of people you to attract to write your security-sensitive code.

But not the kind of people you want to focus on to get traction for your venture-backed startup if you want a relatively quick and profitable exit

	
	
federicobond on July 20, 2017 | parent | prev | next [–]

None of the languages that you mention target the EVM, and the changes required to do so would turn them into restricted versions that look kind of like the original ones but have several differences that you need to keep in mind while programming in them. It makes quite a lot of sense to create a new language that takes advantage of the particular features provided by the EVM.

It's also quite hard for programmers to reason about performance or complexity in most functional languages. In the EVM, all functions should either be O(1) or bounded O(n) and should strive to use the most economical bytecode available to perform each task.

Disclaimer: I am a regular contributor to Solidity.

	
	
vog on July 21, 2017 | root | parent | next [–]

> should strive to use the most economical bytecode available to perform each task.

This is why I criticized the VM design as well as the language.

Yes, much effort went into making "cycle counting" as simple and straight forward as possible.

But I don't see any comparable effort doing the same for safety and correctness. (Apart from providing crypto primitives by the VM, which is neat, but only the tip of the iceberg in terms of language design.)

> It's also quite hard for programmers to reason about performance or complexity in most functional languages

This is true for Haskell, but "most" other functional languages don't suffer this issue: SML, OCaml, F#, Rust, etc.

Also note that lots of effort already went into creating lambda calculus variants where performance is especially easy to reason about. However, dismissing functional langauges as a whole already demonstrates that none that the research played any role in the design of EVM and Solidity.

In case you are interested, here is a nice starting point about the state of the art of 2014: "Structure and Efficiency of Computer Programs" https://www.cs.cmu.edu/~rwh/papers/secp/secp.pdf

	
	
federicobond on July 22, 2017 | root | parent | next [–]

> This is true for Haskell, but "most" other functional languages don't suffer this issue: SML, OCaml, F#, Rust, etc.

I don't have much experience with the ML family of languages, so I may have jumped to conclusions based on my previous experience with Haskell/Elm, but I did not intend it to sound as a dismissal of functional languages at all.

In fact, there is a growing community of researchers working on functional language design for the EVM, but I would wager that it's way more complex to build such a compiler than something a bit more "close to the metal", especially since no one had much experience with the implications of the memory/storage/execution model of Ethereum at the time work started on Solidity, let alone the kinds of vulnerabilities these contracts would be exposed to.

Hindsight is 20/20.

	
	
naasking on July 24, 2017 | root | parent | next [–]

> especially since no one had much experience with the implications of the memory/storage/execution model of Ethereum at the time work started on Solidity, let alone the kinds of vulnerabilities these contracts would be exposed to.

Perhaps the Ethereum devs didn't have this experience, but this experience and knowledge was out there. The E programming language has been a vehicle for smart contract and security research for over 20 years. How many Ethereum and/or Solidity devs read the E research, learned the E language and played around with it?

There is also decades of research on formalized smart contract languages. Nick Szabo is well known in this space, and he wrote a draft paper on a great contract language back in 2002:

http://nakamotoinstitute.org/contract-language/

How many of the devs have read this paper?

	
	
federicobond on July 28, 2017 | root | parent | next [–]

I mentioned that no one had much experience with the implications of Ethereum's model and you point me to a bunch of articles but I fail to see how they apply to the case of Solidity.

What are the big ideas from E that Solidity is missing?

Also, the article by Nick Szabo has some interesting ideas, but as far as I understand, it does not describe a language for programming arbitrary smart contracts, just some notation for common financial contracts.

	
	
eternalban on July 20, 2017 | root | parent | prev | next [–]

> It's also quite hard for programmers to reason about performance or complexity in most functional languages

Yet another data point for 'why a TC language is a bad choice for smart contracts'.

	
	
fn1 on July 20, 2017 | parent | prev | next [–]

Maybe they should have restricted their memory access model by using a stackbased language like Postscript.

	
	
TeMPOraL on July 20, 2017 | root | parent | next [–]

> like Postscript

That would be cool. Even cooler would be if they created a code style culture around small contracts such that a contract should print out a legal-looking document with its own source code, when echoed to a printer.

	
	
kaputsmack on July 20, 2017 | parent | prev | next [–]

Why did you bundle an equally experimental language like kotlin amid those other well established and widely used languages?

	
	
vog on July 21, 2017 | root | parent | next [–]

I just wanted to also add a JVM-based language to the list, and Kotlin is the only one I'm aware of that plays remotely in the league (in terms of language design/quality) of the ML languages.

	
	
masklinn on July 24, 2017 | root | parent | prev | next [–]

Kotlin is young rather than experimental, AFAIK there's no novel feature in Kotlin.

	
	
lngnmn on July 20, 2017 | prev | next [–]

Yet another homegrown PHP-sque language. The Fractal Of Bad Design is actually a pattern.

My bet they never took any PL course, and have very vague understanding of what consequences certain design decisions would produce. "20 years of PHP coding" or something like that.

Any decent PL course would emphasize the first-class-everything (procedures , obviously), lexical scooping for proper closures, strong but dynamic typing (no implicit coersions) etc. No matter which sect one belongs to - ML or Scheme - these are universal fundamentals, the well-researched foundation.

PHP/Javascript coders usually do not understand the principles and implications behind languages they use. Otherwise they would develop a healthy aversion, like to a tasteless, vulgar porn.

Sane people with PL background would use a dialect of Scheme as a DSL (the numerical tower and all the nice things) or at least Lua (because packers are afraid of parenthesis and see nothing behind of them).

Actually, Scheme (or a simplified dialect such as the one inside Julia - flisp) would be almost ideal choice for financial DSLs.

For those who are cosplaying a PL expert (with 20 years of PHP and Javascript coding under their belt) there is a good read: http://users.rcn.com/david-moon/PLOT3/

	
	
sethrin on July 20, 2017 | parent | next [–]

I feel like what you say is sensible, but that your tone is needlessly derogatory.

	
	
oxryly1 on July 23, 2017 | root | parent | next [–]

Needlessly? I disagree... the idea that yet another person decided to utilize Javascript as the basis for a new language is distressing and totally justifies such a response.

	
	
sethrin on July 23, 2017 | root | parent | next [–]

I think the tone of that comment is completely unjustifiable in any polite context. Also, bile obscures text: adding invective just lowers the SNR of your speech.

Your comment is an example of virtue signaling, which is equally boring.

	
	
dmak on July 23, 2017 | parent | prev | next [–]

I've taken a PL course, and still have a vague understanding of the consequences of certain design decisions. Just reading this thread alone has already taught me more and ways to think of why X design is needed and what consequence does it mitigate/resolve.

	
	
mifeng on July 20, 2017 | prev | next [–]

Thank you for succinctly summarizing all my frustrations with Solidity. I am developing a fairly complex smart contract (i.e. not a crowdsale) and it's fucking painful. Trivial computations that would take less than an hour to do in any other language take days to implement correctly.

Debugging is also a nightmare. There's no way to step through function calls or set break points, even though it's basically just Javascript. Multiple classes of bugs will generate the same "invalid opcode" error that is only visible at run-time with no indication of what line even triggered the error.

This is why I invested in the Tezos crowdsale. Smart contracts are a great idea but there's gotta be a better way.

	
	
pmarreck on July 20, 2017 | parent | next [–]

How do you create unit tests for Solidity code? This would eliminate much of the need for a debugger while also leading to safer code

	
	
mifeng on July 20, 2017 | root | parent | next [–]

Unit tests do help, and they are relatively easy in Truffle, the most used JS framework for Solidity. I guess I need to move to more of a TDD style than I'm used to.

	
	
hrpnk on July 20, 2017 | root | parent | next [–]

In fact the tolling around Solidity and smart contracts is quite rich. It's possible to write unit tests, execute contracts code on a private chain in a similar way to integration/API tests, and measure the code coverage.

Sadly, it's not a common practice to develop contracts with full use of such tooling. See [0] for an analysis of the recent 2017 ICOs and their use of the tooling in crowdsale contracts. A few gems exist, that do quite thorough testing including a CI approach.

[0] https://medium.com/@bocytko/would-you-trust-your-money-to-a-...

	
	
pmarreck on July 20, 2017 | root | parent | prev | next [–]

I cannot say enough good things about TDD, but YMMV. I also don't stick to it always... it's just that when I take the time to, my code clearly ends up better. I would imagine it's even MORE beneficial in code that deals with a money...

	
	
mifeng on July 21, 2017 | root | parent | next [–]

I find it hard to write tests when I'm still formulating the logic of how a problem should be solved. For money-based logic in Solidity, I now like to model it in MS Excel first, write code afterwards, and then finally tests.

	
	
pmarreck on July 21, 2017 | root | parent | next [–]

> I find it hard to write tests when I'm still formulating the logic of how a problem should be solved

I think everyone does. I certainly do. But pushing through that actually helps your designing, because you start thinking of things in terms of how the API will look, how each building block leading to the final computation will look, and the need to be able to unit-test it as you go forces you to write smaller, more tightly-focused components automatically, which ends up making more maintainable code that is easier to reason about

	
	
nicksdjohnson on July 20, 2017 | prev | next [–]

Speaking as an Ethereum core developer here - I see a lot of misdirected criticism and misunderstandings of Ethereum and the platform in the wider tech community. That said, your criticisms here are absolutely spot on; these are definite issues with Solidity as a smart contract programming language.

The only small correction I would make concerns this:

> Functions can mutate state by default. Both are overridable by explicit specifiers, much like C++ "const", but you have to remember to do so. Even then, the current implementation doesn't enforce this for functions.

When interacting with Ethereum you can do two things: send a transaction, or call a function against your local copy of the blockchain. The former is mutating, costs ether (in gas fees) and requires the transaction to be included in a block. The latter is entirely local, cost-free, and any mutations to state are ignored.

The 'constant' modifier in Solidity serves only to tag the resulting ABI of a function so that calls to it from common interfaces default to local calls instead of sending transactions. It's not intended to enforce that calls to that contract are read-only, only to indicate to callers that it should be called locally instead of creating a transaction for it.

	
	
int_19h on July 20, 2017 | parent | next [–]

Do you guys plan to address these at some point?

Actually, let me rephrase this. For better or worse, Solidity is a shipping product now, so you're constrained in your ability to make breaking changes. However, its status as the "official" blessed EVM language can be changed without breaking compatibility. So I guess a better question is, are you planning to replace Solidity with a language that has more emphasis on safety in its design (even if it's called Solidity 2.0 or whatever... clearly it would have to significantly break backwards compatibility).

Now, I know that most feedback that you hear is to just take an existing functional language. But after looking at Solidity and EVM specs, I also see why this is not necessarily feasible. And, as one of your contributors has pointed out in this thread, functional style can often be too high-level to keep track of the underlying opcodes and their gas cost.

But even sticking with the imperative approach, I think it's clear that there's a lot that could be changed, starting with adopting some key principles like "explicit is better than implicit", "don't pick a default unless it's absolutely safe" etc. Looking at the design of Solidity, it's clear to me that the primary inspiration was JS with a dash of C++. The one thing that's common to both is that they allow and encourage "clever" code. Perhaps looking at Ada, Eiffel, and even COBOL for inspiration would be more appropriate for this niche.

In any case, if you're interested in specific suggestions on how to redesign, I do have some thoughts, and would be happy to share them.

	
	
MicahZoltu on July 21, 2017 | root | parent | next [–]

There is an EIP that is slated to land in the upcoming hardfork that adds an EVM opcode for effectively "const" call that is enforced by the runtime: https://github.com/ethereum/EIPs/pull/214/files

There are also proposals out for a number of the issues you have mentioned. I believe that people _do_ want to make Solidity into a better language for safe code authoring, but some things require EVM changes which requires a hard fork, and other things simply require time (discussion and engineering). The impression I get is that at this point there _are_ some voices of reason involved in the Solidity language process, but it will take time before those voices can enact all of the changes listed here.

Also, keep in mind that when Ethereum launched it did so like any scrappy startup, with an MVP that was "good enough" to move things forward. Now that Ethereum is a leading contender in the blockchain race it is starting to have to pay for some of those early decisions.

	
	
kensai on July 20, 2017 | prev | next [–]

Seriously, these observations should be submitted to the Solidity team for consideration.

	
	
joncampbelldev on July 20, 2017 | parent | next [–]

they are excellent observations, but it seems pretty clear the team would be unable to implement them properly this late in the game, or worse unable to understand why they are a good idea.

	
	
federicobond on July 20, 2017 | root | parent | next [–]

Most of these observations are well-known to the Solidity devs. I know because I participate in many of the conversations around it. While it's not easy to evolve a language (the API surface is pretty wide compared to many other programs), Solidity is indeed changing and improving regularly, only limited by the available time of their contributors.

	
	
h1d on July 21, 2017 | root | parent | next [–]

I thought they had enough funding to hire just about anyone.

	
	
federicobond on July 22, 2017 | root | parent | next [–]

I may have enough funding to pay more that Brendan Eich is making right now. That does not mean that he will join my project. Good talent is scarce regardless of funding.

	
	
riffraff on July 20, 2017 | root | parent | prev | next [–]

Isn't the team also working on a new language targeting the same VM? If so, they might be useful.

	
	
gst on July 20, 2017 | root | parent | next [–]

There are lots of different languages targeting EVM. The (IMO) most promising so far is https://github.com/ethereum/viper

	
	
mbrock on July 20, 2017 | root | parent | next [–]

You can also just write bytecode directly. It's a simple stack machine, and contracts should be as simple as possible.

	
	
federicobond on July 20, 2017 | root | parent | next [–]

Things are not always simpler at a lower level of abstraction.

	
	
mbrock on July 20, 2017 | root | parent | next [–]

It depends what simplicity you mean. The Solidity compiler is very far from simple, so if you consider that part of the complexity incurred by using Solidity, then a reasonably small bytecode program might be much easier to audit. Consider that a typical smart contract is as simple as "if four out of these six addresses make the same proposal then execute it." You can basically write these things in hexadecimal without too much trouble.

	
	
federicobond on July 22, 2017 | root | parent | next [–]

Yes, that's definitely a good idea. I will go back to my magnetized needle now, I have some bit flipping to do in order to finish my task for Monday.

	
	
federicobond on July 22, 2017 | root | parent | prev | next [–]

Speaking seriously, there is an assembly for Ethereum called LLL. I would love to see code that performs exactly that example you mention and is easier to audit than the equivalent Solidity version.

	
	
smoyer on July 20, 2017 | root | parent | prev | next [–]

For a stack machine, Forth might be a great choice.

	
	
gradys on July 20, 2017 | root | parent | prev | next [–]

Can people outside of the Ethereum org implement their own languages that target that VM?

	
	
tscs37 on July 20, 2017 | root | parent | next [–]

Yes. The VM has a public specification and you can implement your own compilers for it.

On the blockchain, the VM only cares about the bytecodes you submit, not the code itself.

	
	
MichaelRenor on July 20, 2017 | prev | next [–]

This is an amazing write up. How long did you spend in the code before jotting this down?

	
	
int_19h on July 20, 2017 | parent | next [–]

I was literally writing this list down as I was reading through this:

https://solidity.readthedocs.io/en/develop/solidity-in-depth...

	
	
trevor-e on July 20, 2017 | prev | next [–]

Can someone explain to me why they chose to make their own language instead of creating a DSL inside of an existing language?

	
	
tomp on July 20, 2017 | parent | next [–]

What language would you choose? AFAIK there are almost no _actually_ secure languages. The ones that exist (e.g. Idris, Agda) are notoriously hard to use (although their Turing "incompleteness" is arguably a feature for smart contracts/distributed computations).

Daily LoL: Googling "secure language" brings up mostly results about Java.

	
	
DennisP on July 20, 2017 | root | parent | next [–]

Here's a master's thesis by a couple guys who made Idris compile to Ethereum bytecode.

https://publications.lib.chalmers.se/records/fulltext/234939...

	
	
mrkgnao on July 20, 2017 | root | parent | prev | next [–]

Idris isn't difficult to use for things that don't require tons of library support (since there aren't many libraries to speak of yet), unlike Agda, which is far from a programming language in the usual sense (it's a proof assistant, and you're only really supposed to typecheck stuff with it).

You can just skip messing with the termination checker (just don't mark your functions total) in Idris and write Haskell-ish code. The occasional rough edge aside, it's not hard to write the kind of programs we're talking about here: and if you want to prevent DDoS-like infinite looping, put the "total" annotations back in.

	
	
tonyg on July 20, 2017 | root | parent | prev | next [–]

You might be interested in W7, an object-capability Scheme variant: http://mumble.net/~jar/pubs/secureos/secureos.html

Starting from something like the lambda-calculus seems more sensible than starting from something like Javascript.

	
	
naasking on July 24, 2017 | root | parent | prev | next [–]

> What language would you choose?

The E language has been a vehicle for smart contract research for decades. Joe-E is a more recent incarnation of many of the same ideas, that's a secure subset of Java.

	
	
pmlnr on July 20, 2017 | root | parent | prev | next [–]

Ada.

	
	
blunte on July 20, 2017 | parent | prev | next [–]

Naivety, youth.

	
	
asaka on July 20, 2017 | root | parent | next [–]

Too young, too simple, sometimes naive.

	
	
creshal on July 20, 2017 | root | parent | next [–]

Now who the hell gave them any money for that?

	
	
mtgx on July 20, 2017 | root | parent | next [–]

Investors who have FOMO (fear of missing out).

	
	
eternalban on July 20, 2017 | root | parent | next [–]

+No Clue.

	
	
mannykannot on July 20, 2017 | parent | prev | next [–]

When I took a look at it, two or three years ago, I think there were three different guys in the organization each busy with his own idea of a high-level language to put on top of the VM, each inspired by a different GP language. That signaled a certain lack of focus.

	
	
lugg on July 20, 2017 | parent | prev | next [–]

Hubris.

	
	
RexetBlell on July 20, 2017 | parent | prev | next [–]

This new language for Ethereum by Vitalik is exactly what you said: https://github.com/ethereum/viper

	
	
seagreen on July 20, 2017 | root | parent | next [–]

Viper's inspired by Python, which (for all the nice things we can say about it) is a _terrible_ place to go for inspiration for this kind of task.

	
	
tom_mellior on July 21, 2017 | root | parent | next [–]

Viper is only inspired by Python's syntax. It's not a highly reflective completely dynamic language like Python. (But this also means that it is not a "DSL inside of an existing language", contrary to what your parent claimed.)

	
	
ikken on July 20, 2017 | parent | prev | next [–]

This is because running code on Ethereum VM and storing data is hugely expensive (rightly so, as it's being done on all nodes in the world). Therefore Solidity will try to compile into a VM code that uses the least numbers of cheapest instructions and pack data into as small memory package as possible.

	
	
simias on July 20, 2017 | root | parent | next [–]

Most of the issues pointed out by int_19h would be handled at compile time during static analysis and wouldn't change much to the generated bytecode. I'm talking about strong typing, immutability by default, less error-prone syntax, tail calls, evaluation order etc...

Even replacing 256bit ints with arbitrary precision "bigints" wouldn't add too much of a cost if it's a native type of the underlying VM (as it should be for such an application IMO). It might even reduce code size by removing overflow tests.

	
	
int_19h on July 20, 2017 | root | parent | next [–]

It's about costing the operations reliably.

But I would still expect arithmetic to be overflow-checked by default, as in e.g. VB.NET. This would mean that careless arithmetic on unvalidated inputs could still cause the contract to fail - but at least it would be a controllable failure, with all state changes being reversed, and hence not exploitable.

	
	
earlz on July 20, 2017 | root | parent | prev | next [–]

In the rationale document they explain that arbitrary length integers were too difficult to determine a reasonable gas cost around for math etc.

	
	
bigdubs on July 20, 2017 | root | parent | prev | next [–]

"Normal" and "secure" languages don't do this already?

	
	
pdexter on July 20, 2017 | prev | next [–]

Time for the Underhanded Solidity Contest? (http://www.underhanded-c.org/)

	
	
mannykannot on July 20, 2017 | parent | next [–]

It is kind of implicit, from the core principles of Ethereum, that there is a global 24/7 Underhanded Solidity contest going on, with valuable prizes to be won.

	
	
thinkmassive on July 20, 2017 | parent | prev | next [–]

Already happening: http://u.solidity.cc/

	
	
alper on July 20, 2017 | prev | next [–]

I am amazed/not amazed that they write financial applications in this language (and do not have the common sense to not do it).

	
	
h1d on July 21, 2017 | parent | next [–]

Everything about the blockchain is a test now, it's just the investors who mindlessly invest hoping for a quick 5x is making it bigger than they should be.

	
	
arjo1 on July 20, 2017 | prev | next [–]

Fixing the language is one step. It will still not prevent hax0rs from targeting the bytecode of the VM itself.

	
	
lmm on July 20, 2017 | parent | next [–]

Perfect is the enemy of good.

	
	
pmarreck on July 20, 2017 | root | parent | next [–]

If that BS was true, we should all just switch to PHP right now

	
	
lmm on July 20, 2017 | root | parent | next [–]

On the contrary. No other language is perfect, so if you can only think of perfect you would keep using PHP; it's only if you recognise the idea that a language can be better while still being imperfect that you can switch to a better language.

	
	
naasking on July 25, 2017 | parent | prev | next [–]

The EVM has been formalized in theorem provers [1], so they're already taking that threat seriously. One threat also mentioned in the above thread that is often overlooked are full abstraction failures, ie. exploitable mismatches between high level language constructs and the machine code to which it gets compiled.

[1] http://lambda-the-ultimate.org/node/5003#comment-94646

	
	
wslh on July 20, 2017 | prev | next [–]

And you are not saying anything about the VM itself which behaves differently in certain cases... Look at our last article on this specific topic: https://blog.coinfabrik.com/smart-contract-short-address-att...

	
	
int_19h on July 20, 2017 | parent | next [–]

The devs really need to understand that in the niche that they're targeting, unless they can be absolutely sure that some default is safe, there should be no default. Most of these issues stem from this same fundamental problem - they pick some default, or some implicit behavior, ostensibly for convenience, but with unanticipated undesirable side effects.

	
	
wslh on July 21, 2017 | root | parent | next [–]

I don't agree, you cannot expect to handle millions of dollars with those tools in the same way you don't write mission critical software in bash.

	
	
vazhifarer on July 20, 2017 | prev | next [–]

> Scoping rules are inherited from JS, meaning that you can declare variables inside blocks, but their scope is always the enclosing function

That's not still the case with `const` and `let` in Javascript, is it? Lexical scoping still exists, but Block scoping is default now in JS

	
	
MaxfordAndSons on July 20, 2017 | parent | next [–]

> Block scoping is default now in JS

The default best practice, you mean? Declaring with 'var' will still get you the same lexical scoping rules as always.

I believe OP meant 'inherited from pre-ES6 JS'

	
	
positivecomment on July 20, 2017 | root | parent | next [–]

I thought using "var" is deprecated... If there's anyone who has the chance to use let/const but still doesn't, I'd be very curious to learn about their reasoning

	
	
seangrogg on July 20, 2017 | root | parent | next [–]

Bad habits, arguably. Been using JS for years so occasionally instances where a more vigilant self would use "let" still get "var" instead. I think part of this is because of issues with previous versions of v8/node where using let at the global level or without 'use strict' threw errors.

That being said, "const" is already hardwired.

	
	
vazhifarer on July 20, 2017 | root | parent | prev | next [–]

I know right. Principle of least Privilege. Simple.

	
	
hippich on July 20, 2017 | root | parent | prev | next [–]

to get variable defined in function scope? :)

	
	
masklinn on July 21, 2017 | prev | next [–]

> Order of evaluation is not defined for expressions. This in a language that has value-returning mutating operators like ++!

I don't think that one even matters, that the language has side-effecting expressions (through impure functions) is enough to make an undefined OOE problematic.

> Integers are fixed-size and wrap around, so it's possible to have overflow and underflow bugs. Granted, with 256 bits of precision by default that's harder to do than usual... but still pretty easy if you e.g. do arithmetic on two inputs.

That ignores the problematically trivial "type inference" of `var`: type is decided based on the smallest type which can hold the literal. So any implicitly typed variable initialised to 0 is an 8-bit unsigned integer.

	
	
phreeza on July 21, 2017 | prev | next [–]

How bound are people to using Solidity? How hard would it be to come up with a competing, more verifiable langauge specification? I don't know much about Ethereum, but I assume the Solidity gets compiled to some sort of bytecode to run on the VM?

	
	
5chdn on July 22, 2017 | parent | next [–]

Not at all. Solidity is just the most widely used language for the EVM. There are also Mutan (dead), Serpent (dying), LLL (recently revived), and Viper (being drafted).

	
	
peternicky on July 21, 2017 | parent | prev | next [–]

This was exactly the first thought that came to mind when I first read the Solidity docs.

	
	
z3t4 on July 20, 2017 | prev | next [–]

Your opinion about javascript and closures in particular makes me question the rest of your comment. Closures in javascript makes it easy to write async code.

	
	
int_19h on July 21, 2017 | parent | next [–]

What I said about the intersection of closures and variable scoping in JS is not an opinion, it's an objective fact. All too often, people try to do something like declare a var inside a loop, and spin off a closure that captures that var. The reasonable assumption is that the scope of the variable is the body of the loop, and thus every iteration gets its own new copy, and all closures are independent.

In reality, all closures get the same variable, and if it is the loop counter or derived from it, and closures are executed later, then they will all see the same value - the one that said variable had after the last iteration. It's even worse if closures try to write to it.

This is something that is peculiar to JS. In most other languages, there's no similar issue, because either local variables can't be declared inside loops (or, indeed, at all, treating assignment as implicit declaration), as in Python or Ruby; or if they can, then their scope is what you'd expect it to be, as in C#, Java etc.

Writing async code using closures as continuations is not unique to JS, either. All mainstream languages support this now.

	
	
z3t4 on July 22, 2017 | root | parent | next [–]

What you describe is the moment someone would usually go from PITA to Ninja! From programming sequential to async using functions. The WTF code:

  for(var i=0; i < textures.length; i++) {
    textures[i].onload = function (i) {
      pattern[i] = ctx.createPattern(textures[i], 'repeat');
    }
  }

The enlightenment:

  for(var i=0; i < textures.length; i++) {
    createPattern(i);
  }

  function createPattern(i) {
    textures[i].onload = function() {
      pattern[i] = ctx.createPattern(textures[i], 'repeat');
    }
  }

In JavaScript you can now however replace var with let and it will be block scoped and the WTF code will work. It's sad though as people learning JavaScript now will not get the beauty of it and will be stuck in callback hell. Function scope in JavaScript pushes you in the right direction. It's like instead of teaching people how to fly, we give them roads so they can walk, like they are used to.

	
	
int_19h on July 25, 2017 | root | parent | next [–]

It's way more than just async callbacks. For example, you may be using a lazy sequence abstraction, with high-order functions like the usual map/filter/reduce, but with a deferred implementation (i.e. they're only applied, and functions passed as arguments are evaluated, when the next item is read from the sequence). If you create such deferred sequences inside a loop, they will again capture the mutated counter.

	
	
ojr on July 20, 2017 | prev [–]

It is interesting to see but I think Solidity is the first software language/framework that people with no experience writing it or running it, comment and write about it on hackernews as if they did.

A common criticism is the way for loops and arrays are implemented are broken? Well actually they should rarely be used in contracts, its not like you can send ether/btc to an array recipients or in a for loop.

You do not need a complete language like F# and C++ to write contracts, The hacked contract should have had a simple modifier describing only the owner can run this command.

Once again the type system doesn't need to be perfect, if your code on the blockchain is broken you pay a transaction fee and the contract doesn't execute it.

For a language that was made less than 3 years ago, it amazing to see the progression. It will be a language that people hire for in the near future (already happening). Companies are notorious looking for 5 years experience.

Its hard to read the "I told you so replies", when the price of ETH has remained relatively stable during the news

	
	
knocte on July 20, 2017 | parent | next [–]

> It is interesting to see but I think Solidity is the first software language/framework that people with no experience writing it or running it, comment and write about it on hackernews as if they did.

It's interesting to see how the first virtualmachine specification for the first financial smart contract platform has overlooked all the pitfalls of the language-design industry and it doesn't look that a redesign is going to be addressed very soon because of the difficulty of the redeployment (as naturally backwards compatibility must be the first concern).

> Its hard to read the "I told you so replies", when the price of ETH has remained relatively stable during the news

Haha, so if the price is stable, this means that Solidity is not embarrassing? I'm sorry but this just means that people only care about security when their funds are the ones affected. Not about "potential" security problems or "design flaws" which sound too technical to them.

	
	
ojr on July 20, 2017 | root | parent | next [–]

The language was not made from "scratch" but a progression from bitcoin scripts, Ethereum is the like to blockchain dev like Unity is to gamedev, it lowers the barrier to entry, it has a lot of the similar apis and functions as bitcoin, I don't really consider this a bad thing

	
	
int_19h on July 20, 2017 | root | parent | next [–]

Do we really want to lower barrier to entry for writing code that can misdirect $30 million dollars just like that?

By the way, I went and looked at the offending contract. The pull request that introduced the line with the bug added 2228 new lines of code while removing 918 lines. In fact, the diff is so large that GitHub doesn't even show parts of it by default (including the part with the bug). The pull request was reviewed by a single other person, and committed on the same day it was submitted.

I don't think I'm comfortable with the barrier being that low, at all.

	
	
ojr on July 24, 2017 | root | parent | next [–]

it was missing one keyword that was the parity bug not in the 2228 lines, and the DAO hack had a line that was place 3 lines too early, a monetary system created 3 years ago and is highly demanded might have some growing pains

	
	
peternicky on July 21, 2017 | root | parent | prev | next [–]

Can you please link to this pull request?

	
	
int_19h on July 22, 2017 | root | parent | next [–]

https://github.com/paritytech/parity/blame/4c32177ef3f4521c6...

	
	
int_19h on July 20, 2017 | parent | prev | next [–]

> It is interesting to see but I think Solidity is the first software language/framework that people with no experience writing it or running it, comment and write about it on hackernews as if they did.

Not at all. Deconstruction of PL design is a popular pastime here.

> A common criticism is the way for loops and arrays are implemented are broken? Well actually they should rarely be used in contracts

If they cannot be used safely, they shouldn't be in a language.

If they can be used safely, but it requires caution and is to be rarely done, then the syntax should reflect that by not making it so easy to use them.

> Once again the type system doesn't need to be perfect, if your code on the blockchain is broken you pay a transaction fee and the contract doesn't execute it.

The type system enforces invariants that go beyond code being broken in a sense that it doesn't run. Sometimes broken code does run, with catastrophic consequences - remember Mars lander crash? That was one example of an error that could be prevented by a sufficiently advanced type system - in F#, for example, types can reflect units of measurement, and the language will plainly not allow you to do nonsensical things like add together feet and meters, without explicitly converting one way or the other.

By its very nature, smart contracts on a blockchain that deals with money have a huge potential for this sort of thing - a contract that doesn't trigger an error, but which silently behaves differently from what was intended by the author. The consequences, as we can see here, can be staggering, as in millions of dollars in damages.

It follows that the language that is used to write them should use every known mechanism to discover such errors early. This includes strong typing, immutability, explicitness, design by contract etc.

> For a language that was made less than 3 years ago

The language was not made in a vacuum 3 years ago. Other languages preceded it, and provided valuable lessons in PL design. Why these lessons weren't heeded is a reasonable question to ask.

	
	
rnhmjoj on July 20, 2017 | parent | prev [–]

> for loops and arrays are implemented are broken? Well actually they should rarely be used

A great motivation: it's always better to write half-assed implementations of rarely used features because they are rarely used.

> You do not need a complete language like F# and C++ to write contracts

Agreed. You need a small verifiable DSL, not an entire new language.

> Once again the type system doesn't need to be perfect

That's the spirit when designing a language!

>  it amazing to see the progression

Well... yeah, if you started with the goal of executing without crashing it can only improve over time. 
      
      
      
### NOTES 2


	
	
peoplewindow on July 3, 2017 | parent | context | favorite | on: Underhanded Solidity Coding Contest

Solidity has far worse problems than not being an advanced research language. Just being a sanely designed normal language would be a big step up. Solidity is so riddled with bizarre design errors it makes PHP 4 look like a work of genius.

A small sampling of the issues:

Everything is 256 bits wide, including the "byte" type. This means that whilst byte[] is valid syntax, it will take up 32x more space than you expect. Storage space is extremely limited in Solidity programs. You should use "bytes" instead which is an actual byte array. The native 256-bit wide primitive type is called "bytes32" but the actual 8-bit wide byte type is called "int8".

Strings. What can we say about this. There is a string type. It is useless. There is no support for string manipulation at all. String concatenation must be done by hand after casting to a byte array. Basics like indexOf() must also be written by hand or implementations copied into your program. To even learn the length of a string you must cast it to a byte array, but see above. In some versions of the Solidity compiler passing an empty string to a function would cause all arguments after that string to be silently corrupted.

There is no garbage collector. Dead allocations are never reclaimed, despite the scarcity of available memory space. There is also no manual memory management.

Solidity looks superficially like an object oriented language. There is a "this" keyword. However there are actually security-critical differences between "this.setX()" and "setX()" that can cause wrong results: https://github.com/ethereum/solidity/issues/583

Numbers. Despite being intended for financial applications like insurance, floating point is not supported. Integer operations can overflow, despite the underlying operation being interpreted and not implemented in hardware. There is no way to do overflow-checked operations: you need constructs like "require((balanceOf[_to] + _value) >= balanceOf[_to]);"

You can return statically sized arrays from functions, but not variably sized arrays.

For loops are completely broken. Solidity is meant to look like JavaScript but the literal 0 type-infers to byte, not int. Therefore "for (var i = 0; i < a.length; i ++) { a[i] = i; }" will enter an infinite loop if a[] is longer than 255 elements, because it will wrap around back to zero. This is despite the underlying VM using 256 bits to store this byte. You are just supposed to know this and write "uint" instead of "var".

Arrays. Array access syntax looks like C or Java, but array declaration syntax is written backwards: int8[][5] creates 5 dynamic arrays of bytes. Dynamically sized arrays work, in theory, but you cannot create multi-dimensional dynamic arrays. Because "string" is a byte array, that means "string[]" does not work.

The compiler is riddled with mis-compilation bugs, many of them security critical. The documentation helpfully includes a list of these bugs .... in JSON. The actual contents of the JSON is of course just strings meant to be read by humans. Here are some summaries of miscompile bugs:

In some situations, the optimizer replaces certain numbers in the code with routines that compute different numbers

Types shorter than 32 bytes are packed together into the same 32 byte storage slot, but storage writes always write 32 bytes. For some types, the higher order bytes were not cleaned properly, which made it sometimes possible to overwrite a variable in storage when writing to another one.

Dynamic allocation of an empty memory array caused an infinite loop and thus an exception

Access to array elements for arrays of types with less than 32 bytes did not correctly clean the higher order bits, causing corruption in other array elements.

As you can see the decision to build a virtual machine with that is natively 256-bit wide led to a huge number of bugs whereby reads or writes randomly corrupt memory.

Solidity/EVM is by far the worst programming environment I have ever encountered. It would be impossible to write even toy programs correctly in this language, yet it is literally called "Solidity" and used to program a financial system that manages hundreds of millions of dollars.


	
	
floatboth on July 3, 2017 | next [–]

> Despite being intended for financial applications like insurance, floating point is not supported

That's kind of a feature. Sure you can use decimal floating point (but never, NEVER use the common binary float for money), but storing integers of the minimum currency unit (e.g. cents) (typically wrapped in a Money class in OO languages) is also a good option.

	
	
taneq on July 4, 2017 | parent | next [–]

Nitpick: Most finanical packages work on 1/100ths of a cent, not on cents. Otherwise yes, everything money-related should use fixed point and be really careful about over/underflow.

Although one fairly well-known package, produced by a place I once worked briefly, which (when I worked there) internally used doubles for all money values, wrapped in a class that re-rounded the results every so often. No, really.

	
	
peoplewindow on July 3, 2017 | parent | prev | next [–]

You don't want to use floating point numbers to represent monetary amounts, however financial applications often work with numbers that are not money. Consider risk modelling.

	
	
spopejoy on July 3, 2017 | root | parent | next [–]

Do you think this really belongs on a blockchain, as a transactional environment? There's a notion that things like greeks and other non-linear inputs are best fed as inputs/oracles, for a number of reasons: 1) avoiding stochastic stuff on-chain 2) assurance, so you know what your inputs were later 3) impracticality of all that computation on-chain 4) dependence on market data. Of course there are simple things like imputing an option price from the stock with just delta and gamma, but a fixed-point decimal here wouldn't really hurt you; basic calculations like payment schedules would seem to benefit from fixed-point. But mainly, blockchains would seem to represent transactions and workflows primarily; analytics seem ill-suited for the high-assurance, database-write-heavy environment.

	
	
peoplewindow on July 3, 2017 | root | parent | next [–]

My knowledge of Solidity comes from reading the docs. It doesn't seem to support fixed point arithmetic either. The phrase "fixed point" appears in the ABI spec but nowhere else, shrug. Maybe half implemented? I guess you can implement it yourself as it does support bit shifts, assuming they aren't buggy too.

I pass no judgement on what belongs on Ethereum. I know from their website that they advertise it as a platform for general app programming and even implementing entire autonomous businesses. It clearly cannot support these things.

	
	
floatboth on July 3, 2017 | root | parent | next [–]

The Ethereum VM is not for general app programming. It's really not your typical environment. EVM contracts get executed on every network node, and it must return the same results everywhere.

	
	
SolarNet on July 4, 2017 | root | parent | next [–]

> it must return the same results everywhere.

We solved that for floats like 10 years ago. Let alone the fact there are better formats, like posits, or fixed point numbers, that also solve this problem very easily.

	
	
eric_bullington on July 3, 2017 | prev | next [–]

I agree with many of your criticisms (and have other ones for Solidity as well), but the lack of floating point is absolutely a feature, not a bug. There's no reason for floating point in a contract language. Floats should never be used for monetary values or counts, and you're not going to be doing numerics on the blockchain.

	
	
jothezero on July 16, 2017 | prev [–]

Technology will evolve.

Good points.

	
	
fakerobotgamer on July 22, 2017 | parent [–]

or "hey maybe we shouldn't have memory corruption errors and 'optimizations' that randomly change static values to god knows what" will be met with a bunch of cryptocurrency koolaid-chugging mouthbreathers screeching that these are features and not a bug and now we have Etherium "It's In The Contract That I Can Screw You Over, Get Owned Nerd" Classic, Etherium Floating Point Is A Bug And Not A Feature Edition, and Etherium I'm Excited To See What They Fucked Up And Will Have To Fork Next

<br>
