---
layout: single
title:  'Swift capture lists: Reference vs Value properties'
date:   2020-04-08 14:00:00 +0100
categories:
  - Tips
  - Swift
tags:
  - capture lists
  - reference type
  - value type
  - closures
---

When dealing with closures the importance of capturing `self` defining it as `[weak]` or `[unowned]` is well known, mainly, to avoid retain cycles. 

`Swift also allows capturing properties` from the context:
```swift
let blogger = Blogger()

let blogging = { [blogger] in
    blogger.blog()
}
```

This is really cool! And it can often be a better/easier approach over capturing self.
As any other property around Swift code though, a captured property can be of value or reference types. There are key differences that we must be aware of.

## Capturing reference types

Reference types instances holds a reference to a single object (or memory address).
This means that also when captured, a reference to that instance will be kept.

In the following example, even with `blog.openPost(task:)` executing the block/task asynchronously, the captured `blogPost` property will have its latest shared data at the moment the block is executed. That's why "text" is printed.

```swift
// Blog post is a reference type
final class BlogPost {
	var text: String = ""
}

final class Blogger {
    private let blog: Blog
    private var blogPost = BlogPost()

    func writeBlogPost() {
    	// For the sake of the example the openPost task is async,
    	// and therefore executed after blogPost.text is set
        blog.openPost { [blogPost] in
            debugPrint("\(blogPost.text)") // "text"
        }	

        blogPost.text += "text"
    }
}
```

## Capturing value types

In other hand value types have a copying nature. Each instance have a unique copy of the data.

Using the same example, let's say `BlogPost` is a struct. 

In this case the captured `blogPost` property is copied at the moment the block is **defined** and not when evaluated. Therefore, because blogPost.text is empty at this point, an empty string `""` is printed, even if the block is run after `self.blogPost.text` is updated.

```swift
// Blog post is a value type
struct BlogPost {
	var text: String = ""
}

final class Blogger {
    private let blog: Blog
    private var blogPost = BlogPost()

    func writeBlogPost() {
    	// For the sake of this example the openPost task is async,
    	// and therefore executed after blogPost.text is set
        blog.openPost { [blogPost] in
            debugPrint("\(blogPost.text)") // ""
        }	

        blogPost.text += "text"
    }
}
```

Note that if the use case implies that the captured property must work as a referece, even if it is a value type, by capturing it through Self the latest value is obtained at the moment of block evaluation, because self is accessed by reference and its value type as part of it.

```swift
func writeBlogPost() {
	// considering that `blogPost` is a property of a value type
    blog.openPost { [self] in
        debugPrint("\(self.blogPost.text)") // "text"
    }	

    blogPost.text += "text"
}
```

<br>
#### `That's it for Today!`

Using capture lists to capture properties instead of self can be really cool, but it is important to consider the nature of the captured properties to correctly choose between what to capture ;).
