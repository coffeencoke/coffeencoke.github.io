# Code standards that matter

by James Duncan Davidson, medium.com

Most coding standards documents focus on things that apply in the context of one language: how to indent, how many spaces or tabs to use, and how long a method or function should be.

This is not that kind of document. Instead, it looks at higher level standards that apply when you craft code, no matter which language you use.

There is an important set of questions one should consider when writing code: How do you store your code? What do you do when you include other people’s code? How do you credit an idea you read about somewhere else? What’s the best way to include third party modules?

Let’s address these.

Use Source Control

Obvious, perhaps, but always use a source control system. Git, Mercurial, Subversion, or whatever else, it doesn’t matter which one you use. Just use one and make sure to keep your commit history from the start. When your tiny little project blooms into a company and somebody from the future needs to figure out what came from where and was written by who, the answer will be a quick query away.

Help future you and teammates out. Keep your commits small and focused when at all possible. While the command-line is the ultimate tool, consider using a GUI tool. GitHub for Mac is my favorite, and there’s a Windows version as well. These applications let you easily make focused commits of a few changes in a file rather than everything at once. This allows you to give fine grained documentation of your work. And, in some cases, a commit message is a better place to put an explanation of why you did something than an inline code comment.

Use straightforward tooling

Every language and development environment has its own way to do things. That’s ok. Use ‘em. If you’re working in multiple languages on multiple projects, don’t make things complicated by trying to use the same build tooling for all of them. For Android projects, use Android Studio and gradle. For Mac and iOS projects, use Xcode. For Scala projects, use sbt.

Commit and merge frequently

When you start coding as an individual developer, it’s easy to get in the habit of only committing when you’ve accomplished something big. Fight that temptation. In a team environment, it’s much better to commit and merge frequently. This accomplishes two things. First, it means that other people on your team can track your progress and know what you’re doing. Second, it reduces the chance that you and a colleague will make massive edits to the same functionality that conflict with each other.

In the same spirit, don’t stay out on a branch too long. The longer your changes are away from master, the more likely it is that you’ll get distracted and will lose focus.

Don’t copy and paste code

In this era of Stack Overflow, it’s really easy to search for the answer to a problem, find a code snippet, and then cut and paste it into your own codebase. Don’t. Just don’t.

That’s not to say don’t use Stack Overflow or search for a solution for a hard problem with Google. You should. It’s a valuable tool and so much better than what we had fifteen years ago. But use it for what it’s intended for: the exchange of ideas. A place to learn. Borrow the ideas, but not the expression of those ideas. Learn, then type your own code in you own way.

“But wait!” you might say. “content on Stack Overflow is Creative Commons licensed, only requires attribution, and people who post content agree that they have permission to post it!”

Bzzt. Thank you for playing.

There are at least two problems with this. First, Creative Commons licenses weren’t designed for software. Stack Overflow is an information resource, so while using a Creative Commons license makes sense for them, it doesn’t help you with your use of the code. Second, do you really believe that every contributor to Stack Overflow is posting their own original work? The fact that they post it there doesn’t indemnify you in case of infringement.

All that said, if you must cut and paste, then clearly and explicitly cite where your code came from and who provided it. That way, if you ever do have to sort out the provenance of your codebase, you can.

Encapsulate third-party code

What if you need to borrow a file or two (or three) from an open source project, maybe make a few edits, and include them with your code? Assuming that the code’s licensing permits usage, you should keep such code in isolated files, complete with their original license headers and copyright information.

Better yet, you should aim to include third-party code using the appropriate package or dependency system for your environment, such as using Maven, Bundler, or even just including a .jar or .so file with your project. If you need to make edits to a dependency — say to fix a bug or add a feature before submitting back upstream — fork the original repository, make your changes, and then include the updated dependency. Above all, keep the threads of who owns what clear.

GPL code considerations

While GNU Public License (GPL) software is called free software and the GNU movement has done an incredible amount of work that we all benefit from, free in this case may or may not match with your own definition of freedom. In particular, it comes with the condition that any software you compile and distribute it with must be licensed under the same terms.

This serves GNU well, but unless you are explicitly writing software for free distribution, are very careful about using any provided license exceptions, or will never ever distribute your software, then you’ll want to avoid GPL code. On the other had, if you’re explicitly distributing your own code under a GPL license, then you’ll want to avoid non-GPL code unless you’re really really smart about the way such licenses intersect.

If you think you can get away with sneaking some GPL code into a non-GPL codebase — or vice versa — and removing a license, think again. There are specialized code forensic tools that can compare your code with public codebases stored on GitHub, Stack Overflow, and any number of other locations. If you ever have your code audited for some reason, you’ll find out just how well they work.

Attribute liberally

If you use somebody else’s code, give a shout out even if the license you use it under doesn’t require it. Mention it in your app’s information dialog or in its README.txt file. If you always attribute, then you won’t have to remember when you’re required to and when you’re just being nice.

Test appropriately

Write tests. Practice Test Driven Development when it makes sense, which is often. Use Behavioral Driven Development if you can. Write more tests than you need while you’re working on a new problem and then remove the tests that are no longer useful when you’re through with a development cycle. The number and kind of tests needed to keep a system clean are different than the ones that are useful to build a system in the first place.

Don’t let a mandate to test everything slow you down when you’re just prototyping an idea and will throw away the code later anyway.

Use continuous integration

Keep your code in good, runnable condition at all times by using continuous integration tools, such as Travis CI, Jenkins, or Xcode Server. Fix problems as they occur. Strive to keep your codebase shippable at a moments notice and you’ll be able to respond to problems and ship fixes quickly.

Never check in secrets

SSL private keys, API access keys, passwords, user names. None of these should appear in your source code. These should be all included externally in some manner or another. For server apps, the twelve-factor app methodology of using environment variables for configuration is the right way to go. For client apps, a configuration file that you distribute among developers is the way to go.

However you handle secrets, don’t make the building of your app dependent on secrets being present. You shouldn’t need to set up a key or a code signing certificate to make a developer build and run tests. Aim to keep your codebase in a state where you could let anybody check it, build, and test it using default tooling.

Strive for Simplicity

Finally, don’t create overly cute code. Don’t try to impress your co-workers with how well you know the decorator singleton factory pattern. Don’t use fancy pants variable naming schemes. Don’t layer your abstractions to the point of ridculousness.

Do write clean, clear code that does what you say it does. And remember: the person struggling to figure out what a piece of code does twelve months from now might just be you.

Written as an informal set of coding standards for polyglot development at 6Wunderkinder — makers of Wunderlist — where we use C#, Clojure, Go, Haskell, Java, JavaScript, Objective-C, Ruby, Scala and more on a daily basis and care more about working well across languages than perfecting code spacing or line length standards for any single one.

Original Page: https://medium.com/@duncan/code-standards-that-matter-c4961473aad0?source=tw-4e4086094dd7-1430395454757

Shared from Pocket