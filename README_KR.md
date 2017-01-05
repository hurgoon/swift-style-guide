Swift 스타일과 규칙 안내입니다.

다음 목표(대략적인 우선순위)를 이루는 패턴을 장려하려고 합니다:

 1. 엄격성 증가, 프로그래머 에러의 가능성 감소
 1. 목적의 명확성 증가
 1. 중복 감소
 1. 미학에 대한 논쟁을 줄임

만약 제안이 있으면, 우리의 [공헌 정책](CONTRIBUTING.md)를 보고 pull request를 보내주시기 바랍니다. :zap:

----

#### 공백 [원문](https://github.com/github/swift-style-guide#whitespace)

 * 공백이 아닌 탭 사용.
 * 파일 끝에 줄 바꿈.
 * 코드를 논리 단위로 나누도록 빈 줄을 아낌없이 사용.
 * 줄 끝에 공백을 남기지 않기.
   * 빈 줄에서 들여쓰기조차 안됨.


#### `var` 바인딩보다 `let` 바인딩을 선호 [원문](https://github.com/github/swift-style-guide#prefer-let-bindings-over-var-bindings-wherever-possible)

가능하면(그리고 의심스럽더라도) `var foo = …` 보다 `let foo = …`을 사용합니다. 만약에 정말로 사용해야 할때만 `var`을 사용합니다. (즉, 값이 변할 것을 알고 있고, 예를 들면 `weak` 저장 수식어를 사용할 때입니다.)

_이유:_ 두 키워드의 목적과 의미는 명확하지만, *기본적으로 let*이 더 안전하고 명확한 코드입니다.

프로그래머에게 `let`-바인딩은 값이 예상되고, 바뀌지 않을 것임을 보장하고 명확하게 전달합니다. 후속 코드는 변수의 용도를 더 쉽게 추측할 수 있습니다.

코드를 더 쉽게 이해하게 됩니다. `var`를 사용하는 동안 값이 변하지 않았음을 가정해가면서, 직접 확인해야 합니다.

따라서 `var` 식별자가 사용된 것을 볼 때마다, 값이 변할 것을 가정하고 스스로 왜 그런지 물어볼 것입니다.

#### 옵셔널의 강제 언래핑 사용 피하기 [원문](https://github.com/github/swift-style-guide#avoid-using-force-unwrapping-of-optionals)

타입 `FooType?` 또는 `FooType!`의 `foo` 식별자가 있다면, 강제 언래핑으로 값(`foo!`)를 얻으려고 하지 마세요. 가능하면 말이죠.

대신에, 이 방식으로 하세요:

```swift
if let foo = foo {
    // 여기에서 언래핑된 `foo` 값 사용
} else {
    // 여기에 해당하는 경우, 옵셔널이 nil인 경우를 다루세요.
}
```

아니면 예를 들면 일부의 경우에서 Swift의 옵셔널 채이닝을 사용할 수 있습니다:

```swift
// `foo`가 nil이 아니라면 함수가 호출됩니다. 만약 `foo`가 nil이라면 함수 호출을 무시합니다.
foo?.callSomethingIfFooIsNotNil()
```

_이유:_ 명시적 옵셔널의 `if let`-바인딩 결과로 더 안전한 코드가 됩니다. 강제 언래핑은 런타임 크래시가 발생할 가능성이 많습니다.

#### 암시적으로 언래핑된 옵셔널 피하기 [원문](https://github.com/github/swift-style-guide#avoid-using-implicitly-unwrapped-optionals)

만약에 `foo`가 nil이라면 `let foo: FooType!` 대신 `let foo: FooType?`를 가능하면 사용하세요.(주의, `!` 대신 `?` 사용할 수 있습니다.)

_이유:_ 명시적 옵셔널이 더 안전한 코드입니다. 암시적으로 언래핑된 옵셔널은 런타임시 크래시의 원인이 될 수 있습니다.

#### 읽기전용 속성과 서브스크립트에서 암시적 getter 선호 [원문](https://github.com/github/swift-style-guide#prefer-implicit-getters-on-read-only-properties-and-subscripts)

가능하면 읽기 전용 계산된 속성과 읽기 전용 서브스크립트에서 `get` 키워드는 생략합니다.

그래서 다음과 같이 작성합니다:

