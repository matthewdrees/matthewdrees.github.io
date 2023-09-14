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
20. Project 2 concepts prep. VStack, HStack, ZStack. Colors. Frames with min/maxWidth. Safe area. Materials. Some things (e.g. ultraThinMaterial, Color.primary/secondary) automatically adjusts for light/dark mode. Gradients: 3 types: linear, radial, angular. Buttons: action, role, styles. Images. Label auto-formats depending on context. Alert messages.
21. Project 2, did the project. Nice successive usage of new concepts. Looked through the [iPhone models compatible with iOS 16](https://support.apple.com/guide/iphone/supported-models-iphe3fa5df43/ios): smallest display is iPhone 8: 1334 x 750 pixels, largest is 14 Pro Max: 2796 x 1290 pixels. Can run the simulators side by side, the app looks the same in both!
22. Project 2 wrap up and challenge. Getting comfortable with @State, updating views, and alerts.
23. Project 3, how views and modifiers work. SwiftUI uses View structs instead of classes because they are simpler and faster. UIView class has hundreds of properties, whether you need them or not. Color.red is a swift View struct with only one property. At the moment there is a UIHostingController to bridge the old UIKit and SwiftUI but don't rely on that. Modifier order matters. SwiftUI uses the opaque modifier *some* View as the body property type, so we don't have to type out the long exact custom ModifiedContent<...> thing. Environment modifier (e.g. font) can be overridden by child views, regular modifiers (e.g. blur) do not. Breaking up complex views using views as properties, 3 ways: Group, Stack, @ViewBuilder. Custom views and view modifiers. Whew!
24. Project 3 review and challenges.
25. Milestone 2: Rock Paper Scissors app. Similar to Guess the Flag. Took me 1.5 hours.
26. Project 4, BetterRest prep. Stepper, DatePicker, Date ranges. Warning: working with dates is really hard. E.g. time zones, leap years/seconds, and 12 days missing from September 1752. Amazingly easy to work a Machine Learning model into iOS, in this case used a Tabular Regression model that automatically figures out which works best: Random Forest, Boosted Tree, Decision Tree, or Linear Regression.
27. Project 4, build the BetterRest app. More layout work, Core ML. The Core ML stuff is pretty cool.
28. Project 4, review and wrap up challenges.
29. Project 5, part 1. *List* most popular view type, for a scrolling view of data. (Equivalent to UITableView.) A form is a specialized type of List. Loading resources from app bundle. Some string manipulation.
30. Project 5, part 2. Build the app. onAppear and fatalError. Spellcheck using UITextChecker.
31. Project 5 challenges and review questions. Already forgetting some stuff and having to go back to previous projects. Good for learning reinforcement I suppose.
32. Implicit animations. Use the *.animation* modifier. Can put the modifier on a view or a binding, both are important but don't know why yet. Neat circle button effect with radiating fading ring in a circle overlay.
33. Explicit animations. Use *withAnimation* method. Multiple animations at the same time, the modifier order matters. Neat affect with Hello World letters animating with different delays. Showing/hiding views.
34. Project 6 wrap up and challenge with animations. I spent a couple of hours on this, but I went down a rabbit hole on some of the animations.
35. Challenge. App that does multiplication tables for 9 year olds. It took me about 3 hours to do the boring (form-based) version of the app. I have in mind a better version of the app, with a fun layout, colors, and animations, that I bet would take me ~20 hours. Not sure I want to actually commit that time to doing it though.
36. Project 7, moving right along! @State property wrapper works for monitoring structs because they are recreated on every property change. However, it doesn't work for classes, because the property is modified within the class instance and the instance (pointer) stays the same. ObservableObject protocol for classes that will have observable changes, with @Published property wrapper for fields whose changes should enforce UI updates. @StateObject or @ObservedObject property wrappers for class objects that are created or shared, respectively. Sheets and dismissing them with @Environment property wrapper. Deleting items from a list with onDelete and EditButton. UserDefaults, don't store more than ~500 KB to keep app launch fast. Yet another property wrapper: @AppStorage.
