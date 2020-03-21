
## SoftPro Swift Style Guide

## Base

1. [Swift API Design Guidelines by Apple, [swift.org/...](https://swift.org/documentation/api-design-guidelines/)
2. Swift Style Guide by Google [google.github.io/...](https://google.github.io/swift/#general-formatting)

## Overrides
### Common
1. Use 4 spaces for indent

### Declarations
1. Omit `return` in functions with single expression



### Other 
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

#### Namespacing
Subclass `Namespace` class to make it clear that the type is just a namespace
```swift 

```

*discuss major differences:*
1. Private extensions vs private functions, [#access-levels](https://google.github.io/swift/#access-levels)
2. Redundant imports (Foundation + UIKit), [#import-statements](https://google.github.io/swift/#import-statements)
3. 
4.
5.

## Additions

### Formatting

#### RX
1. Formatting for Rx calls:
```swift

```

### Naming

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

In generic protocols, name associated type without `T` suffix:
```swift
protocol EntityType {
    associatedtype ID
    
    var id: ID { get }
}
```


### Code Organization

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