```swift
var myGreatProperty: Int {
    return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… 다음과 같이 작성하지 않습니다.

```swift
var myGreatProperty: Int {
    get {
        return 4
    }
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_이유:_ 첫 번째 버전의 목적과 의미가 명확하여 더 적은 코드가 됩니다.

#### 항상 최상위 수준 정의를 명시적으로 접근 제어 지정 [원문](https://github.com/github/swift-style-guide#always-specify-access-control-explicitly-for-top-level-definitions)

최상위 수준 함수, 타입 그리고 변수는 항상 명시적 접근 제어 지정자가 있어야 합니다:

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

그러나 이들 내 정의는 적절한 곳에 암묵적으로 접근 제어를 남길 수 있습니다:

```swift
internal struct TheFez {
    var owner: Person = Joshaber()
}
```

_이유:_ 최상위 수준 정의에서 더욱 명확하게 `internal`이 되는 것과 명시적으로 신중하게 생각하여 결정을 보장하기가 극히 드뭅니다. 정의 내에서 같은 접근 제어 지정자 재사용은 단지 중복되며, 일반적으로 기본값이 합당합니다. 

#### 타입을 지정할 때, 항상 식별자 뒤에 콜론을 연결 [원문](https://github.com/github/swift-style-guide#when-specifying-a-type-always-associate-the-colon-with-the-identifier)

식별자의 타입을 지정할 때, 항상 식발자 뒤에 콜론을 바로 붙이고, 공백 다음에 타입 이름이 붙습니다.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_이유:_ 타입 지정자는 _식별자_에 대해서 무언가를 이야기합니다. 그래서 타입 지정자는 식별자와 위치해야 합니다.

또한, 딕셔너리의 타입을 지정할 때, 항상 콜론은 키 타입 뒤에 위치해야 하며 공백 다음에 값 타입이 뒤따릅니다.

```swift
let capitals: [Country: City] = [ Sweden: Stockholm ]
```

#### 필요할 때만 명시적으로 `self` 참조 [원문](https://github.com/github/swift-style-guide#only-explicitly-refer-to-self-when-required)

self의 속성과 메소드에 접근할 때, `self`를 따로 적지 않습니다.(기본적으로 `self`는 암묵적 참조입니다.):

```swift
private class History {
    var events: [Event]

    func rewrite() {
        events = []
    }
}
```

클로저처럼 파라미터명이 충돌일 경우에만 명시적으로 `self` 키워드를 사용합니다.:

```swift
extension History {
    init(events: [Event]) {
        self.events = events
    }

    var whenVictorious: () -> () {
        return {
            self.rewrite()
        }
    }
}
```

_이유:_ 클로저 내에서 `self`의 캡처링 의미를 더 돋보이게 만들고, 다른 곳에서 장황하게 사용하는 것을 피할 수 있게 합니다.

#### 클래스 보다 구조체를 선호 [원문](https://github.com/github/swift-style-guide#prefer-structs-over-classes)

클래스에서만 사용가능한 것들(identity, deinitializer 등)이 필요한 경우가 아니라면, 구조체로 구현하세요.

보통은 클래스를 사용하는 이유가, 상속이 (유일한) 이유는 아닙니다. 왜냐하면 다형성은 프로토콜이 제공할 수 있고, 구현의 재사용은 조립(Composition)을 통해서 가능합니다.

다음은 클래스 계층 구조 입니다:

```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * numberOfWheels
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}

```

다음과 같이 프로토콜로 리팩토링되었습니다:

```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * vehicle.numberOfWheels
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

_이유:_ 값 타입은 더 간단하고, `let` 키워드와 함께 예상대로 동작하여 쉽게 이해합니다.

#### 기본적으로 클래스는 `final`로 작성 [원문](https://github.com/github/swift-style-guide#make-classes-final-by-default)

클래스는 `final`로 시작해야 합니다. 상속이 필요하다면 클래스는 서브클래스를 만들도록 할 수 있게 변경합니다. 심지어, 클래스 내 가능한 많은 정의는 `final`이 되어야 하고, 같은 규칙을 따릅니다.

_이유:_ 일반적으로 구성은 상속을 선호하고, 아마도 상속을 선택하면 결정하는데 좀 더 많은 생각을 해야 합니다.(역자 주 - 상속 구조를 결정하기 위해 고민을 더 해야 한다고 생각됩니다.)

#### 가능하면 타입 인자 생략하기 [원문](https://github.com/github/swift-style-guide#omit-type-parameters-where-possible)

매개 변수가 된 타입의 메소드는 수신자가 같을 때 받는 타입에 타입 매개 변수를 생략할 수 있습니다. 예를 들어:

```swift
struct Composite<T> {
    …
    func compose(other: Composite<T>) -> Composite<T> {
        return Composite<T>(self, other)
    }
}
```

다음과 같이 작성할 수 있습니다:

```swift
struct Composite<T> {
    …
    func compose(other: Composite) -> Composite {
        return Composite(self, other)
    }
}
```

_이유:_ 중복 타입 매개변수를 생략하려는 목적이 명확합니다. 그리고 반환 타입이 다른 타입 매개변수를 취할 때 명확하게 차이가 나도록 만듭니다.

#### 연산자 정의에 공백 사용 [원문](https://github.com/github/swift-style-guide#use-whitespace-around-operator-definitions)

연산자를 정의할 때 공백을 사용합니다. 다음과 같이 하지 않습니다:

```swift
func <|(lhs: Int, rhs: Int) -> Int
func <|<<A>(lhs: A, rhs: A) -> A
```

다음과 같이 작성합니다:

```swift
func <| (lhs: Int, rhs: Int) -> Int
func <|< <A>(lhs: A, rhs: A) -> A
```

_이유:_ 연산자는 문장부호로 되어 있으며, 타입 또는 값 매개 변수 목록과 붙어 있으면 읽기 어렵습니다. 공백이 추가되어 더 명확해집니다.

#### 번역

* [中文版](https://github.com/Artwalk/swift-style-guide/blob/master/README_CN.md)
* [日本語版](https://github.com/jarinosuke/swift-style-guide/blob/master/README_JP.md)