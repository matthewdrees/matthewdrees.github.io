---
layout: post
title:  "100 Days of Swift"
category: finance
---

By Paul Hudson.

My notes from the [https://www.hackingwithswift.com/100/swiftui](100 Days of SwiftUI) course.

I like the pacing of this course. Paul is right when he says a little bit each day. I enjoyed the dog cameos!

1. Why swift, numbers, strings. Use let/var syntax to allow for constants (rather than extra "const" keyword in C++). The multi-line string """ formatting is strict.
2. Bools, string concatenation.
3. Arrays, dictionaries, sets, enums. Arrays are the main thing, like std::vector in C++, lists in Python, or Vec in Rust. Sets and Dictionaries are Hash Tables. Tuples are fixed size and type but the values are mutable. Enumerations are variants like in Rust (I think).
4. Swift is type safe. Type is inferred but can be specific with annotations.
5. Conditions. If, switch, and ternary. Ranges: ... and ..< for inclusive and noninclusive ranges. Can use as cases in switch statements.
6. Loops. Can break out of an outer loop by labelling the outer loop. Clever, in the past to do this I would put it in its own function.
7. Functions. Can have external argument name different than the internal parameter name. Whoa. Add underscore in function declaration to remove the calling argument label.
8. Default values and errors in functions. Most exceptions in swift have a "meaningful message" that can be shown to the user, using error.localizedDescription.
9. Closures. "Why does Swift love closures so much?" I had the same question! Trailing closure syntax if the last parameter to a function is a closure. Very clean. Very unique to Swift I think? I've never seen it before. Shorthand syntax: $0, $1, etc, Paul recommends only using for simple closures. Can have multiple trailing closures.
10. Structs. Computed properties with get/set (and newValue). Property Observers using didSet (and, more rarely, willSet, say for animations). Custom initializers.
11. Structs II. Access control with private, fileprivate, public, and private(set). Self vs self.
12. Classes. 5 differences with struct. Inheritance, copying, init, deinit. Classes don't use mutating keyword for methods.
13. Protocols and extensions. Opaque return types, using *some* keyword, returns a specific value of a protocol. Can use extensions to add capability, even to standard library stuff like Strings. Make compelling case for extensions rather than functions. Tip: put custom initializer in an extension to keep the auto-generated member-wise initializer. Protocol extensions (aptly named!), uses example of extension Collection { var isNotEmpty: Bool {...} }.
14. Optionals and unwrapping. Unwrapping with let/guard. Force unwrapping with *!*. Implicitly unwrapped don't need to be unwrapped to be used, are becoming more rare with move to SwiftUI. Nil coalescing provides a default value with *??* operator. Optional chaining using *?*. Optional *try?* for changing a throwing function to one that returns an optional, can force with *try!*. "Failable" initializers *init?* return an optional, with *nil* for failing. Typecasting is equivalent to C++ dynamic_cast, using *as?*.
15. Review. Skipped it. Nice work making the 50 "Swifty hidden terms" game!
16. Project 1, WeSplit. Set up new project. Limit of 10 children inside a parent (via declaration, can do as many as you want with a loop), e.g. Text fields in a Form. Safe area. "Views are a function of their state." @State property wrapper... allows structs to modify parameters without being in a mutable method, which I didn't notice before in the IOS app tutorial. *$* for 2 way binding.
17. Entering currency using Locale, decimalPad. Pickers. Segmented control. Hiding the keyboard (the regular keyboard has a *Done* button, but not decimalPad). Did the challenges.

Notes from [https://www.hackingwithswift.com/100](100 Days of Swift)

I ventured down this path a bit because I was using older sample code that used the old storyboard style of project.

16. Project 1, build a simple "Storm Viewer" app with fixed images. Interface Builder, storyboards, Swift. A very good intro to Interface Builder, though it reminds how much I dislike it and storyboards. I much prefer SwiftUI. I really like how Paul talks through the code, especially points where the code can fail with possible errors. 
17. Project 1, Detail view and image screens for "Storm Viewer" app. Using "aspectFill" caused the image to go up into the Navigation title area.
18. Project 1, recap. Quiz and challenges.
19. Challenge. Did a Length Conversion. Paul laments not putting a pure coding exercise earlier in 100 days of Swift, and resolved to get one as early as possible in this round. It took me 1.5 hours, and I was constantly looking up past exercises and the apple developer documentation. It reinforced many of the concepts.
20. Project 2 concepts prep. VStack, HStack, ZStack. Colors. Frames with min/maxWidth. Safe area. Materials. Some things (e.g. ultraThinMaterial, Color.primary/secondary) automatically adjusts for light/dark mode. Gradients: 3 types: linear, radial, angular. Buttons: action, role, styles. Images. Label auto-formats depending on context.
