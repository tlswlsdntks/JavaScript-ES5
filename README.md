Functional Programming Jargon
FP (Functional Programming)는 많은 이점을 제공하며 결과적으로 인기가 높아지고 있습니다. 그러나 각 프로그래밍 패러다임에는 고유한 특수 용어가 포함되어 있으며 FP도 예외는 아닙니다. 용어집을 제공함으로써 FP 학습이 쉬워지길 바랍니다.

예제는 JavaScript (ES2015)로 제공됩니다. Why JavaScript?

이것은 WIP입니다; PR을 보내 주시기 바랍니다;)

해당되는 경우, 이 문서는 Fantasy Land spec에 정의된 용어를 사용합니다.

Table of Contents

Arity
Higher-Order Functions (HOF)
Partial Application
Currying
Closure
Auto Currying
Function Composition
Continuation
Purity
Side effects
Idempotent
Point-Free Style
Predicate
Contracts
Category
Value
Constant
Functor
Pointed Functor
Lift
Referential Transparency
Equational Reasoning
Lambda
Lambda Calculus
Lazy evaluation
Monoid
Monad
Comonad
Applicative Functor
Morphism
Endomorphism
Isomorphism
Setoid
Semigroup
Foldable
Lens
Type Signatures
Algebraic data type
Sum type
Product type
Option
Functional Programming Libraries in JavaScript
Arity
함수가 취하는 인수의 갯수입니다. 단항, 이원, 삼항 등의 단어에서 이 단어는 두 개의 접미사 "--ary"와 "--ity"로 구분된다는 구별이 있습니다. 예를 들어, 더하기는 두 개의 인수를 취하므로 이진 함수 또는 2의 arity를 ​​갖는 함수로 정의됩니다. 그러한 함수는 라틴어에서 그리스어로 뿌리를 선호하는 사람들에 의해 때때로 "dyadic"이라고 불릴 수 있습니다. 비슷하게 변수 개수의 인수를 취하는 함수는 "variadic"이라고 불리는 반면, 2진 함수는 currying과 부분 적용에도 불구하고 두 개의 인수만 제공해야합니다 (아래 참조).

const sum = (a, b) => a + b

const arity = sum.length
console.log(arity) // 2

// The arity of sum is 2
Higher-Order Functions (HOF)
함수를 인수로 사용하거나 함수를 반환하는 함수입니다.

const filter = (predicate, xs) => xs.filter(predicate)
const is = (type) => (x) => Object(x) instanceof type
filter(is(Number), [0, '1', 2, null]) // [0, 2]
Partial Application
함수를 부분적으로 적용한다는 것(Partial Application)은 인수 중 일부를 원래 함수에 미리 채워서 새 함수를 만드는 것을 의미합니다.

// 부분적으로 적용된 함수를 만드는 도우미
// 함수와 몇 가지 인수를 취합니다.
const partial = (f, ...args) =>
  // 나머지 인수를 취하는 함수를 반환합니다.
  (...moreArgs) =>
    // 그리고 모두 원래 함수를 호출합니다.
    f(...args, ...moreArgs)

// 뭔가 적용할 것
const add3 = (a, b, c) => a + b + c

// `2`와`3`을 부분적으로 `add3`에 적용하면 하나의 인자로 쓸 수 있습니다
const fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c

fivePlus(4) // 9
Function.prototype.bind를 사용하여 부분적으로 적용할수도 있습니다 :

const add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
Partial application을 사용하면 데이터를 보유 할 때 데이터를 조작해서 보다 간단한 함수를 작성할 수 있습니다. Curried 함수는 부분적으로 자동 적용됩니다.

Currying
여러 인수를 사용하는 함수를 한 번에 하나씩 사용하는 함수로 변환하는 프로세스입니다.

함수가 호출될 때마다 하나의 인수만 허용하고 모든 인수가 전달될 때까지 인수 하나를 취하는 함수를 리턴합니다.

const sum = (a, b) => a + b

const curriedSum = (a) => (b) => a + b

curriedSum(40)(2) // 42.

const add2 = curriedSum(2) // (b) => 2 + b

