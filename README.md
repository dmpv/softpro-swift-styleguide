# SoftPro Swift Style Guide (draft)

This codestyle is based on two documents:

1. [Apple's Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
2. [Google's Swift Style Guide](https://google.github.io/swift/)

and adds some [overrides](#overrides) and [additions](#additions) to the latter.

Before reading this document, be sure you are familiar with [(1)](https://swift.org/documentation/api-design-guidelines/) and [(2)](https://google.github.io/swift/).


_Shoutout to Airbnb team and their [styleguide](https://github.com/airbnb/swift) for certain well-stated points._


# Overrides

## Formatting

1. **Use 4 spaces to indent lines**

1. **`guard` and `if`**

   For one expression:   
   ```swift
   guard fruitCount > 5 else { return } // (linebreak allowed)   

   if fruitCount < 7 { print("need more fruit for this pie") }
   ``` 

   When multline:
   ```swift
   guard
       case let .upsert(message) = anyMessage,
       case let .text(textMessage) = message,
       let correlationID = textMessage.content.correlationID
   else { return .delete($0.id) } // linebreak allowed
   ```

1. **Omit `return` in functions with single expression**

## Patterns

1. **`throws` vs `-> ReturnT?`**

   Prefer throwing an error instead of returning optional in function of initializer:
   ```swift

   // bad: why did it fail? 
   func parse(encoded value: Any) -> String? {
       // ...
   }

   // good
   func parse(encoded value: Any) throws -> String {
       guard let dictionary = value as? [String: String] else { throw ParseError("not a dictionary") }
       guard let value = dictionary["id"] else { throw ParseError("`id` not presented") }
       // ...
   }
   ```

1. **Namespaces**

   Subclass `Namespace` class to make it clear that the type is just a namespace:
   ```swift 
   // bad
   enum Config {
       static let lengthRange = 10...50
   }

   // good
   class Config: Namespace {
       static let lengthRange = 10...50
   }
   ```

# Additions

## Formatting

1. **Vertical white space**

   Add single blank line to separate code parts:
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

   EOF
   ```

## Naming

1. **General rules**

   * Word in plural form can only be last part of the name (with rare exceptions):
      ```swift 
      let eventsModel: EventsModel // bad
      let eventModel: EventModel // good

      let objectsCount: Int // bad   
      let objectCount: Int // good

      let teamsProvider: Provider<Team> // bad
      let teamProvider: Provider<Team> // good

      func subscribeOnChatNotifications() { ... } // good

      var eventsByID: [Event.ID: Event] // exception, good
      ```

2. **Variables**

   * For `String`, Numeric types, `Bool` and `Date` omit the type name:
      ```swift 
      var title: String
      var id: String

      var personCount: Int
      var bottomMargin: CGFloat

      var isHidden: Bool
      var shouldInvalidate: Bool

      var createdAt: Date
      ```

   * For `Array` use plural form:
      ```swift
      var eventModels: [EventModel]
      var models: [EventModel] // also good if in `Event` context
      ```

   * For `Dictionary` use pattern `<ObjectT>sBy<KeyT>` (with some exceptions):
      ```swift 
      var eventsById: [Event.ID: Event]
      var addressesByPersonName: [String: String]

      var jsonDictionary: [String: Any] // also OK, in case of poor semantics
      ```

   * For other types, name should end either with the name of the type or it's suffix:
      ```swift
      var eventSet: Set<Event>

      var containerView: UIView
      var stackView: UIStackView  
      var titleLabel: UILabel

      var eventStatus: EventStatus
      var status: EventStatus // also OK if in `Event` context

      var eventChange: EntityCollectionChange<Event>
      ```
   
3. **Abbreviations**

   Abbreviations are prohibited:
   ```swift
   // bad
   var subs: Subscription

   var s: String

   var params: [String: String]

   var val: NSValue

   var i: Int
   ```

   Exceptions are:
   ```
   str (string)
   config (configuration)
   dict (dictionary)
   ```

   Abbreviations from the list above do not reduce clarity, improve code readability and are highly recommeded to use

4. **Acronyms**

   Acronyms in names (e.g. URL) should be all-caps except when it’s the start of a name that would otherwise be lowerCamelCase, in which case it should be uniformly lower-cased:
   ```swift
   class URLValidator {
     func isValidURL(_ url: URL) -> Bool { ... }

     func isProfileURL(_ url: URL, for userID: String) -> Bool { ... }
   }

   let urlValidator = URLValidator()
   let isProfile = urlValidator.isProfileUrl(urlToTest, userID: idOfUser)
   ```
   Exception: although `id` is not an acronym, the rules for it are the same

5. **Generics**

   For generic type and function declarations, name placeholder types using `<Placeholder>T` pattern:
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

   For generic protocols, name the associated type without `T` suffix:
   ```swift
   protocol EntityType {
       associatedtype ID

       var id: ID { get }
   }
   ```

## Patterns

1. **`guard` usage**

   Prefer using `guard` at the beginning of a scope. It's easier to reason about a block of code when all guard statements are grouped together at the top rather than intermixed with business logic. Use `if` otherwise.
   
1. **property declarations**

   * Use `let` for properties with values injected through the initializer      
   
   * Use `var` for computed, mutable properties or optional external dependencies (e.g. delegate, dataSource)
   
   * Use `lazy var` to avoid implicitly unwrapped optionals
  
   Use implace property initialization for lazy and mutable properties.
   
   If property intialization doesn't fit into one line, extract it into `make<PropertyName>` function. Property observers should also fit into one line or being extracted.
   
   ```swift
   class EventViewModel {
      var statusObservable: Observable<Status> { statusRelay.asObservable }
      
      weak var delegate: EventViewModelDelegate? {
         didSet { delegate.viewModel(self, updatedStatus: statusRelay.value) }
      }
      
      private(set) var cachedHeaderHeight: CGFloat = 0.0 {
         didSet { print("cache updated") }
      }
   
      private let model: EventModel
      private let soundEffectPlayer: SoundEffectPlayer
      
      private let config = Config()
      private let bag = DisposeBag()
      
      private lazy var dateFormatter = makeDateFormatter()
      private lazy var statusRelay = BehaviorRelay(value: .idle)

      init(
         model: EventModel,
         soundEffectPlayer: SoundEffectPlayer
      ) {
         self.model = model
         self.soundEffectPlayer = soundEffectPlayer
      }
      
      // ...
   }
   ```
   
1. **Declaration order (TODO)**

   ```swift
   class MyClass {
       var publicVar: Int = 0

       let publicLet: Int

       private var privateVar: Int

       private lazy var privateLazyVar: Int

       private let privateLet: Int

       init() { ... }

       override func overridedMethod() { ... }

       func publicMethod() { ... }

       private func privateMethod() { ... }
   }

   extension MyClass {
       static func publicStaticMethod() { ... }

       private static func privateStaticMethod() { ... }
   }

   extension MyClass {
       enum MyAuxillaryEnum {
           case a
           case b
       }

       enum MyAuxillaryStruct {
           var text: String
           var counter: Int
       }
   }
   ```

1. **Splitting up into extensions (TODO)**

   Avoid using extensions on your class for splitting logic purposes. Use marks instead.
   
   Use extensions:
   * to implement protocol conformance
   * when it can be extracted to another file
   
   TODO: Add an example
   
1. Use `Pure` namespace to group pure functions
   ```swift
   extension MyClass {
       class Pure: Namespace {
           static func pureFunction1(a: Int) -> Int { ... }

           static func pureFunction2(b: Int, c: String) -> String { ... }
       }
   }
   ```

# Appendix A: Tools

## Patterns

1. **`fallback(...)` and `fatalError(...)`**

   Handle an unexpected but recoverable condition with an `fallback` function:
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

   If the unexpected condition is not recoverable, prefer `fatalError`:
   ```swift
   func fruit(at: index) -> Fruit {
       guard index < fruits.count else { fatalError(.shouldNeverBeCalled) }

       // ...
   }
   ```

2. **`strongify(self) { ... }`**

   When capturing `self` in a closure, use `strongify` instead of cumbersome `weakify-strongify self` routine.
   
   For now, avoid using `strongify` with closures, since `sself` is ugly:
   ```swift
   // avoid
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

   `strongify` often allows you to avoid curly brackets:
   ```swift
   // Somewhere in CollectionReloadCoordinator class...

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

   In case `self` is `nil` at the moment of invocation, strongified function returns `nil`.
   Use function composition operator (`>>>`) to remove optionality from the return value of strongified call:
   ```swift
   // in EventViewModel:
   eventModel.statusObservable
       .filter(strongify(self, EventViewModel.shouldUpdateForStatus) >>> { $0 ?? false })
       .subscribe(onNext: strongify(self, EventViewModel.update))
       .disposed(by: bag)
   ```

# Appendix B: Rx

## Formatting

1. **Indentation**

   Example of correctly indented code:
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

## Naming

1. **Dispose bags**

   Omit `dispose` word when naming `DisposeBag`'s:
   ```swift
   // Single default bag
   lazy var bag = DisposeBag()

   // Specific bag
   lazy var subscriptionBag = DisposeBag()
   ```

1. **Abbreviations (TODO?)**

   Allowed abbreviations:
   
   `actionObsevable` -> `$action`
   
# Appendix C: UI

TODO



Comments:
1. 
if, guard — ранний выход

2.
if, guard — без однострочности

3.
Исключение — ID — с большой

4. Single-expression func: ньюанс Двустрочность

5. Неймспейсы (пример другой)

6. Марки — на свое усмотрение

7. Пустые строки — не ставим после объ типа и экстеншна

8. 
VC, VM — не пишем
i — не пишем

9. lazy — mutable

10. didSet, instantiate — oneline

11 Порядок var/let — todo примеры

12 Не бьем класс на экстеншны, только по признаку протоколов + то, что можно унести в отдельный файл

13 Declaration order

static - в конце
types - в конце

14 Pure


15 strongify - только 1 строка (метод)

16 ctrl + i recommednation

17 switch — 1 или 2 строки

18 utils, helpers? 

19 file naming: +, ., ... (Google)

20. Можно ссылаться на эппл гайд и гугл гайд

*TODO: discuss distinctions from Google's guide:*
* Private extensions vs private functions, [#access-levels](https://google.github.io/swift/#access-levels)
* Redundant imports (Foundation + UIKit), [#import-statements](https://google.github.io/swift/#import-statements)

