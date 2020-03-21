
В основе этого стайл-гайда лежат Swift API Design Guidelines от Apple:

Swift https://swift.org/documentation/api-design-guidelines/

## Style and Formatting

### General
1. Use 4 spaces for indent
2. Max line width = 100
3. Trim trailing whitespace
4. Don't fight Xcode's <kbd>^</kbd> + <kbd>I</kbd> indentation behavior (exceptions: rx)

### Declarations

#### Variables
1. Use type-inferance intensively
```swift
let appleCount = 3

let volume: CGFloat = 0

let numbers = [1, 2, 3, 4]

let fractions: [CGFloat] = [1.2, 2.2, 3, 4]

let contactsDictionary = ["Alfred": "+7 916 909 58 20"]

let personSet: Set<String> = ["Stewie", "Cris", "Peter"]

let makeFullName = { (firstName: String, secondName: Int) in firstName + secondName }

let makeFullName: (String, String) -> String = { $0 + $1 } // also ok for small inline closures
```
#### Functions
1. Single line for single expression (if fits)
2. Omit `return` if possible
3. Default arguments should go at the end
4. Split into lines when
   1. cant fit into 100 column width
   2. parameter count >= 4
```swift
func compare(lhs: Int, rhs: Int) { lhs < rhs }

func compare(lhs: Int, rhs: Int, lexicographically: Bool = false) {
    lexicographically
        ? lhs < rhs
        : String(lhs) < String(rhs)
}

func makeMyAwesomeModel(
    dependency1: MyAwesomeService1,
    dependency1: MyAwesomeService2,
    dependency1: MyAwesomeService3
) {
    // ...
}
```

#### Types
```swift

```

### Control Flow


### Decoration
1. Use 1 line

#### Rx
```swift

```


## Нейминг

## Организация кода

