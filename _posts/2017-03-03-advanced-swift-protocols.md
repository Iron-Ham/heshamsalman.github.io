When Dave Abrahams introduced protocol-oriented programming in a [renowned WWDC talk](https://developer.apple.com/videos/play/wwdc2015/408/), he introduced himself as _professor of blowing your mind_.

Protocol oriented programming is certainly mind-blowing, and you can do amazing things with it — but it certainly has its limits in the current version of Swift (3.0.2).

For the uninitiated, protocol oriented programming is a technique that favors focusing on the behaviors of an object and moving those behaviors to a reusable protocol.

## Our Example Project: Animalia

If we were to write an animal cataloguing system for a zoo, we’d want to approach it thinking of the generic properties and behaviors of Animals. Instead of beginning with a base class and beginning an inheritance chain, we’d start with a protocol.

{% gist heshamsalman/7d27895df0cafe744dd52b08e50cd976 %}

Since protocol types are first class citizens in Swift, we can refer to `Animal` as if it were a base class. Note that the `WaterBreathable` protocol implies `Swimmable`, and that a chained protocol of this sort is fine.
At this point, we could go ahead and start defining many animals. But, before we start making our structs, we should being by defining a few general types of animals, such as birds and mammals.

At this point, we could go ahead and start defining many animals. But, before we start making our structs, we should begin by defining a few general types of animals, such as birds and mammals.

{% gist heshamsalman/2c6d86f38ac6ef11ab12c5dcce02d081 %}

> Extensions? Self? What’s happening up there?

Glad you asked, reader. Instead of forcing all these types to conform to `Animal` — we’re just implementing these default behaviors to these classes in the case that they are also `Animal`. By using this pattern, that we can continue defining default behaviors for `Animal`. This also means that our extensions impose a `Self` requirement, even though our protocol doesn’t.

> Okay, sure. But snakes don’t have two limbs.

[Yes they do.](https://en.wikipedia.org/wiki/Snake)

---

Using our new library of protocols, we can begin defining our animal types.

{% gist heshamsalman/754f468017e54dc97cf71c9a2b5bb2db %}

Wow, look at that! We don’t have to fill out a body for these structs because their behaviors are already implemented by their backing protocols.

## Associated Types

So after we take this all to production, we start notice that our cows and lions are dying out because we haven’t catalogued their dietary requirements. Whoops. Let’s fix that right now.

{% gist heshamsalman/b801efea22e66c49343769e38245a332 %}

We’ve defined a protocol, `Food`, and its companion types: `Meat`, and `Grass`. We’ve also updated our `Animal` object to eat a type of `Food`. But, with this implementation, we realize a problem: Cows don’t eat `Food`; they eat `Grass`. Similarly, lions don’t eat generic `Food`, they eat `Meat`.
In OOP languages, such as Java, we may do something like this in our implementations of `Animal`:

{% gist heshamsalman/15d92f05ded43a48614bfdfd03ec6341 %}

This seems inadequate — not only because it doesn’t represent the relationship between animals and food, but also because we are expecting the users of these classes to know the foods appropriate for each animal.

Swift allows an alternative approach, involving the oft-maligned `associatedtype` keyword. An associated type is a placeholder type within a protocol. Implementing associated types allow you to use a “generic” type in your protocol but be warned, they are not quite generic protocols, nor do they come without drawbacks. When using a protocol with an associated type, you will lose the ability to use that protocol as a type. You’ll only be able to use it as a generic constraint. You’ll also lose the ability to use the protocol in a heterogeneous collection; you can no longer have an array of `Animal` objects, but you’ll be able to have an array of `Lion` objects.

Let’s explore this solution:

First, we’ll update our Animal protocol to include an associated type:

{% gist heshamsalman/0f51705cb37be902f2bdee7e0c37f40e %}

Let’s assume that instead of having _just_ `Cow` and `Lion`, that we had _hundreds_ of `Animal`s. Manually implementing an `eat` function on possibly hundreds of types is cumbersome, so let’s use protocols to circumvent that.

{% gist heshamsalman/fbd1f52e387ffd25b1c7e42df85aa08b %}

At this point, we just have to decide what kinds of eaters our existing animals are — A much easier task than manually implementing a function across multiple types!

## Typealiases

After defining several more animals, such as `Eagle`, `Tiger`, `Hawk`, `Gorilla`, and `Monkey`, we may find that we’re using a lot of the same patterns. We can chain protocols together in type aliases to shortcut the process of declaring new animals.

{% gist heshamsalman/3271a95b228454cbde810b8dfdcf890c %}

Using a protocol-first methodology can be very powerful.

But, as mentioned earlier, `associatedtype` can have some far reaching implications.

## Avoiding the Pitfalls of Associated Types

![wwdc_screen](https://cdn-images-1.medium.com/max/1600/1*ykUDuOrDL1sf2kqETBPN6g.png)


The table above quickly outlines the difference between protocols with and without self requirements. The most potentially problematic of these is the second point: ‘Think homogenous’.

This point implies that we cannot have arrays of the generic `Animal` type. The reason why is quite simple: `Animal`, as a protocol is not just defined by being an animal, but also by its association; there is no such thing as an `Animal`, but there is an `Animal<Grass>`.

You can take multiple routes to get around this problem.

## Generic Functions

{% gist heshamsalman/07d9b4f066040baa7912598d5cd8d09e %}

Using generic functions in this manner, we can have the same API for the different types so that the end user doesn’t feel impacted by associated types. This is a common pattern throughout the Swift standard library. It’s also a clearly repetitive and cumbersome pattern — although it is the most effective and generally applicable.

## Type Erasures

{% gist heshamsalman/d8d8c85b3436097a0e68cd79fa6aaa88 %}

Type erasures are a complex pattern that’s used all over the Swift standard library. They are, however, very limited. In this instance, the `AnyAnimal` wrapper allows you to wrap any types of animals together to have an array of them, but will no longer conform to its wrapped object’s protocols.

If you had an `Eagle`, `e`, and a wrapped eagle `w` of type `AnyAnimal<Eagle>`, it `canFly`. However, it is not `Flyable`. Not an ideal solution if your methods rely on prototypical conformance to function.

## Wait For The Next Major Swift Release

Swift 4 is likely to support covariance, which removes a lot of the limitations of protocols with an associated type. In fact, they’re planning on improving Swift support for Generics as a whole in order to avoid some of the workarounds used in the standard library.

To see the full code sample used in this project, check out the Animalia [GitHub repo](https://github.com/heshamsalman/Animalia).
