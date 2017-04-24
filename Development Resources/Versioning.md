# Versioning

Versioning Software

Versioning can be a random number or can be something that makes your release process fun.  The version format that I like to use, and have found works best, is Major.Minor.Patch (ie. 1.3.2).

Major

Significant enhancement, addition, or update that breaks backward compatibility with the previous major release.

Minor

Smaller enhancement, addition, or update that does not break backward compatibility with the current major release.

Patch

Bug fix.


There are many reasons versioning is important, but just to name a few that have been real for me in the past are: Planning, and API's for mobile applications.

A version number is a contract.  A contract that states, you, the client, using me, the library, can upgrade me as much as you want and I guarantee that I will not break your code as long as you stay within the same major release number.  That's as simple as it gets.

Let's see an example perhaps?

"Awesome API" is at version 1.0.0 and the "Great Mobile App" decides to use the "Awesome API".  Meanwhile, the developers of "Awesome API" are hard at work making it even more awesome.  They're receiving bug reports, enhancement requests, feature suggestions and are planning satisfying all of their customers.  There are a few rules that we must understand:

1. Bugs must be fixed as soon as possible
2. Enhancements should be released as soon as they are "feature" complete and shippable
3. Some enhancements will break the current API.

Major.Minor.Patch allows you to satisfy all three of those points.

Planning

While planning out what tasks, which I will refer to as stories, you and your team will be developing, often it gets a bit hairy.  By hairy, I mean, you have priorities, but how good are those priorities?  In the end it just seems a bit like eating an early breakfast.  And no, you're not really supposed to know what I mean by that, it's just the only way I can explain the feeling.

When you have your stories categorized over a the 3 versioning types by creating milestones for each version type.  Let's say that you just released your application! Yay, good day, you are at 1.0.0.  As you plan for the next coming iterations of work, you will end up coming up with a pretty decent amount of stories.  The problem is that the developer sees bugs and knows that they NEED fixed, but the business team who mostly drive the priorities of the project think they can just move on to newer and better features without taking head to the bugs that have popped up, because there are no bugs right?  Right.

So, by separating your stories into feature and bug, you now are able to clearly see how many bugs there are, how many are really important and you can come to an agreeable amount of bugs that need to be done.  It's easier to do a balanced approach.

Current: 1.0.0
Milestones: 1.0.1, 1.1.0, 2.0.0

As you write issues, place them in these milestones.  Is this a bug? Is this an enhancement or addition?  Will this break the current API?  

API's for Clients

Any client using your API needs to know if they can upgrade safely in case there are some bugs that have been fixed.  Providing this version format satisfies this concern.

Inspiration
experience
http://rubyrogues.com/037-rr-versioning-and-releases/