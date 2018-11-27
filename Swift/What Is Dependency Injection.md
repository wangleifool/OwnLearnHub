Original Article is https://cocoacasts.com/nuts-and-bolts-of-dependency-injection-in-swift



My favorite quote about dependency injection is a quote by [James Shore](http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html). It summarizes much of the confusion that surrounds dependency injection.

> "Dependency Injection" is a 25-dollar term for a 5-cent concept. - [James Shore](http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html)

When I first heard about dependency injection, I figured it was a technique too advanced for my needs at that time. I could do without dependency injection, whatever it was.

## What Is Dependency Injection

I later learned that dependency injection is a simple concept when it's reduced to its bare essentials. James Shore offers a succinct and straightforward definition of dependency injection.

> Dependency injection means giving an object its instance variables. Really. That's it. - [James Shore](http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html)

For developers new to dependency injection, it's important to learn the basics before relying on a framework or library. Start simple. Chances are that you already use dependency injection without realizing it.

Dependency injection is nothing more than injecting dependencies into an object instead of tasking the object with the responsibility of creating its dependencies. Or, as James Shore puts it, you give an object its instance variables.

## An Example

Let me show you an example. In this example, we define a `UIViewController` subclass that declares a property, `requestManager`, of type `RequestManager?`.

```swift
import UIKit

class ViewController: UIViewController {

    var requestManager: RequestManager?

}
```

We can set the value of the `requestManager` property one of two ways.

### Without Dependency Injection

The first option, without dependency injection, is to task the `ViewController` class with the instantiation of the `RequestManager` instance. We can make the property lazy or initialize the request manager in the view controller's initializer. That's not the point, though. The point is that the view controller is in charge of creating the `RequestManager`instance.

```swift
import UIKit

class ViewController: UIViewController {

    var requestManager: RequestManager? = RequestManager()

}
```

This means that the `ViewController` class not only knows about the behavior of the `RequestManager` class. It also knows about its instantiation. That's a subtle but important detail.

### With Dependency Injection

The second option, with dependency injection, is to inject the `RequestManager` instance into the `ViewController` instance. Even though the end result may appear identical, it isn't. By injecting the request manager, the view controller doesn't know how to instantiate the request manager.

```swift
// Initialize View Controller
let viewController = ViewController()

// Configure View Controller
viewController.requestManager = RequestManager()
```

Many developers immediately discard the second option because it's cumbersome and unnecessarily complex. But if you consider the benefits, dependency injection becomes more appealing.

## Another Example

I'd like to show you another example to emphasize the point I made earlier. Take a look at this example.

```swift
import Foundation

protocol Serializer {

    func serialize(data: AnyObject) -> Data?

}
import Foundation

class RequestSerializer: Serializer {

    func serialize(data: AnyObject) -> Data? {
        ...
    }

}
class DataManager {

    var serializer: Serializer? = RequestSerializer()

}
```

The `DataManager` class has a property, `serializer`, of type `Serializer?`. In this example, `Serializer` is a protocol. The `DataManager` class is in charge of instantiating an instance of a type that conforms to the `Serializer` protocol, the `RequestSerializer` class in this example.

Should the `DataManager` class know how to instantiate an object of type `Serializer`? Take a look at this example to show the power of protocols and dependency injection.

```swift
// Initialize Data Manager
let dataManager = DataManager()

// Configure Data Manager
dataManager.serializer = RequestSerializer()
```

The `DataManager` class is no longer in charge of instantiating the `RequestSerializer`class. It no longer assigns a value to its `serializer` property. In fact, we can replace `RequestSerializer` with another type as long as it conforms to the `Serializer` protocol. The `DataMananger` class no longer knows or cares about these details.

## What Do We Gain

I hope that these examples have at least captured your attention. Let me list a few additional benefits of dependency injection.

### Raise Transparency

By injecting the dependencies of an object, the responsibilities and requirements of a class or structure become more clear and transparent. By injecting a request manager into a view controller, we understand that the view controller depends on the request manager *and* we can assume the view controller is responsible for request managing and/or handling.

### Improve Testability

Unit testing is much easier with dependency injection. Dependency injection allows developers to replace an object's dependencies with mock objects, which makes isolating behavior and setting up unit tests easier and less complicated.

