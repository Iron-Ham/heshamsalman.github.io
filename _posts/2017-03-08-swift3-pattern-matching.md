Nearly two years ago, Natasha the Robot published a short blog about [pattern matching with the if case](https://www.natashatherobot.com/swift-2-pattern-matching-with-if-case/). While it provided an awesome intro into the `if case`, it was just that: an intro.

By the end of this blog, you’ll be able to match & filter circles around your non-`case` using colleagues.

## The Example

VTS is a commercial real estate technology company — and as such, we’re going to use a building themed example.

{% gist heshamsalman/49ecb9d74189c262dc89e89c4d724a7f %}

In the above example, we have a number of building types with associated values. Enums with associated values can really clean up closely related `class` objects — and keep you from making mistakes in your state machines.

{% gist heshamsalman/c68ffb2c977442e73c70f4b43cf9f9f2 %}

Above is effectively the equivalent of Natasha’s example — simple and to the point. It allows us to match enumerations based on a single case, and create a localized variable of the field we’re looking to work with. It allows us to switch on a single case of the enumeration without using a verbose `switch` with `default`. We can also have conditional matching based on internal fields:

{% gist heshamsalman/975ece9fca557d4f0e22b210d7453125 %}

Because we can use `if case let`, we can also use `guard case let` in a similar way. But, most importantly, we can combine this with a `for` loop:

{% gist heshamsalman/8923f3a2130d58b3265ae070550c3992 %}

Combining it with a `for` loop in this way allows us to filter and match over a collection. This lets us perform powerful filtering techniques in an easy-to-read fashion.

## Further Reading

[Match Me If You Can](https://appventure.me/2015/08/20/swift-pattern-matching-in-detail/), by AppVenture
[Pattern Match Operator](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Patterns.html), Swift Documentation
[Swift Pattern Matching](https://oleb.net/blog/2015/09/swift-pattern-matching/), Ole Begemann