add2(10) // 12
Closure
클로저(Closure)는 범위 외부의 변수에 액세스하는 방법입니다. 공식적으로 클로저는 정적 스코프가 지정된 바인딩을 구현하는 기술입니다. 이것은 환경과 실행코드(함수)를 표현합니다.

클로저는 실행이 정의된 블록 밖으로 이동한 후에도 함수의 로컬변수를 액세스용으로 캡처하는 범위입니다. 즉. 변수가 선언된 블록이 실행을 마친 후에 범위를 참조할 수 있습니다.

const addTo = x => y => x + y
var addToFive = addTo(5)
addToFive(3) // returns 8
addTo() 함수는 (내부적으로 add ()라고 불리는) 함수를 반환하며 매개 변수 5를 갖는 curry 호출로 addToFive라는 변수에 저장합니다.

이상적으로 addTo 함수가 실행을 끝내면 그 범위는 지역 변수 add, x, y로 접근 할 수 없어야합니다. 그러나 addToFive()를 호출하면 8을 반환합니다. 이는 코드 블록이 실행을 마친 후에도 addTo 함수의 상태가 저장된다는 것을 의미합니다. 그렇지 않으면 addTo가 addTo(5)로 호출되고 x 값이 5로 설정되었음을 알 수 있는 방법이 없습니다.

어휘적 유효 범위는 x 및 add 값 (실행을 완료한 부모의 개인 변수)을 찾을 수 있는 이유입니다. 이 값을 Closure라고합니다.

스택은 함수의 어휘적 유효 범위와 함께 부모에 대한 참조 형식으로 저장됩니다. 이렇게하면 클로저와 기본변수가 가비지 컬렉션 되는 것을 방지합니다.(적어도 하나의 실제 참조가 있으므로)

Lambda Vs Closure :

Lambda는 기본적으로 함수를 선언하는 표준 방법이 아닌 인라인으로 정의된 함수입니다. Lambda는 종종 객체로 전달될 수 있습니다.

Closure는 몸의 외부 필드를 참조하여 주변상태를 둘러싼 함수입니다. 닫힌상태는 클로저의 호출을 가로질러 유지됩니다.

추가 읽기 / 출처

Lambda Vs Closure
How do JavaScript Closures Work?
Auto Currying
여러 개의 인수를 취하는 함수를 하나의 인수로 변환하면 올바른 수의 인수보다 작으면 나머지를 취하는 함수가 반환됩니다. 함수가 올바른 수의 인수를 얻으면 평가됩니다.

lodash & Ramda는 이런식으로 작동하는 curry함수를 가지고 있습니다.

const add = (x, y) => x + y

const curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
추가 읽기

Favoring Curry
Hey Underscore, You're Doing It Wrong!
Function Composition
하나의 함수 출력이 다른 함수의 입력인 제 3의 함수를 형성하기 위해 두 함수를 함께 모으는 행위입니다.

const compose = (f, g) => (a) => f(g(a)) // 정의
const floorAndToString = compose((val) => val.toString(), Math.floor) // 사용법
floorAndToString(121.212121) // '121'
Continuation
프로그램의 특정 지점에서 코드의 실행되지 않은 부분을 계속(Continuation)이라고 합니다.

const printAsString = (num) => console.log(`Given ${num}`)

const addOneAndContinue = (num, cc) => {
  const result = num + 1
  cc(result)
}

addOneAndContinue(2, printAsString) // 'Given 3'
비동기 프로그래밍에서는 프로그램을 계속 진행하기 전에 데이터를 받기 위해 대기해야하는 경우가 종종 있습니다. 응답은 프로그램의 나머지 부분으로 전달되는 경우가 종종 있습니다.

const continueProgramWith = (data) => {
  // 데이터와 함께 프로그램을 계속한다.
}

readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // handle error
    return
  }
  continueProgramWith(response)
})
Purity
반환 값이 입력 값에 의해서만 결정되고 부수효과가 발생하지 않으면 함수는 순수(Purity)합니다.

const greet = (name) => `Hi, ${name}`

