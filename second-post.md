---
title: Will you spend 4 days to save 4 kb?
slug: will-you-spend-4-days-to-save-4kb
tags: engineering, Frontend Development
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1609226246059/RfZ24rck3.webp
domain: hng.hashnode.dev
---

Consider you got a new use case for a new UI component for your product. And being a wise developer the first thing you do is search for a library in open source. You got an interesting project which serves your purpose.

Then being wise you go to  [bundlephobia](https://bundlephobia.com/) to check the size. You realize the size seems bigger for your use case. You feel the component based on your case can be implemented within 2kb, while the library is around 6kb.

Your inner developer kicks in, and an urge to write things from scratch takes over.

You may make the following arguments with your mind to not use that 6kb library.
- Your use case can be implemented within 4 days, and you can save 4kb for all users. Why not do that?
- As the component will be home built, it will be easier to enhance it in the future for a custom use case.
- The component will follow a similar convention for APIs if it is a home build
- You will learn in the process of implementing it.

## Are this argument complete?

While all the above arguments are correct. But, are those arguments complete? There are other factors and questions you should be asking to make that decision.

### Is it actually 4 days?
- You will have to write the code for it.
- You will have to make it accessible.
- You will have to write a unit test for it.
- You will have to write documentation for that control.

Yes, the scope just got increased.

### What about the recurring cost of maintenance?

Remember the cost of maintenance is a big part, it's even higher than the implementation itself.

There are lot many factors that can lead to maintenance costs.

- Bugs
- Enhancement and new use case
- Friction for other people updating the code
- Code refactors (You will always realize you can do something better in the existing system)
 
A decision based on the initial cost of development not considering the maintenance cost usually haunts you.

### Do you understand the use cases completely?

I have realized it's pretty hard to know the scope from the start. Even if you are an experienced person I can bet, you don't know it all. 

It's pretty hard to know how the dev will be consuming your component or what all corner cases will come until it's actually out.

Open-source libraries gets an upper hand for this as they are battle-tested by many developers and many users on different products.  And hence they are more stable than the custom solution.

## But building a custom solution also has its perks.

By now you might lean toward using a third-party library. But wait, this article is not about using the third-party library or not. The motivation is to make you thoughtful while making this decision.

Now let's come back to some perks of the other side as well. 

### Reduced complexity

Third-party libraries need to support a large set of use cases (developers). While they are battle-tested on corner cases. They also come with a bloat of APIs which you might not need at all.

If you build the component yourself you can be more thoughtful about what are the things you need. And the API just to support that. 

Note this point can also become a pain point in the future (because you don't know it all). But it's okay to not know it all. 

*Optimizing a lot for the future may hamper your present.*

### Consistency

Different third-party libraries can have different API and different design which doesn't align with your organization's design and conventions. 

And you will have to patch those libraries to match the design. You might also have to write wrapper components that map the consistent API to an inconsistent library.

With a homegrown solution, you can build things in a consistent way.

### Control

While third-party lib might be covering a lot of many use cases, you may end up in a situation where it doesn't. And when that happens, incorporating your use case on third-party lib is really hard. You might have to create a fork of the library.
Probably you can propose the changes upstream. But maybe the use case is unique to your org and might not be the priority for the library to add it.

There will always be some friction on updating things inside a third-party library.

With a custom solution, you get more control over how you want to mold it in the future. Note the friction can be on the homegrown solution as well if another person is updating it. But usually, it's less compared to third-party lib as the code might follow similar conventions across the places.

### Performance

As a third-party library does a lot, it might not be the most performant option. The size will be bigger, the logical complexity might be high.

A well-built custom solution usually serves specific use cases and they end to be smaller and more performant.

So we definitely get some benefits of custom solutions at the cost of maintenance and stability.  

------------------------------------

Few more things you should keep in mind while making the decision.

### What stage your product/org is in?

In the early stage, you want things out as soon as possible. For the start, you need a working product that solves a problem, maybe not in the most performant way or with a consistent design. At this stage, you should optimize for speed of shipping rather than a perfect system. So use things which are already available. 

Once you start getting a good user base, the impact of small improvement multiplies. In such times every single KB, every single user experience matters and if that requires building a custom solution, don't back out.

### Will the user notice that 4kb save?

In the end perceived experience matters, if a user is not able to notice the 4kb optimization, is it really worth it?
For example
- The library probably can be loaded in the background without even the user noticing it.
- The library might not be required on the critical rendering path.
- The library probably can be prefetched.

*Think about users, not about KBs 🙂. But think about all users and not just a few users with the best system capabilities. *

### Wrapper component for third party UI component.
You can mitigate some of the risks with third party component (like consistency and constraints) by writing a wrapper component over it.

In fact, you should prefer to write wrapper components specifically for UI components. With the third party UI components, it's easy to get repeated or inconsistent customizations. Plus your application becomes tightly coupled with that library which is very hard to update in the future (If you want to).

With wrapper components
- All the customizations can be done inside the wrapper.
- The API of the wrapper can be consistent with the org conventions.

This way the consumer of the wrapper doesn't know anything about the underlying library and uses the consistent wrapper. And the underlying library can be updated or replaced without any changes on the consumers of that component.

An ending note:
> 
Software Engineering is all about tradeoffs. A good engineer is one who can make the right tradeoff at the right time with a plan to mitigate the risk of that tradeoff.
