
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
1. private extensions vs private functions [#access-levels](https://google.github.io/swift/#access-levels)
1. private extensions vs private functions [#access-levels](https://google.github.io/swift/#access-levels)

## Additions

### Formatting

#### RX
1. Formatting for Rx calls:
```swift

```

### Code Organization

#### Vertical white space
1. Add a single blank line before and after `MARK:`
```swift
//...
} 

// MARK: - MyClass+OtherClassDelegate

extension MyClass: OtherClassDelegate { 
    // ...
}
```