greet('Brianne') // 'Hi, Brianne'
다음과 같이:

window.name = 'Brianne'

const greet = () => `Hi, ${window.name}`

greet() // "Hi, Brianne"
위 예제의 출력은 함수 외부에 저장된 데이터를 기반으로 합니다...

let greeting

const greet = (name) => {
  greeting = `Hi, ${name}`
}

greet('Brianne')
greeting // "Hi, Brianne"
... 그리고 이 함수 밖에서 상태를 수정합니다.

Side effects
함수 또는 표현식은 값을 반환하는 것과는 별도로 외부 변경 가능 상태와 상호 작용 (읽기 또는 쓰기)하는 경우 부수효과(Side effects)가 있다고합니다.

const differentEveryTime = new Date()
console.log('IO is a side effect!')
Idempotent
함수에 결과를 다시 적용해도 다른 결과가 나오지 않으면 함수는 멱등성(Idempotent)을 가집니다.

f(f(x)) ≍ f(x)
Math.abs(Math.abs(10))
sort(sort(sort([2, 1])))
Point-Free Style
정의가 사용된 인수를 명시적으로 식별하지 않는 함수 작성 방식입니다. 이 스타일은 일반적으로 currying 또는 다른 Higher-Order functions을 필요로합니다. A.K.A Tacit programming.

// Given
const map = (fn) => (list) => list.map(fn)
const add = (a) => (b) => a + b

// Then

// Not points-free - `numbers` is an explicit argument
const incrementAll = (numbers) => map(add(1))(numbers)

// Points-free - The list is an implicit argument
const incrementAll2 = map(add(1))
incrementAll은 매개 변수 numbers를 사용하고 식별하므로 Point-free 하지 않습니다. incrementAll2는 함수와 값을 결합하여 작성되며 인수에 대해서는 언급하지 않습니다. Point-free합니다.

points-free 함수 정의는 function 또는 => 없이 일반적인 할당처럼 보입니다.

Predicate
술부(Predicate)는 주어진 값에 대해 참 또는 거짓을 리턴하는 함수입니다. 술부(Predicate)의 일반적인 사용은 배열 필터의 콜백입니다.

const predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
Contracts
계약(Contracts)은 런타임에 함수 또는 표현식으로부터 행위의 의무 및 보증을 지정합니다. 이것은 함수 또는 표현식의 입력 및 출력에서 예상되는 일련의 규칙으로 작동하며 일반적으로 계약 위반시 오류가 보고됩니다.

// Define our contract : int -> int
const contract = (input) => {
  if (typeof input === 'number') return true
  throw new Error('Contract violated: expected int -> int')
}

const addOne = (num) => contract(num) && num + 1

addOne(2) // 3
addOne('some string') // Contract violated: expected int -> int
Category
범주 이론의 category는 객체와 객체간의 morphism 모음집입니다. 프로그래밍에서 사용하는 category는 일반적으로 타입을 객체로, 함수를 객체 사이의 morphism 으로 가지는 category입니다.

유효한 Category가 되기 위해서는 3 가지 규칙이 충족되어야합니다:

객체를 그 자체로 매핑하는 identity morphism이 있어야합니다. a가 어떤 Category의 객체인 경우, a -> a의 함수가 있어야합니다.
morphism이 합성되야 합니다. 여기서 a, b 및 c는 어떤 범주의 객체이고, f는 a -> b의 morphism이고 g는 b -> c의 morphism입니다; (g • f)(x)는 g(f(x))와 같아야합니다.
합성은 연관성이 있어야합니다. f • (g • h)는 (f • g) • h와 같아야합니다.
이 규칙은 매우 추상적인 수준에서 합성을 관리하기 때문에 Category 이론은 객체를 합성하는 새로운 방법을 발견하는데 큰 도움이 됩니다.

추가 읽기

Category Theory for Programmers
Value
변수에 할당 할 수 있는 모든 것입니다.

5
Object.freeze({name: 'John', age: 30}) // `freeze` 함수는 불변성을 강제합니다.
;(a) => a
;[1]
undefined
Constant
한 번 할당하면 다시 할당할 수 없는 변수입니다.

