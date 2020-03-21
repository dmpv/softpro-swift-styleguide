
## SoftPro Swift Style Guide

## Base

1. [Swift API Design Guidelines by Apple, [swift.org/...](https://swift.org/documentation/api-design-guidelines/)
2. Swift Style Guide by Google [google.github.io/...](https://google.github.io/swift/#general-formatting)

## Overrides
### Common
1. Use 4 spaces for indent

### Declarations
1. Omit `return` in functions with single expression

## Additions

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
