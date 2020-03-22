
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
3. Omit `return` in functions with single expression
   ```
   
### Patterns
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
1. String, Numeric types, Bool and Date:
   ```swift
   // 

   var title: String

   var personCount: Int

   var bottomMargin: CGFloat

   var isHidden: Bool

   var createdAt: Date
   ```

2. Array
   ```swift
   var eventModels: [EventModel]

   var models: [EventModel] // also good if in `Event` context
   ```
   
3. Dictionary
   ```swift 
   var eventsById: [Event.ID: Event]

   var addressByPersonName: [String: String]

   var jsonDictionary: [String: Any] // also OK, in case of poor semantics
   ```
   
4. Other
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

### Patterns

#### Declaration order
```swift
```

#### Splitting up into extensions
```swift 
```

#### Prefer immutable values whenever possible. 
Mutable variables increase complexity, so try to keep them in as narrow a scope as possible. Use `map` and `compactMap` instead of appending to a new collection. Use `filter` instead of removing elements from a mutable collection.
```swift
// bad
var results = [SomeType]()
for element in input {
  let result = transform(element)
  results.append(result)
}

// good
let results = input.map(transform)
```

```swift
// bad
var results = [SomeType]()
for element in input {
  if let result = transformThatReturnsAnOptional(element) {
    results.append(result)
  }
}

// good
let results = input.compactMap(transformThatReturnsAnOptional)
```

#### Guard
Prefer using `guard` at the beginning of a scope. It's easier to reason about a block of code when all guard statements are grouped together at the top rather than intermixed with business logic. Use `if` otherwise

## Appendix A: Tools

### Patterns

#### `fallback(...)` and `fatalError(...)`
Handle an unexpected but recoverable condition with an `fallback` function
```swift
func eatFruit(at index: Int) {
    guard index < fruits.count else { return fallback() }
    
    // ...
}

func nameForFruit(_ fruit: Fruit) -> String {
    guard knownFruits.contains(fruit) else { return fallback("Unnamed") }
    
    // ...
}
```

If the unexpected condition is not recoverable, prefer `fatalError`
```swift
func fruit(at: index) -> Fruit {
    guard index < fruits.count else { fatalError(.shouldNeverBeCalled) }
    
    // ...
}
```

#### `strongify(self) { ... }`
When capturing `self` in a closure, use `strongify` instead of cumbersome `weakify-strongify self` routine 
```swift
// bad
authModel.unauthorize
    .observeOn(MainScheduler.instance)
    .subscribe(
        onNext: { [weak self] error in
            guard let self = self else { return }
            self.didReceiveLoginError(error)
            self.settingsModel.eraseData()
            self.initUserInterface()
        }
    ).disposed(by: disposeBag)

// good
authModel.unauthorize
    .observeOn(MainScheduler.instance)
    .subscribe(
        onNext: strongify(self) { sself, error in
            sself.didReceiveLoginError(error)
            sself.settingsModel.eraseData()
            sself.initUserInterface()
        }
    ).disposed(by: disposeBag)
```

```swift

// bad
elementsProvider.observable
    .subscribe(
        onNext: { [weak self] elements in
            self?.onUpdateElements(elements)
        }
    )
    .disposed(by: bag)
    
// good
elementsProvider.observable
    .subscribe(onNext: strongify(self, CollectionReloadCoordinator.onUpdateElements))
    .disposed(by: bag)
```

## Appendix B: Rx

### Formatting
#### Guide
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
actionObservable  // too long
```

## Appendix C: UI



