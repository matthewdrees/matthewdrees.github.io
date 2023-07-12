---
layout: post
title:  "100 Days of Swift"
category: finance
---

By Paul Hudson.

Paul recommends the [https://www.hackingwithswift.com/100/swiftui](100 Days of SwiftUI) course over the [https://www.hackingwithswift.com/100](100 Days of Swift) course, but I've run into limitations of SwiftUI and find myself needing to understand the UIKit & Interface Builder stuff.

I like the pacing of this course. Paul is right when he says a little bit each day.

1. 10 mins. Skimmed it. Swift is type safe. Type is inferred but can be specific with annotations. Use let/var syntax to allow for constants (rather than extra "const" keyword in C++). The multi-line string """ formatting is strict.
2. Arrays are the main thing. Like Vectors in C++, Lists in Python, or Vec in Rust. Sets and Dictionaries are Hash Tables. Tuples are fixed size and type but the values are mutable. Enumerations are variants like in Rust (I think).
3. Operators and conditions. The usual stuff. Except for range operators: ... and ..< for inclusive and noninclusive ranges. Can use as cases in switch statements.
4. Loops. Can break out of an outer loop by labelling the outer loop. Clever, in the past to do this I would put it in its own function.
6. Closures. "Why does Swift love closures so much?" I had the same question! Trailing closure syntax if the last parameter to a function is a closure. Very clean. Very unique to Swift I think? I've never seen it before.
16. Project 1, build a simple "Storm Viewer" app with fixed images. Interface Builder, storyboards, Swift. A very good intro to Interface Builder, though it reminds how much I dislike it and storyboards. I much prefer SwiftUI. I really like how Paul talks through the code, especially points where the code can fail with possible errors. 
17. Project 1, Detail view and image screens for "Storm Viewer" app. Using "aspectFill" caused the image to go up into the Navigation title area.
18. Project 1, recap. Quiz and challenges.