const five = 5
const john = Object.freeze({name: 'John', age: 30})
상수는 참조 투명성을 가집니다. 즉, 결과에 영향을 미치지 않고 나타내는 값으로 바꿀 수 있습니다.

위의 두 상수를 사용하면 다음 표현식이 항상 true를 반환합니다.

john.age + five === ({name: 'John', age: 30}).age + (5)
Functor
객체의 각 값을 실행하면서 새로운 객체를 생성하는 동안 map 함수를 구현하는 객체는 두 가지 규칙을 따릅니다 :

신원 보존

object.map(x => x) ≍ object
합성 가능

object.map(compose(f, g)) ≍ object.map(g).map(f)
(임의의 함수 f, g)

자바 스크립트의 Functor인 Array는 두 가지 Functor 규칙을 따릅니다:

;[1, 2, 3].map(x => x) // = [1, 2, 3]
그리고

const f = x => x + 1
const g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]
Pointed Functor
임의 의 단일 값을 넣는 of 함수가 있는 객체입니다 .

ES2015에서 Array를 pointed functor로 만드는 Array.of를 추가했습니다.

Array.of(1) // [1]
Lift
Lifting은 값을 가져 와서 functor와 같은 객체에 넣는 것 입니다. 함수를 Applicative Functor로 들어 올리면 해당 Functor에도 있는 값에 대해 함수를 적용 할 수 있습니다.

일부 구현에서는 lift, liftA2라 불리는 함수가 functor에서 함수를 보다 쉽게 실행하도록 합니다.

const liftA2 = (f) => (a, b) => a.map(f).ap(b) // 이것은 `ap`이지,`map`이 아니라는 것에 주의하십시오.

const mult = a => b => a * b

const liftedMult = liftA2(mult) // 이 함수는 이제 배열과 같은 functor에서 작동합니다.

