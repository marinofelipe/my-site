---
layout: single
title: 'Swift tip: Well-defined Bundles'
date: 2020-05-20 11:00:00 +0100
categories:
  - Tips
  - Swift
tags:
  - modular
  - bundle
  - extensions
---

When working on Apple's ecosystem we often need to interact with bundled resources. `Storyboard`s (😫), `XIB/NIB`s, `UIImage`s, `LocalizedString`s, `JSON` files (used for testing fixtures for example), and so on.
<br>

Nowadays apps tend to be quite `modular` which means working with `maaany bundles`. Let's see how we can mimic Apple's `Bundle.main` static property by creating `Swift.Bundle extensions` for each module, which helps in achieving a `standardized, reusable, and comprehensible` API.

```swift
// Let's say we have a Profile module in our app...
//
// Bundle+Profile.swift 

private class ProfileBundleReference {}

extension Bundle {
    static let profile = Bundle(for: ProfileBundleReference.self)
}
```

On the `call site`, it gets `straightforward` and easy to `reason about`.

```swift
// Localized string
let localizedString = LocalizedString("my_localized_string", bundle: .profile, comment: "")

// Image asset
let profileImage = UIImage(named: "icn_profile", bundle: .profile, compatibleWith: nil)
```

Don't forget that the same applies to `test targets or test frameworks`.
```swift
let jsonFixture = JSONLoader.load(fileNamed: "success", bundle: .profileTests)
```

### References: 
* Apple Developer: [class Foundation.Bundle](https://developer.apple.com/documentation/foundation/bundle)
