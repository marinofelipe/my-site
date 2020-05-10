---
layout: single
title:  iOS Dynamic Type with custom fonts
date:   2019-04-08 21:38:52 +0200
categories:
  - Accessibility
  - UI
tags:
  - dynamic type
  - fonts
  - iOS
---
iOS offers a bunch of built-in **Accessibility features**, such as Voice Over, Switch Control, and **Dynamic Type**. The last one allows users to define preferred content size, therefore it can provide a much more consistent reading experience for everyone.

As we are going to see in this post it's simple to use its API, but it gets **repetitive** when programmatically configuring views. Let's see how we can make it more **readable** and **easier to maintain** when using it with **custom fonts**.

> "...iOS provides an extraordinary opportunity to deliver a superior mobile experience to every customer, including those with special needs."
> -- from Apple's Accessibility on iOS

As we are going to see in this post it's simple to use its API, but it gets repetitive when programmatically configuring views and we can make it more readable and easy to work with custom fonts.

<br>
## Text styles
Every app has a label, right? Let's say we want our label's text size to be dynamic.

<script src="https://gist.github.com/marinofelipe/5054e973aa9907a21b6a21c36da2dc13.js"></script>

iOS leverages [typography](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/typography/) with great focus on legibility. It comes with built-in text styles that can be used to display different text content and that lets the system know how best to adjust its size. Whenever we want views to adapt its context size we use text styles. You can configure text styles in either Interface Builder or your source code.

### Auto updating views
Pretty simple, isn't it? Ok, so right now the label has a system font and only adapts itself when the user starts the app. That's not so good at all, so let's see how we make our views to adapt to user content category settings changes.

<script src="https://gist.github.com/marinofelipe/850ae31e671bd9442d4f216b4e6c3b51.js"></script>

Since **iOS 10** with the addition of **UIContentSizeCategoryAdjusting** protocol that became easier. All base UIKit classes that have text **UILabel**, **UITextField**, and **UITextView** conforms to it. In this case, having **adjustsFontForContentSizeCategory** set to **true** takes the job of resizing.

### Under iOS 10
When working with important products every user counts. In this case to listen to content category changes of those who still uses **iOS 9.***, generally around [3%](http://gs.statcounter.com/ios-version-market-share/mobile-tablet/worldwide), **UIContentSizeCategory.didChangeNotification** enters the game.

<script src="https://gist.github.com/marinofelipe/8eef4cb83f8d3eda08843f0232d26595.js"></script>

<br>
## Custom fonts
That's pretty nice! Our label now adapts its text size whenever the user changes the preferred settings. Nevertheless, the awesome apps that we make generally a design system to follow and custom fonts, which since iOS 11 are supported through UIFontMetric class.
To use it we initialise a UIFontMetrics object injecting a text style, then we take care of scaling the font by calling its scaledFont function.

<script src="https://gist.github.com/marinofelipe/824d12a71f6f573e87197659ef616097.js"></script>

The above code works 👍, but doing it for all our fonts is quite repetitive and it can get a bit messy. Imagine how much pain would it be to also set the views to adapt to content size, configure the fonts, and all other configuring boilerplate. So, considering that you already know how to bundle custom fonts, let's see how we can make use of some nice **Swift** features to use them along with Dynamic Type for all views on easy.

So let's say our app shows famous movies and they have their own displayable fonts. First, we can define an enum for each of them.

<script src="https://gist.github.com/marinofelipe/8df29a0aedb3974d986c02899b5c624d.js"></script>

Then we create a **CustomFont** protocol with conditional conformance where **Self** is **RawRepresentable** and Self's **RawValue** has the type **String**, so we are able to get a constructed custom font with the name represented by its **rawValue** and the given size.

<script src="https://gist.github.com/marinofelipe/18d2b515e07ad5cc349b9e9ea8aa2b6f.js"></script>

Now we link our jurassic park custom font to our CustomFont protocol, and add it as a child of UIFont extension so it gets more clear that we are working with UIFonts.

<script src="https://gist.github.com/marinofelipe/fb7cd916e13ea3657eb1ee797515f142.js"></script>

Custom fonts can be set as below.

<script src="https://gist.github.com/marinofelipe/46012e19961e43ba931af18250ca9fbb.js"></script>

<br>
## Scalable fonts

Now let's see how we can make our custom fonts to scale.  Here we create a **ScalableFont** protocol where we define what we expect from scalable font objects. After that we extend UIFont to allow us to scale any font that we work with.

<script src="https://gist.github.com/marinofelipe/566cf6d7b379a02a1463561e935862bf.js"></script>

At this moment we are able to scale our declared custom fonts, defining or not a text style and a max size.

<script src="https://gist.github.com/marinofelipe/b065cbc959c59470e7d75ddfcf12aec0.js"></script>

Awesome! Yes, but right now we don't really know if any of our custom fonts are really there (until we run the old print UIFont.familyNames to see the available fonts).

<br>
## Conditional conformance, again...
I personally love conditional conformance and how Swift allows us to constraint a protocol extension to defined types. We are gonna make use of it creating an Optional extension where Wrapped is UIFont, so we can precondition failure whenever unwrapping a UIFont fails.

<script src="https://gist.github.com/marinofelipe/2f395766ac58b288d55fc08c79cd3fca.js"></script>

Now we can improve our CustomFont behaviour to retrieve only unwrapped fonts or pre condition failing when the font is not well bundled.

<script src="https://gist.github.com/marinofelipe/4d97fcdafc413e77eeb71544068bea9e.js"></script>

Right now, for each view, we would have set the font, make them update to content size, and all other kind of boilerplate when programatically creating them. That's why we are going to use factories, static in this case.

<br>
## Static factories
The factory pattern is a creational pattern which can be used to deal with the complexities of creation and configuring objects. To no more you should definitely read, <a href="https://www.swiftbysundell.com/posts/static-factory-methods-in-swift">John Sundell's post</a>.

To finish let's make also it easier to configure our labels or any other views that should adapt its content size, by using static factories, that could be global or available for specific domains, depending on the use case.

When programmatically creating views, that's end up being really great, in the other hand for @IBOulets we would need to handle already initialised objects.

<script src="https://gist.github.com/marinofelipe/8c314569ffc6f1783517da5b24830acf.js"></script>

That's the final usage

<script src="https://gist.github.com/marinofelipe/8ab48b7e75f61b1959702e35260b650d.js"></script>

<br>
## Conclusion
Supporting Dynamic text size is simple, but dealing with custom fonts, and views configuration along with it, can get a bit disorganised. Handling it with static factories, conditional conformance and enums might help to set an easy to read an maintain code, that makes our app visually accessible for our users and nice to work for our team.

To make apps fully accessible, I recommend you to watch the <a href="https://developer.apple.com/videos/play/wwdc2017/245/">2017 WWDC's Building Apps with Dynamic Type session</a>. There's also a lot of other great posts covering everything about Dynamic Type in our great community.

That's it for today. This is my very first blog post and I would really love to receive comments, feedbacks and suggestions. Also if you have any doubt, feel free to <a href="mailto:felipemarino91@gmail.com">contact me</a> or hit me on my Twitter account - <a href="https://www.twitter.com/_marinofelipe">@_marinofelipe</a>.

<br>
### Update - April 08, 2019
<a href="https://github.com/swiftmarino/dynamic-type-with-custom-fonts">Sample project</a> available on GitHub :)