liftedMult([1, 2], [3]) // [3, 6]
liftA2(a => b => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
map과 같이 하나의 인자를 갖는 함수를 들어 올리고 적용하는 일을 합니다.

const increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
Referential Transparency
프로그램의 동작을 변경하지 않고 값으로 대체 할 수 있는 표현식은 참조투명성(Referential Transparency)을 가진다고 합니다.

우리가 greet 함수를 가졌다고 하면 :

const greet = () => 'Hello World!'
Hello World!를 대체할 수 있는 greet()는 참조투명성(Referential Transparency)입니다.

Equational Reasoning
응용 프로그램이 표현식으로 합성되어 부수효과가 없는 경우 시스템에 대한 진실은 해당 부분에서 파생될 수 있습니다.

Lambda
값처럼 취급 될 수 있는 익명의 함수입니다.

;(function (a) {
  return a + 1
})

;(a) => a + 1
람다는 종종 고차 함수에 인수로 전달됩니다.

;[1, 2].map((a) => a + 1) // [2, 3]
변수에 람다를 할당할 수 있습니다.

const add1 = (a) => a + 1
Lambda Calculus
함수를 사용하여 universal model of computation을 만드는 수학 분야입니다.

Lazy evaluation
게으른 평가(Lazy evaluation)는 값이 필요할 때까지 표현식 평가를 지연시키는 호출 별 평가 메커니즘입니다. 함수형 언어에서는 무한한 목록과 같은 구조가 허용됩니다. 이 구조는 명령 순서 지정이 중요한 명령형 언어에서는 일반적으로 사용할 수 없습니다.

const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
const randIter = rand()
randIter.next() // 각 실행마다 임의의 값이 주어지며 필요에 따라 표현식이 평가됩니다.
Monoid
객체를 같은 타입의 다른 객체와 "결합"하는 기능을 가진 객체입니다.

하나의 간단한 monoid는 숫자 추가입니다 :

1 + 1 // 2
이 경우 number는 객체이며 +는 함수입니다.

값과 결합 될 때 값을 변경하지 않는 "ID"값이 있어야합니다.

추가 할 ID 값은 0입니다.

1 + 0 // 1
또한 작업의 그룹화가 결과 (연관성)에 영향을 미치지 않아야합니다 :

1 + (2 + 3) === (1 + 2) + 3 // true
배열 연결은 또한 monoid를 형성합니다 :

;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
아이디 값은 빈 배열 []입니다 :

;[1, 2].concat([]) // [1, 2]
identity와 compose 함수가 제공되면, 함수 자체는 monoid를 형성합니다 :

const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
foo는 하나의 인수를 취하는 함수입니다.

compose(foo, identity) ≍ compose(identity, foo) ≍ foo
Monad
Monad는 어떤 타입과 그 타입에 적용할 수 있는 of 와 chain 함수입니다. chain은 중첩된 결과를 중첩되지 않게 하는것을 제외하고 map와 비슷합니다.

// 구현
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// 사용법
Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// map과 비교
Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
of는 다른 함수형 언어에서 return이라고도 합니다.

chain은 다른 언어에서flatmap 과 bind라고도 합니다.

Comonad
extract 와 extend 함수를 가진 객체입니다.

const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
Extract은 functor에서 값을 취합니다.

CoIdentity(1).extract() // 1
Extend는 comonad에서 함수를 실행합니다. 이 함수는 comonad와 동일한 타입을 반환해야 합니다.

CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
Applicative Functor
Applicative Functor는 ap 함수를 가진 객체입니다. ap은 객체의 함수를 같은 타입의 다른 객체의 값에 적용합니다.

// 구현 
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// 예제 사용법
;[(a) => a + 1].ap([1]) // [2]
이는 두 개의 객체가 있고 그 내용에 이진 함수를 적용하려는 경우에 유용합니다.

// 결합하려는 배열 
const arg1 = [1, 3]
const arg2 = [4, 5]

//결합 기능 - 이것이 작동하려면 curry해야합니다.
const add = (x) => (y) => x + y

const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
이것은 결과를 얻기 위해 ap 라고 부를 수있는 배열을 제공합니다 :

partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
Morphism
변환 함수입니다.

Endomorphism
입력 타입이 출력과 동일한 함수입니다.

// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
Isomorphism
구조적으로 자연스럽고 데이터가 손실되지 않는 두 가지 타입 객체간의 변환쌍입니다.

예를 들어, 2D 좌표는 배열 [2,3]또는 객체 {x: 2, y: 3}로 저장 될 수 있습니다.

// 양방향으로 변환 할 함수를 제공하면 isomorphic이 됩니다.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
Setoid
동일한 타입의 다른 객체를 비교하는데 사용할 수 있는 equals함수가 있는 객체입니다.

배열을 Setoid로 만듭니다:

Array.prototype.equals = function (arr) {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
Semigroup
동일한 타입의 다른 객체와 결합하는 concat 함수가 있는 객체입니다.

;[1].concat([2]) // [1, 2]
Foldable
객체를 다른 타입으로 변환 할 수있는 reduce 함수가 있는 객체입니다.

const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
Lens
Lens는 다른 데이터 구조를 위해 getter와 non-mutating setter를 쌍으로 만드는 구조체 (종종 객체 또는 함수)입니다.

// Using [Ramda's lens](http://ramdajs.com/docs/#lens)
const nameLens = R.lens(
  //객체의 name 속성에 대한 getter
  (obj) => obj.name,
  // 이름 속성에 대한 setter
  (val, obj) => Object.assign({}, obj, {name: val})
)
주어진 데이터 구조에 대해 get과 set의 쌍을 사용하면 몇 가지 주요 기능을 사용할 수 있습니다.

const person = {name: 'Gertrude Blanch'}

// getter를 호출한다.
R.view(nameLens, person) // 'Gertrude Blanch'

// setter를 호출한다.
R.set(nameLens, 'Shafi Goldwasser', person) // {name: 'Shafi Goldwasser'}

// 구조체의 값에 함수를 실행한다.
R.over(nameLens, uppercase, person) // {name: 'GERTRUDE BLANCH'}
Lens도 합성 가능합니다. 따라서 중첩된 데이터를 쉽게 변경할 수 있습니다.

// 이 Lens는 비어있지 않은 배열의 첫 번째 항목에 초점을 맞춥니다.
const firstLens = R.lens(
  // 배열의 첫 번째 항목 가져 오기
  xs => xs[0],
  // 배열의 첫 번째 항목에 대한 비 변형 setter
  (val, [__, ...xs]) => [val, ...xs]
)

const people = [{name: 'Gertrude Blanch'}, {name: 'Shafi Goldwasser'}]

// 당신이 가정할 수도 있겠지만, Lens는 왼쪽에서 오른쪽으로 합성됩니다.
R.over(compose(firstLens, nameLens), uppercase, people) // [{'name': 'GERTRUDE BLANCH'}, {'name': 'Shafi Goldwasser'}]
다른 구현:

partial.lenses - Tasty syntax sugar and a lot of powerful features
nanoscope - Fluent-interface
Type Signatures
종종 JavaScript의 함수에는 인수의 타입과 반환 값을 나타내는 주석이 포함됩니다.

커뮤니티 전반에 걸쳐 약간의 차이가 있지만 다음 패턴을 자주 따르는 경우가 많습니다:

// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
함수가 인수로 다른 함수를 받아들이면 괄호 안에 싸여 있습니다.

// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
a,b, c,d는 인수가 어떤 타입이든 될 수 있음을 나타내기 위해 사용됩니다. 다음 버전의 map은 어떤 타입의 값을 다른 타입인 b 타입으로 변환하는 함수를 취합니다.이 타입은 a 타입의 값의 배열이고, b 타입의 값의 배열을 반환합니다.

// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
추가 읽기

Ramda's type signatures
Mostly Adequate Guide
What is Hindley-Milner? on Stack Overflow
Algebraic data type
다른 타입을 조합하여 만든 복합 타입입니다. 대수타입의 두가지 일반적인 클래스는 sum과 product입니다.

Sum type
합계 타입(Sum type)은 두 타입의 결합으로 다른 타입으로 결합됩니다. 결과 타입에서 가능한 값의 수는 입력 타입의 합이기 때문에 sum이라고 합니다.

JavaScript에는 이와 같은 타입이 없지만 Set을 사용하여 가장 할 수 있습니다:

// 여기에는 Set가 아니라 이 값만 가질 수있다고 상상해보세요.
const bools = new Set([true, false])
const halfTrue = new Set(['half-true'])

// weakLogic 타입에는 bools과 halfTrue의 값의 sum이 들어 있습니다.
const weakLogicValues = new Set([...bools, ...halfTrue])
합계 타입(Sum type)은 때때로 합집합 타입, 차별화된 합집합 또는 태그가 지정된 합집합이라고 합니다.

JS에는 결합 타입을 정의하고 사용하는데 도움이 되는 couple libraries가 있습니다.

Flow의 union types과 TypeScript의 Enums이 이와 같은 역할을 합니다.

Product type
Product type은 타입을 더 잘 알고있는 방식으로 결합합니다.

// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({ x, y })
데이터 구조의 총 가능한 값은 다른 값의 결과이므로 제품이라고합니다. 많은 언어에는 제품 타입의 가장 간단한 형식인 튜플 타입이 있습니다.

Set theory을 참조하세요.

Option
Option은 Some과 None으로 불리는 sum type입니다.

Option은 값을 반환하지 않는 함수를 작성하는 데 유용합니다.

// Naive definition

const Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

const None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Option a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
Chain을 Option을 반환하는 시퀀스 함수에 사용하세요.

// getItem :: Cart -> Option CartItem
const getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Option Number
const getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Option a
const getNestedPrice = (cart) => getItem(obj).chain(getPrice)

getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
Option은 Maybe라고도합니다. Some은 때때로 Just라고 부릅니다. None은 Nothing이라고도 합니다.

Functional Programming Libraries in JavaScript
mori
Immutable
Ramda
ramda-adjunct
Folktale
monet.js
lodash
Underscore.js
Lazy.js
maryamyriameliamurphies.js
Haskell in ES6
Sanctuary
