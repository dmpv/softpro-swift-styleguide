
## SoftPro Swift Style Guide

## Base

1. [Swift API Design Guidelines by Apple, [swift.org/...](https://swift.org/documentation/api-design-guidelines/)
2. Swift Style Guide by Google [google.github.io/...](https://google.github.io/swift/#general-formatting)

## Overrides
### Formatting
1. Use 4 spaces for indent
2. Use following guide for `guard` statements
   ```swift
   guard
       case let .upsert(message) = anyMessage,
       case let .text(textMessage) = message,
       let correlationID = textMessage.content.correlationID
   else { return .delete($0.id) }
   ```

### Styling
1. Omit `return` in functions with single expression

### Misc
#### `throws` vs `-> ReturnT?`
Prefer to throw an error instead of returning optional in function of initializer
```swift

// Bad: why did it fail? 
func parse(encoded value: Any) -> String? {
    // ...
}

// Good
func parse(encoded value: Any) throws -> String {
    guard let dictionary = value as? [String: String] else { throw ParseError("not a dictionary") }
    guard let value = dictionary["id"] else { throw ParseError("`id` not presented") }
    // ...
}
```

#### Namespaces
Subclass `Namespace` class to make it clear that the type is just a namespace
```swift 

```

*discuss differences:*
1. Private extensions vs private functions, [#access-levels](https://google.github.io/swift/#access-levels)
2. Redundant imports (Foundation + UIKit), [#import-statements](https://google.github.io/swift/#import-statements)
3. `let` position in `switch-case`, [#pattern-matching](https://google.github.io/swift/#pattern-matching)
4.
5.

## Additions

### Formatting

#### Vertical white space
Add single blank line to code parts
```swift
// ...
//  Copyright © 2020 SoftPro. All rights reserved.
//

import Foundation

final class EventViewModel {
    // ...
}

// MARK: - Auxillary Types

extension EventViewModel {
    struct Cache {
        // ...
    }
}

// MARK: - EventModelDelegate

extension EventViewModel: EventModelDelegate {
    func model(_ eventModel: EventModel, didUpdateEventFor id: Event.ID) {
        // ...
    }
}
```

### Naming

#### Variables
```swift
// For String, Numeric types, Bool and Date:

var title: String

var personCount: Int

var bottomMargin: CGFloat

var isHidden: Bool

var createdAt: Date
```

For Array:
```swift
var eventModels: [EventModel]

var models: [EventModel] // also good if in `Event` context
```
For Dictionary:
```swift 
var eventsById: [Event.ID: Event]

var addressByPersonName: [String: String]

var jsonDictionary: [String: Any] // also OK, in case of poor semantics
```
For anything else
```swift
var eventSet: Set<Event>

var containerView: UIView

var titleLabel: UILabel

var eventStatus: EventStatus

var status: EventStatus // also good if in `Event` context
```

#### Acronyms
Acronyms in names (e.g. URL) should be all-caps except when it’s the start of a name that would otherwise be lowerCamelCase, in which case it should be uniformly lower-cased
```swift
class URLValidator {
  func isValidURL(_ url: URL) -> Bool { ... }
  
  func isProfileURL(_ url: URL, for userID: String) -> Bool { ... }
}

let urlValidator = URLValidator()
let isProfile = urlValidator.isProfileUrl(urlToTest, userID: idOfUser)
```

#### Generics

In generic type and function declarations, name placeholder types using `<Placeholder>T` pattern:
```swift
public func index<CollectionT: Collection, ElementT>(
    of element: ElementT,
    in collection: CollectionT
) throws -> CollectionT.Index where CollectionT.Element == ElementT, ElementT: Equatable {
    for element in collection {
        // ...
    }
}
```

In generic protocols, name the associated type without `T` suffix:
```swift
protocol EntityType {
    associatedtype ID
    
    var id: ID { get }
}
```

### Misc

#### Declaration order
```swift
```

#### Splitting up into extensions
```swift 
```

## Appendix A: Tools
### Patterns
1. Instead of using `return` in `guard`, consider using `return fallback()` in cases, where

## Appendix B: Rx

### Formatting
```swift
actionObservable
    .filter { $0 == .startLoadNextPage }
    .withLatestFrom(stateObservable.map { $0.feed }) // one line is OK for single rx-statement
    .flatMap { state in // use named parameters for multline rx-statement
        messageService.loadMessages(before: state.lastMessageDate(), limit: state.pageSize)
            .observeOn(MainScheduler.instance)
            .asObservable()
            .map(FeedAction.finishLoadNextPage)
            .catchError { _ in .empty() }
    }
    .subscribe(
        onNext: { action in
            // ...
        },
        onError: { error in
            // ...
        }
    ).disposed(by: bag)  // the only statement allowed not to start with a line break
```

### Naming
1. Omit `dispose` word when naming `DisposeBag`'s
```swift
// Single default bag
lazy var bag = DisposeBag()

// Specific bag
lazy var subscriptionBag = DisposeBag()
```

2. (Experimental) Add corresponding suffix for variable names
```swift
// What suffix shall we use? 
actionObservable  // too fucking long
```

## Appendix C: UI