```swift
import Foundation

class MockSerializer: Serializer {

    func serialize(data: AnyObject) -> Data? {
        ...
    }

}
// Initialize Data Manager
let dataManager = DataManager()

// Configure Data Manager
dataManager.serializer = MockSerializer()
```

### Separation of Concerns

As I mentioned and illustrated earlier, another subtle benefit of dependency injection is a stricter separation of concerns. The `DataManager` class in the example isn't responsible for instantiating the `RequestSerializer` instance. It doesn't need to know how to do this.

Even though the `DataManager` class is concerned with the behavior of its serializer, it isn't and shouldn't be concerned with its instantiation. What if the `RequestManager` of the first example also has a number of dependencies. Should the `ViewController` class be aware of those dependencies too?

### Looser Coupling

The example of the `DataManager` class illustrated how the use of protocols and dependency injection can reduce coupling in a project. Protocols are incredibly useful and versatile in Swift. The example we discussed is a scenario in which protocols really shine.

## Types of Dependency Injection

Most developers consider three forms or types of dependency injection:

- dependency injection through an initializer (initializer injection)
- dependency injection using properties (property injection)
- dependency injection in methods (method injection)

These types shouldn't be considered equal, though. Let me list the pros and cons of each type.



### Initializer Injection

I prefer to pass dependencies during the initialization of an object because this has several key benefits. The most important benefit is that dependencies passed in during initialization can be made immutable. This is very easy to do in Swift by declaring the properties for the dependencies as constants. Take a look at this example.

```swift
class DataManager {

    private let serializer: Serializer

    init(serializer: Serializer) {
        self.serializer = serializer
    }

}
// Initialize Request Serializer
let serializer = RequestSerializer()

// Initialize Data Manager
let dataManager = DataManager(serializer: serializer)
```

The only way to set the `serializer` property is by passing it as an argument during initialization. The `init(serializer:)` method is the designated initializer and guarantees that the `DataManager` instance is correctly configured. Another benefit is that the `serializer` property cannot be mutated.

Because we're required to pass the serializer as an argument during initialization, the designated initializer clearly shows what the dependencies of the `DataManager` class are.



### Property Injection

Dependencies can also be injected by declaring an internal or public property on the class or structure that requires the dependency. This may seem convenient, but it adds a loophole in that the dependency can be modified or replaced. In other words, the dependency isn't immutable.

```swift
import UIKit

class ViewController: UIViewController {

    var requestManager: RequestManager?

}
// Initialize View Controller
let viewController = ViewController()

// Configure View Controller
viewController.requestManager = RequestManager()
```



### Method Injection

Dependencies can also be injected whenever they're needed. This is easy to do by declaring a method that accepts the dependency as an argument. In this example, the serializer isn't a property on the `DataManager` class. Instead, the serializer is injected as an argument of the `serializeRequest(_:withSerializer:)` method.

```swift
import Foundation

class DataManager {

    func serializeRequest(request: Request, withSerializer serializer: Serializer) -> Data? {
        ...
    }

}
```

Even though the `DataManager` class loses some control over the dependency, the serializer, this type of dependency injection introduces flexibility. Depending on the use case, we can choose what type of serializer to pass into `serializeRequest(_:withSerializer:)`.

There's another minor benefit. The `DataManager` class doesn't hold a reference to the serializer. This is a subtle benefit that reduces the complexity of the `DataManager` class.

## Singletons

Dependency injection is a pattern that can be used to eliminate the need for singletons in a project. I'm not a fan of the singleton pattern and I avoid it whenever possible. Even though I don't consider the singleton pattern an anti-pattern, I believe they should be used very sparingly. The singleton pattern increases coupling whereas dependency injection reduces coupling.

Too often, developers use the singleton pattern because it's an easy solution to an often trivial problem. Dependency injection, however, adds clarity and transparency to a project. By injecting dependencies during the initialization of an object, it becomes clear which dependencies the target class or structure has, and it also reveals some of the object's responsibilities.

## Give It a Try

Dependency injection is one of my favorite patterns because it helps me stay on top of complex projects. This pattern has so many benefits. The only drawback I can think of is the need for a few more lines of code. That's a drawback I'm happy to accept.