---
layout: single
title: "함수와 일급 객체, 프로토타입"
categories: STUDY4242
classes: wide
---

## 18장 함수와 일급 객체

### 1. 일급 객체의 조건

1. 무명의 리터럴로 생성할 수 있다. 즉, 변수를 만드는 것처럼 런타임에 생성이 가능하다.
2. 변수나 자료 구조(객체, 배열 등)에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다. 즉, 함수의 인자로 사용된다.
4. 함수의 반환값(결과)으로 사용할 수 있다.

자바스크립트의 함수는 위의 조건을 모두 만족하므로 일급 객체다. <br>
따라서 함수를 객체와 동일하게 사용할 수 있으며, 더 나아가 값으로도 다룰 수 있다. <br>
함수는 값을 사용할 수 있는 곳(변수 할당문, 객체의 프로퍼티 값, 배열의 요소, 함수 호출의 인수, 함수 반환문)이라면  <br>
어디서든지 리터럴로 정의할 수 있으며 런타임에 함수 객체로 평가된다.

```
<script>
const add2 = a => a + 2; // (1)
log(add2); // (2)

// (3)
const f = () => () => 1;
log(f());

// (4)
const f2 = f();
log(f2());
</script>
```

1. 변수 `add2`에 익명함수 `a=>a+2`를 담을 수 있다. <br>
2. 변수 `add2`에 담긴 함수를 다른 함수인 `log`의 인자로 전달할 수 있다.
   즉, 함수는 다른 함수나 메서드의 인자로 사용될 수 있다. <br>
3. `f` 함수를 실행하면 결과로 또 다른 함수를 반환한다. <br>
4. `f()`를 호출하여 반환된 함수를 변수 `f2`에 할당하고, 변수에 담겨진 함수는 메서드에 담겨 인자로 사용될 수 있다. <br>

<br>
함수는 객체이지만 일반 객체와는 차이가 있다. <br>
가령 일반 객체는 호출할 수 없지만, **함수 객체는 호출할 수 있다**는 점이 그렇다. <br>
또한, 함수 객체는 일반 객체에는 없는 **함수 고유의 프로퍼티를 소유**한다.

### 2. 함수 객체의 프로퍼티

브라우저 콘솔에서 `console.dir` 메서드를 사용하면 함수 객체의 내부를 볼 수 있다. <br>
또한, `Object.getOwnPropertyDescriptors` 메서드를 통해 `square` 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트를 확인할 수 있다.
<br>

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/a8079b70-fa7b-46e0-a91d-3a74b96f2d14)

`arguments`, `caller`, `length`, `name`, `prototype` 프로퍼티는 모두 일반 객체에는 없는 함수 객체 고유의 데이터 프로퍼티다. <br>
하지만 `__proto__`는 접근자 프로퍼티이며, 함수 객체 고유의 프로퍼티가 아니라 Object.prototype 객체의 프로퍼티를 상속받은 것이다.<br>
해당 객체의 프로퍼티는 모든 객체가 상속받아 사용할 수 있으며 이에 관해서는 뒤에서 더 자세히 알아보고자 한다. <br>
일단 함수 객체의 프로퍼티에 대해 하나씩 살펴보고자 한다.
<br>

### 2-1. arguments 프로퍼티

함수 객체의 arguments 프로퍼티 값은  arguments 객체다.<br>
**arguments 객체는 함수 호출 시 전달된 인수(argument)들의 정보를 담고 있는 순회 가능한 유사 배열 객체**이며  <br>
함수 내부에서 지역 변수처럼 사용되기 때문에 함수 외부에서는 참조할 수 없다. <br>
 <br>
arguments 프로퍼티는 ES3부터 표준에서 폐지되었기 때문에 Function.arguments와 같은 사용법은 권장되지 않으며 <br>
함수 내부에서 지역 변수처럼 사용할 수 있는 arguments 객체를 참조하도록 한다. <br>
<br>
**자바스크립트는 함수의 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.** <br>
 <br>
따라서 함수 호출 시 매개변수 개수만큼 인수를 전달하지 않아도 에러가 발생하지 않는다! <br>
<br>
함수를 정의할 때 선언한 매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다. <br>
즉, 함수가 호출되면 함수 몸체 내에서 암묵적으로 매개변수가 선언되고 undefined로 초기화된 후 인수가 할당된다. <br>
 <br>
따라서 매개변수의 개수보다 인수를 적게 전달해도 인수가 전달되지 않은 매개변수는 undefined로 초기화된 상태를 유지하는 셈이다. <br>
반대로, 매개변수의 개수보다 인수를 더 많이 전달하면 초과된 인수는 그냥 무시된다.  <br>
그러나 초과된 인수가 버려지는 것은 아니다. <br>
모든 인수는 암묵적으로 arguments 객체의 프로퍼티로 보관되기 때문이다. <br>
<br>
아래 예시를 통해 간단하게 알아보고자 한다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/0485dfef-a506-46f4-87d3-f2e735e2e339)

arguments 객체는 인수를 프로퍼티 값으로 소유하며, 프로퍼티 키는 인수의 순서를 나타낸다. <br>
arguments 객체의 callee 프로퍼티는 호출되어 arguments 객체를 생성한 함수(= 함수 자신)을 가리키고, length 프로퍼티는 인수의 개수를 가리킨다. <br>
Symbol(Symbol.iterator) 프로퍼티는 arguments 객체를 순회 가능한 자료구조인 iterable로 만들기위한 프로퍼티다. <br>
해당 프로퍼티를 키로 사용한 메서드를 구현하는 것에 의해 iterable이 된다. <br>
<br>
위 예제에서 그랬듯이, 자바스크립트는 선언된 매개변수의 개수와 인수의 개수를 확인하지 않는다. <br>
때문에, **함수가 호출되면 인수 개수를 확인하고 이에 따라 함수의 동작을 달리 정의할 필요**가 있을 수도 있다. <br>
그리고 이때 사용하는 것이 **arguments 객체**이다! <br>
<br>
arguments 객체는 매개변수 개수를 확정할 수 없고, 매개 변수의 개수가 변할 수 있게 하는 **가변 인자 함수**를 구현할 때 유용하다. <br>

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/f75d3664-8674-40ad-be74-d4ab5bf1f8ed)

arguments 객체는 배열 형태로 인자 정보를 담고 있으나 실제 배열이 아닌 **유사 배열 객체**(array-like object)다. <br>
유사 배열 객체란 length 프로퍼티를 가진 객체로, 이를 통해 for 문으로 순회를 가능하게 하는 객체이다. <br>
따라서 유사 배열 객체는 배열 메서드를 사용할 경우 에러가 발생한다. <br>
번거로운 과정이긴 하지마는 배열 메서드를 사용하기 위해서는 `Function.prototype.call`, `Function.prototype.apply`를 사용하여 간접 호출이 가능하긴 하다. <br>

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/c3f83a15-e86a-4a2b-8358-66cde0c7a7c6)

ES6에서는 Rest 파라미터를 도입함으로써 번거로움을 해결하였다. <br>
간단하게 말하자면 Rest 파라미터는 '...' 구문을 사용하여 함수 매개변수를 선언할 때 나타내는 자바스크립트의 기능이다. <br>
예를 들어, 사용자가 입력한 수의 합계를 계산하는 함수를 정의할 때, 일반적으로는 정해진 개수의 매개변수를 받는 함수를 선언하여 매개변수를 개별적으로 지정한다. <br>
그러나 Rest 파라미터를 사용하면 유연하게 임의의 개수의 매개변수를 수용할 수 있게 된다! <br>
위 코드에서는 `args`를 배열로 처리하여, 해당 배열에 인수를 할당하도록 한다. 그런 후, `reduce()`를 통해 합계를 계산한다. <br>

### 2-2. caller 프로퍼티

`caller` 프로퍼티는 ECMAScript 사양에 포함되지 않은 **비표준 프로퍼티**이다. <br>
따라서 짧게 훑고 넘어가자면, 함수 객체의 caller 프로퍼티는 **함수 자신을 호출한 함수**를 가리킨다. <br>

### 2-3. length 프로퍼티

`length` 프로퍼티는 **함수를 정의할 때 선언한 매개변수의 개수**를 가리킨다. <br>
다만, `arguments` 객체의 `length` 프로퍼티와 함수 객체의 `length` 프로퍼티의 값은 다를 수도 있다. <br>
`arguments` 객체의 `length` 프로퍼티는 인자(argument)의 개수를 가리키고, 함수 객체의 `length` 프로퍼티는 매개변수(paramenter)의 개수를 가리키기 때문이다. <br>

### 2-4. name 프로퍼티

함수 객체의 `name` 프로퍼티는 함수 이름을 나타낸다. <br>
여담으로, ES6에서 정식 표준이 된 프로퍼티이다. 따라서 ES5와 ES6에서 동작을 달리한다. <br>
예를 들어, 익명 함수 표현식에서 ES5는의 `name` 프로퍼티는 빈 문자열을 값으로 가진다.  <br>
그러나 ES6에서는 함수 객체를 가리키는 식별자를 값으로 가진다. 

```
var anonymousFunc = function() {};
console.log(anonymousFunc.name);
```

위 코드에서 ES5의 경우, name 프로퍼티는 빈 문자열을 값으로 가진다. <br>
ES6의 경우, 함수 객체를 가리키는 변수 이름, 즉, anonymousFunc를 값으로 가진다. <br>
추가로, 함수 이름과 함수 객체를 가리키는 식별자는 의미가 다르다. <br>
함수를 호출할 때는 함수 이름이 아닌 함수 객체를 가리키는 식별자로 호출한다. <br>

### 2-5. __proto__  접근자 프로퍼티

모든 객체는 [[prototype]]이라는 내부 슬롯을 가지며, [[prototype]] 내부 슬롯은 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체를 가리킨다. <br>
프로토타입 객체에 대해서는 이후에 더 자세히 알아보고자 한다. <br>
`__proto__` 프로퍼티는 [[prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티이다. <br>
해당 접근자 프로퍼티를 통해 간접적으로 프로토타입 객체에 접근할 수 있다.

> **내부 슬롯** : 자바스크립트 엔진의 내부 동작(구현 알고리즘)을 설명하기 위해 사용하는 의사 프로퍼티(pseudo property)이다. <br>
> 즉, ECMAScript 문서에서 자바스크립트 내부 동작을 설명하기 위해 정의해 놓은 가상 메소드이다. <br>
> 자바스크립트 엔진의 내부 로직이기 때문에 원칙적으로는 직접 접근하거나 호출할 수 있는 방법을 제공하지 않는다. <br>
> 다만, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공한다.

### 2-6. prototype 프로퍼티

`prototype` 프로퍼티는 생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor(생성자)만이 소유하는 프로퍼티다. <br>
따라서 일반 객체와 생성자 함수로 호출할 수 없는 non-constructor에는 `prototype` 프로퍼티가 없다. <br>
`(function(){}).hasOwnProperty('prototype');` <br>
위 코드에서, 함수 객체는 `prototype` 프로퍼티를 갖고 있기 때문에 true를 반환한다. <br>
따라서 `prototype` 프로퍼티는 함수가 객체를 생성하는 생성자 함수로 호출될 때, 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다.

> **생성자** <br>
> 정해진 key와 value를 가진 객체를 편리하게 생성할 수 있게 도와주는 기계와 같은 역할을 수행한다. <br>
> 비슷한 객체들을 여러개 생성하기 위해서 생성할때마다 key, value 값을 일일이 입력해서 만드는 것이 아니라 <br>
> 하나의 생성자를 만들어놓으면 그 생성자를 이용하여 간편하게 해당 객체(인스턴스)를 생성할 수 있게 해준다.

<hr>
<hr>

## 19장 프로토타입

자바스크립트는 프로토타입 기반의 객체지향 프로그래밍 언어고, 자바스크립트를 이루고 있는 거의 모든 것이 객체이다. <br>
원시 타입의 값(숫자, 문자열, 불린 등)을 제외한 나머지 값들(함수, 배열, 정규 표현식 등)은 모두 객체다. <br>
일단 객체지향 프로그래밍에 대해 간단히 알아보고자 한다.

### 1. 객체지향 프로그래밍

객체지향 프로그래밍은 여러 개의 독립적 단위, 즉 객체(Object)의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다. <br>
이는 실체(사물이나 개념)을 인식하는 사고를 프로그래밍에 접목하려는 시도에서 시작한다. <br>
실체는 특징이나 성질 등을 나타내는 **속성 (attribute/property)**을 가지고 있고, 이를 통해 실체를 인식하거나 구별할 수 있다. <br>
그리고 다양한 속성들 중, 구현하려는 프로그램에서 필요한 속성만을 간추려 내어 표현하는 것을 **추상화 (abstraction)**라고 한다. <br>
 <br>
정리하자면, **속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조**를 객체라고 하고, <br>
객체지향 프로그래밍은 독립적인 객체의 집합으로 프로그램을 표현하려는프로그래밍 패러다임인 셈이다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/6466acd2-d1ec-4bd6-b503-1bf92ceb8a70)

위 코드는 원(circle)이라는 개념을 객체로 만들고자 하는 시도이다. <br>
반지름(radius)라는 속성이 있으며, 이는 원의 상태를 나타내는 데이터이다. <br>
이 반지름을 가지고 넓이를 구할 수 있다.(getArea()). 이는 동작이라고 부른다. <br>
이처럼 객체지향 프로그래밍은 객체의 **상태**(state)를 나타내는 데이터와 이 상태 데이터를 조작할 수 있는 **동작**(behavior)을 하나의 논리적인 단위로 묶어 생각한다. <br>
 <br>
따라서 **객체는 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조**이다! <br>
이때 객체의 상태 데이터를 **프로퍼티**(property), 동작을 **메서드**(method)라고 부른다. <br>
각 객체는 고유의 기능을 갖는 독립적인 부품이기도 하나, 고유한 기능을 수행하면서 다른 객체와 관계성(relationship)을 가질 수도 있으며 다른 객체의 상태 데이터나 동작을 상속받아 사용하기도 한다.

### 2. 상속과 프로토타입

상속(inheritance)은 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다. <br>
자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다. <br>

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/41e02275-058f-4031-a188-8a916377faed)

위 코드에서 `Circle` 생성자 함수가 생성하는 모든 객체(인스턴스)는 프로퍼티 `radius`와 메서드 `getArea`를 가진다. <br>
여기서 메서드 `getArea`의 경우 모든 인스턴스가 동일한 내용의 메서드를 사용하기 때문에 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다. <br>
그러나 현재는 `Circle` 생성자 함수가 인스턴스를 생성할 때마다 getArea 메서드를 중복 생성하고, 모든 인스턴스가 중복 소유한다. <br>
 <br>
이처럼 동일한 생성자 함수에 의해 생성한 모든 인스턴스가 동일한 메서드를 중복 소유하는 것은 메모리를 불필요하게 낭비한다. <br>
자바스크립트는 프로토타입을 기반으로 상속을 구현하기 때문에 불필요한 중복이라는 문제점을 해결해낼 수 있다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/d65cfd28-3e67-4472-8a71-4a87d5647890)

위 코드에서는 `Circle` 생성자 함수가 생성한 모든 인스턴스가 `getArea` 메서드를 공유해서 사용할 수 있도록 프로토타입에 추가하였다. <br>
이때, 프로토타입은 `Circle` 생성자 함수의 `prototype` 프로퍼티에 바인딩되어있다. <br>
이후 `Circle` 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는 프로토타입 `Circle.prototype`으로부터 메서드 `getArea`를 상속받는다. <br>
 <br>
즉, `Circle` 생성자 함수가 생성하는 모든 인스턴스는 하나의 메서드 `getArea`를 공유한다. <br>
결론적으로 `Circle` 생성자가 생성하는 모든 인스턴스는 자신의 상태를 나타내는 `radius` 프로퍼티만 개별적으로 소유하고, 내용이 동일한 메서드는 상속을 통해 공유하여 사용하는 것이다.

### 3. 프로토타입 객체

프로토타입 객체(또는 프로토타입)는 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위하여 사용된다. <br>
프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서, 다른 객체에 메서드를 포함한 공유 프로퍼티를 제공한다. <br>
따라서 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용할 수 있다. <br>
 <br>
모든 객체는 [[Prototype]]이라는 내부 슬롯을 가진다. 이 내부 슬롯의 값은 프로토타입의 참조이며 `null`인 경우도 있다. <br>
[[Prototype]]에 저장되는 프로토타입은 객체 생성 방식(객체 리터럴, 생성자 함수, `Object.create()` 메서드 등)에 의해 결정된다. <br>
 <br>
객체 생성 방식에 따라 프로토타입이 결정되므로, 동일한 생성 방식으로 생성된 객체는 동일한 프로토타입을 가질 수 있다. <br>
모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다. <br>
 <br>
즉, 객체와 프로토타입과 생성자 함수는 모두 서로 연결되어 있다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/d6244aa4-15ac-41c4-8d39-8ea930978425)

`[[Prototype]]` 내부 슬롯에 직접 접근하는 것은 어렵지만, 위 그림처럼 `__protot__`  접근자 프로퍼티를 통해 <br>
자신의 프로토타입(= 자신의 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입)에 간접적으로 접근할 수 있다. <br>
그리고 프로토타입은 자신의 `cosntructor` 프로퍼티를 통해 생성자 함수에 접근할 수 있고,  <br>
생성자 함수는 자신의 `prototype` 프로퍼티를 통해 프로토타입에 접근할 수 있다.

### 3-1. `__proto__` 접근자 프로퍼티

**모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 `[[Prototype]]` 내부 슬롯에 간접적으로 접근할 수 있다.**

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/05d8ab37-6415-4bfc-a9d3-f6655dceec25)

위 그림에서, `name: Lee`가 `person` 객체의 프로퍼티며, `[[Prototype]] : Object`부터의 내용은 `person` 객체의 프로토타입인 `Object.prototype`이다. <br>
이는 `person` 객체의 `[[Prototype]]` 내부 슬롯이 가리키는 객체인 `Object.prototype`에 접근한 결과를 크롬 브라우저가 콘솔에 표시한 것이다. <br>
이처럼 모든 객체는 `__proto__` 접근자 프로퍼티를 통해 프로토타입을 가리키는 `[[Prototype]]` 내부 슬롯에 접근할 수 있다.

>`[[Prototype]]`과 `__proto__`는 모두 객체의 프로토타입을 가리키는 값이다. 동일한 개념을 가리키 표기 방식에 차이가 있다.
>여러 자바스크립트 환경에서는 `__proto__`를 사용하여 객체의 프로토타입에 접근할 수 있다. <br>
다만, 모든 자바스크립트 엔진에서 지원되지는 않을 수 있고, 특정 환경에서는 `[[Prototype]]`을 확인할 수 있을 수도 있다.

>**프로퍼티의 종류** <br>
>**데이터 프로퍼티** : 값을 저장하기 위해 일반적으로 사용하는 프로퍼티 <br>
>**접근자 프로퍼티** : 값이 없으며 프로퍼티를 읽거나 쓸 때 호출하는 함수를 지정할 수 있는 프로퍼티로 <br>
>값을 획득(get, getter 메서드)하고 설정(set, setter 메서)하는 역할을 담당한다.
<br>

**`__proto__`는 접근자 프로퍼티다.**
그리고 내부 슬롯은 프로퍼티가 아니다. <br>
`[[Prototype]]` 내부 슬롯에 직접 접근할 수 없기 때문에 `__proto__` 접근자 프로퍼티를 사용하여 간접적으로 `[[Prototype]]` 내부 슬롯의 값 프로토타입에 접근한다. <br>
접근자 프로퍼티는 자체적으로 값([[Value]] 프로퍼티 어트리뷰트)를 갖지 않고,  <br>
다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수(accesor function), 즉 `[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰트로 구성된 프로퍼티다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/4d86120d-282c-4112-b045-82a1905142f7)

`Object.prototype`의 접근자 프로퍼티인 `__proto__`는 getter, setter 함수라고 부르는 접근자 함수(`[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰트에 할당된 함수)를 통해 `[[Prototype]]` 내부 슬롯의 값인 프로토타입을 취득하거나 할당한다. <br>
`__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하면 내부적으로 getter 함수인 `[[Get]]`이 호출된다. <br>
`__proto__` 접근자 프로퍼티를 통해 새로운 프로토타입을 할당하면 setter 함수인 `[[Set]]`이 호출된다.
<br>

**`__proto__` 접근자 프로퍼티는 상속을 통해 사용된다.** <br>
`__proto__` 접근자 프로퍼티는 `Object.prototype`의 프로퍼티기 때문에 모든 객체는 상속을 통해 `Object.prototype.__proto__` 접근자 프로퍼티를 사용할 수 있다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/fc34e80f-3847-4e12-9e28-c93c1d36551d)

<br>

**`__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유** 

이는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/8a01ec13-b742-4e9c-b825-0657677b2a74)

위 예제에서 `child` 객체의 프로토타입을 `parent` 객체로 설정한 후 `parent` 객체의 프로토타입을 `child` 객체로 설정하였다. <br>
이는 서로가 자신의 프로토타입이 되는 순환 참조하는 프로토타입 체인이 만들어지기 때문에 `__proto__` 접근자 프로퍼티는 에러를 발생시킨다. <br>
프로토타입 체인은 단방향 링크드 리스트로만 구현되어야 한다. 즉, 프로퍼티 검색 방향이 한쪽 방향으로만 흘러가야만 한다. <br>
 <br>
위 예제에서는 프로토타입 체인 종점이 존재하지 않기 때문에, 프로퍼티를 검색할 때 무한 루프에 빠진다. <br>
따라서 무조건적으로 프로토타입을 교체할 수 없도록 `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하고 교체하도록 구현되어 있다.

<br>

**`__proto__` 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.** 

이는 ES5까지 ESCMAScript 사양에 포함되지 않은 비표준이었기 때문이다. <br>
물론 현재는 대부분 브라우저가 `__proto__`를 지원하기는 한다. <br>
그러나 모든 객체가 `__proto__` 접근자 프로퍼티를 사용할 수 있는 것 또한 아니다. <br>
 <br>
가령, 직접 상속을 통해 `Object.prototype`을 상속받지 않는 객체를 생성한 경우,  `__proto__` 접근자 프로퍼티를 사용할 수 없다. <br>
위와 같은 경우, `Object.getPrototyoeOf`, `Object.setPrototypeOf` 메서드 등을 사용한다.

### 3-2. 함수 객체의 prototype 프로퍼티

**함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.**

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/fb87729d-9ef4-461f-b9e9-42d3d8477644)

함수 객체는 prototype 프로퍼티를 소유하지만, 일반 객체는 소유하지 않는다. <br>
이는 생성자 함수로서 호출할 수 없는 함수, 즉 non-constructor인 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않으며 프로토타입 또한 생성하지 않기 때문이다. <br>
생성자 함수로 호출하기 위해 정의하지 않은 일반 함수(함수 선언문, 함수 표현식)도 prototype 프로퍼티를 소유하지만 객체를 생성하지 않는 일반 함수의 prototype 프로퍼티는 아무런 의미가 없다. <br>
모든 객체가 가지고 있는, `Object.prototype`으로부터 상속받은 `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 결국 동일한 프로토타입을 가진다. <br>
하지만 사용하는 주체가 다르다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/13d6ed8f-0967-49af-b819-4a381b670e8d)

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/3602da89-bbac-4122-9284-a0bee006e0d3)

생성자 함수로 객체를 생성한 후, `__proto__` 접근자 프로퍼티와 prototype 프로퍼티로 프로토타입 객체에 접근하면, 결국 동일한 프로토타입을 가리킨다는 것을 알 수 있다.

### 3-3. 프로토타입의 constructor 프로퍼티와 생성자 함수

모든 프로토타입은 constructor 프로퍼티를 가진다. <br>
이 **constructor 프로퍼티는 prototype 프로퍼티로 자신을 참조하고 있는 생성자 함수**를 가리킨다. <br>
이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이루어진다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/1d0dda1c-4a9a-4327-9190-7abfe883a6a5)

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/0c197d4e-dbd2-43ac-9c89-16c22d0cdac2)

위 예제에서 Person 생성자 함수는 객체 me를 생성했다. me 객체는 프로토타입의 constructor 프로퍼티를 통해 생성자 함수와 연결된다. <br>
me 객체에는 constructor 프로퍼티가 없지만, me 객체의 프로토타입인 Person.prototype에는 constructor 프로퍼티가 있다. <br>
 <br>
따라서 me 객체는 프로토타입인 Person.prototype의 constructor 프로퍼티를 상속받아 사용한다. <br>
이때 constructor 프로퍼티가 가리키는 생성자 함수는 인스턴스를 생성한 생성자 함수이다.

## 4. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

리터럴 표기법에 의한 객체 생성 방식과 같이 명시적으로 new 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식 또한 있다. <br>
가령 객체 리터럴, 함수 리터럴, 배열 리터럴, 정규 표현식 리터럴 등이 그렇다. <br>
리터럴 표기법에 의해 생성된 객체도 물론 프로토타입이 존재한다. <br>
 <br>
그러나 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 함수라고 단정할 수 없다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/7791041b-757b-41f9-a3a6-a1b073125e84)

위 코드에서 obj 객체는 Object 생성자 함수가 아니라 객체 리터럴로 생성한 객체이다. <br>
그러나 obj 객체의 생성자 함수는 Object 생성자 함수라는 true가 반환된다. <br>
Object 생성자 함수에 인수를 전달하지 않거나 undefined 또는 null을 인수로 전달하면서 호출하면, <br>
내부적으로 추상 연산 OrdinaryObjectCreate를 호출하여 Object.prototype을 프로토타입으로 갖는 빈 객체를 생성하기 때문이다.

> **추상 연산** (abstract operation)
> ESCMAScript 사양에서 내부 동작의 구현 알고리즘을 표현한 것으로 설명을 위해 사용되는 함수와 유사한 의사 코드

```
// 2. Object 생성자 함수에 의한 객체 생성
// 인수 전달 X -> 추상 연산 OrdinaryObjectCreate를 호출 -> 빈 객체 생성
let obj = new Object();
console.log(obj);

// 1. new.target이 undefined or Object가 아닌 경우
// 프로토타입 체인 생성 (인스턴스 -> Foo.prototype -> Object.prototype)
class Foo extends Object {}
new Foo();

// 3. 인수 전달 O -> 인수를 객체로 변환
obj = new Object(123);
console.log(obj); // Number {123}
obj = new Object('123');
console.log(obj); // String {"123"}
```

객체 리터럴이 평가될 때 또한 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성하고 프로퍼티를 추가한다. <br>
그러나 new.target의 확인, 프로퍼티 추가 등 세부 내용은 다르다. <br>
따라서 객체 리터럴에 생성된 객체는 Object 생성자 함수가 직접적으로 생성한 객체가 아니다. <br>
그러나 리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다. <br>
 <br>
따라서 자바스크립트 엔진 내부에서 처리될 때, 객체를 다루기 위해 내부적으로 프로토타입을 설정하여 가상적인 생성자 함수를 갖는다. <br>
빈 객체를 생성하고, 해당 객체의 프로토타입을 `Object.prototype`으로 설정하는 과정을 거치기 때문이다.<br>
 <br>
따라서 객체 리터럴로 생성된 객체도 마치 `Object` 생성자 함수에 의해 생성된 것처럼 동작하며,  <br>
프로토타입 체인에서 `Object.prototype`에 정의된 메서드나 프로퍼티를 상속받을 수 있다. <br>
따라서 프로토타입의 constructor 프로퍼티를 통해 연결되어 있는 생성자 함수를 리터럴 표기법을 생성한 객체를 생성한 생성자 함수로 생각해도 크게 무리는 없다. <br>
리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입은 다음과 같다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/949e7bb4-1e96-493b-b9a1-56c30ef94231)

그리고 **프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다**는 것을 기억해야 한다. <br>
프로토타입은 생성자 함수와 더불어 생성되며 prototype, constructor 프로퍼티에 의해 연결되어있다. <br>
프로토타입 객체는 constructor 프로퍼티를 통해 자신을 생성한 생성자 함수를 참조할 수 있기 때문이다.<br>

>**new.target 속성** : 함수 또는 생성자가 연산자를 사용하여 호출되었는지 감지할 수 있다.
> new와 함께 호출한 경우에는 함수 자체를 반환하며 일반 함수 호출에서는 undefined를 반환한다.

## 5. 프로토타입의 생성 시점

**프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성**된다.  <br>
프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다. <br>
생성자 함수는 사용자가 직접 정의한 사용자 정의 생성자 함수와 자바스크립트가 기본 제공하는 빌트인 생성자 함수로 구분할 수 있다.

### 5-1. 사용자 정의 생성자 함수와 프로토타입 생성 시점

내부 메서드 [[Construct]]를 갖는 함수 객체, 즉 화살표 함수나 ES6의 메서드 축약 표현으로 정의하지 않은 일반 함수(함수 선언문, 함수 표현식)로 정의한 함수 객체는 new 연산자와 함께 생성자 함수로서 호출할 수 있다. <br>
**생성자 함수로서 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.** <br>
함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. <br>
 <br>
따라서 함수 선언문으로 정의된 생성주 함수 또한 우선 평가되어 함수 객체가 되고 프로토타입 또한 더불어 생성된다. <br>
생성된 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되며 오직 constructor 프로퍼티만을 갖는 객체이다. <br>
프로토타입도 객체고, 모든 객체는 프로토타입을 가지므로 프로토타입 또한 자신의 프로토타입을 가진다. <br>
생성된 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

### 5-2. 빌트인 생성자 함수와 프로토타입 생성 시점

Object, String, Number, Function, Array, Date 등과 같은 빌트인 생성자 함수 또한 일반 함수와 마찬가지로 함수가 생성되는 시점에 프로토타입이 생성된다. <br>
생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩된다. <br>
이후 **생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당**된다. <br>
이로써 생성된 객체는 프로토타입을 상속받는다.

## 6. 객체 생성 방식과 프로토타입의 결정

객체는 객체 리터럴, Object 생성자 함수, 생성자 함수, Object.create 메서드, 클래스(ES6) 등의 방법으로 생성할 수 있다. <br>
각 방식마다 세부적인 객체 생성 방식의 차이는 있으나 추상 연산 OrdinaryObjectCreate에 의해 생성된다는 공통점이 있다. <br>
 <br>
추상 연산 OrdinaryObjectCreate는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달받는다.  <br>
그리고 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달할 수 있다. <br>
추상 연산 OrdinaryObjectCreate은 빈 객체를 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우에는 프로퍼티를 객체에 추가한다. <br>
이후, 자신이 생성한 객체의 [[Prototype]] 내부 슬롯에 할당한 다음, 생성한 객체를 반환한다. <br>
 <br>
즉, 프로토타입은 추상 연산 OrdinaryObjectCreate에 전달되는 인수에 의해 결정되며, 해당 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

### 6-1. 객체 리터럴에 의해 생성된 객체의 프로토타입

자바스크립트 엔진은 객체 리터럴을 평가하여 객체를 생성할 때 추상 연산 OrdinaryObjectCreate를 호출한다.  <br>
이때 추상 연산 OrdinaryObjectCreate에 전달되는 프로토타입은 Object.prototype이다. <br>
 <br>
즉, 객체 리터럴에 의해 생성되는 객체의 프로토타입은 Object.prototype이다. <br>
`const obj = { x : 1 };`
위 객체 리터럴이 평가되면 추상 연산 OrdinaryObjectCreate에 의해 Object 생성자 함수와 Object.prototype과 생성된 객체 사이 연결이 만들어진다. <br>
이후 obj 객체는 Object.prototype을 상속받아 프로토타입으로 갖게 된다.  <br>
 <br>
이로써 Object.prototype의 constructor 프로퍼티와 hasOwnProperty 메서드 등을 자신의 자산인 것처럼 사용할 수 있다.

### 6-2. Object 생성자 함수에 의해 생성된 객체의 프로토타입

Object 생성자 함수는 인수 없이 호출하면 빈 객체를 생성하며 마찬가지로 추상 연산 OrdinaryObjectCreate가 호출된다. <br>
이때 전달되는 프로토타입 또한 Object.prototype이다. <br>
 <br>
즉, Object 생성자 함수에 의해 생성되는 객체의 프로토타입 또한 Object.prototype이다. <br>
```
const obj = new Object();
obj.x = 1;
```
위 코드가 실행되면 객체 리터럴과 동일하게 추상 연산 OrdinaryObjectCreate에 의해 Object 생성자 함수와 Object.prototype과 생성된 객체 사이 연결이 만들어진다. <br>
이로써 Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받아 프로토타입으로 가진다. <br>
객체 리터럴과의 차이점은 프로퍼티를 추가하는 방식에 있다. <br>
객체 리터럴 방식은 객체 리터럴 내부에 프로퍼티를 추가하지만, Object 생성자 함수 방식은 일단 빈 객체를 생성한 이후 프로퍼티를 추가해야 한다.

### 6-3. 생성자 함수에 의해 생성된 객체의 프로토타입

new 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하면 다른 방식과 마찬가지로 추상 연산 OrdinaryObjectCreate가 호출된다.  <br>
이때 추상 연산 OrdinaryObjectCreate에 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다. <br>
 <br>
즉, 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다.
```
function Person(name) {
  this.name = name;
  }
  const me = new Person('Lee');
  ```
위 코드가 실행되면 추상 연산 OrdinaryObjectCreate에 의해 생성자 함수와 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체와 생성된 객체 사이에 연결이 만들어진다. <br>
Person 생성자 함수는 사용자 정의 생성자 함수이기 때문에 프로토타입 Person.prototype의 프로퍼티는 constructor 뿐이다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/cea76b9a-0fca-4433-9327-79100718ec08)

위 그림은 프로토타입에 프로퍼티를 추가하여 하위(자식) 객체가 상속받을 수 있도록 구현한 코드이다.  <br>
이를 통해 일반 객체와 같이 프로토타입에도 프로퍼티를 추가 및 삭제할 수 있으며, 수정된 내용은 프로토타입 체인에 즉각 반영된다는 것을 알 수 있다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/55c48898-a412-4675-97e3-4ba8ebf9761c)

## 7. 프로토타입 체인
```
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function(){
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');

console.log(me.hasOwnProperty('name'));
```
위 코드를 보면, Person 생성자 함수에 의해 생성된 me 객체는 `Object.prototype`의 메서드인 hasOwnProperty 또한 호출하였다. <br>
이는 me 객체가 `Person.prototype` 뿐만 아니라 `Object.prototype` 또한 상속받았다는 것을 의미한다. <br>
이는 me 객체의 프로토타입은 `Person.prototype`이고, `Person.prototype`의 프로토타입은 `Object.prototype`이기 때문이다.

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/82d0ba2d-9523-467c-a47c-e223c274bd74)

자바스크립트는 **객체의 프로퍼티에 접근하려고 할 때, 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색**한다. 이를 **프로토타입 체인**이라고 한다.  <br>
프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 매커니즘이다. <br>
프로토타입 체인의 최상위에 위치하는 객체는 언제나 `Object.prototype`이다. <br>
 <br>
따라서 모든 객체는 `Object.prototype`을 상속받으며 이를 **프로토타입 체인의 종점**(End of Prototype Chain)이라고 한다. <br>
`Object.prototype`의 프로토타입, 즉 [[Prototype]] 내부 슬롯의 값은 null이다.  <br>
 <br>
따라서 프로토타입 체인의 종점인 `Object.prototype`에서도 프로퍼티를 검색할 수 없는 경우 undefined를 반환하며 에러가 발생하지는 않는다. <br>
이처럼 자바스크립트 엔진은 프로토타입 체인을 따라, 객체 간의 상속 관계로 이루어진 프로토타입의 계층적인 구조에서 객체의 프로퍼티를 검색한다. <br>
 <br>
따라서 **프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘**이라고 할 수 있다.

## 8. 오버라이딩과 프로퍼티 섀도잉
```
const Person = (function () {
  function Person(name) {
    this.name = name;
  }
  
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };
  return Person;
}());

const me = new Person('Lee');

// 인스턴스 메서드
me.sayHello = function() {
  console.log(`Hey! My name is ${this.name}`);
};

me.sayHello(); // 인스턴스 메서드가 호출된다.
```

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/c0737c29-a3f4-447b-b62f-bb78389f8a8e)

프로토타입이 소유한 프로퍼티를 프로토타입 프로퍼티, 인스턴스가 소유한 프로퍼티를 인스턴스 프로퍼티라고 부른다. <br>
프로토타입 프로퍼티오 같은 이름의 프로퍼티를 인스턴스에 추가하면 프로토타입 체인에 따라 프로토타입 프로퍼티를 검색하여, 인스턴스 프로퍼티로 추가한다.  <br>
(프로토타입 프로퍼티를 덮어쓰는 것이 아니다!) <br>
 <br>
위 예시에서, 인스턴스 메서드 sayHello는 프로토타입 sayHello를 오버라이딩했고, 프로토타입 메서드 sayHello는 가려져 "Hey! My name is Lee"가 출력된다. <br>
이처럼 상속 관계에 의해 프로퍼티가 가려지는 현상을 **프로퍼티 섀도잉**이라고 부른다.

>**오버라이딩** (Overriding) : 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

프로퍼티를 삭제하는 경우도 마찬가지다.  <br>
프로토타입 메서드가 아닌 인스턴스 메서드가 삭제되며, 당연히 이후에는 프로토타입 메서드가 호출된다.  <br>
여기서 한 번 더 메서드를 삭제하여 프로토타입 메서드를 삭제해보고자 시도한다면, 불가능하다는 것을 알 수 있다. <br>
 <br>
즉, 하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능하다. get 액세스는 허용되나 set 액세스는 허용되지 않는 셈이다. <br>
만일 프로토타입 프로퍼티를 변경 또는 삭제하고 싶다면, 하위 객체를 통해 프로토타입 체인으로 접근하는 것이 아니라 프로토타입에 직접 접근해야 한다.

## 9. 프로토타입의 교체

프로토타입은 임의의 다른 객체로 변경할 수 있다.  <br>
이는 부모 객체인 프로토타입을 동적으로 변경할 수 있음을 의미한다. <br>
프로토타입은 생성자 함수 또는 인스턴스에 의해 교체할 수 있다.

### 9-1. 생성자 함수에 의한 프로토타입 교체
```
const Person = (function (){
  function Person(name) {
    this.name = name;
  }
  // 생성자 함수의 prototype 프로퍼티 통해 프로토타입 교체
  Person.prototype = {
    sayHello(){
      consoel.log(`Hi! My name is #{this.name}`);
    }
  };
  return Person;
}());
const me = new Person('Lee');
```

`Person.prototype = {sayHello(){`에서 Person.prototype에 객체 리터럴을 할당했다. <br>
이는 Person 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체한 것이다. <br>
당연히 교체한 객체 리터럴에는 constructor 프로퍼티가 없다. <br>
constructor 프로퍼티는 자바스크립트 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티기 때문이다. <br>
이렇게 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.<br>
 <br>
따라서 me 객체의 생성자 함수를 검색하면 Person이 아니라 Object가 나온다. <br>
```
Person.prototype = {
  constructor: Person,
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};
```

위 코드처럼 프로토타입으로 교체한 객체 리터럴에 constructor 프로퍼티를 추가하는 코드를 통해 프로토타입의 constructor 프로퍼티를 되살릴 수 있다.  <br>
저 이후의 결과는 다음과 같다.

```
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```
constructor 프로퍼티와 생성자 함수 간의 연결을 설정했기 때문에 constructor 프로퍼티가 이제는 생성자 함수를 가리킨다.

### 9-2. 인스턴스에 의한 프로토타입 교체

프로토타입은 생성자 함수의 prototype 프로퍼티 뿐만 아니라 인스턴스의 `__proto__` 접근자 프로퍼티(또는 `Object.getPrototypeOf` 메서드)를 통해 접근할 수 있다. <br>
따라서 이를 통해서도 프로토타입을 교체할 수 있다. <br>
생성자 함수의 prototype 프로퍼티에 다른 임의의 객체를 바인딩하는 것은 미래에 생성할 인스턴스 프로토타입을 교체하는 것이다.  <br>
 <br>
따라서 `__proto__` 접근자 프로퍼티를 통해 프로토타입을 교체하는 것은 이미 생성된 객체의 프로토타입을 교채하는 것이다.

```
function Person(name) {
  this.name = name;
}
const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// me 객체의 프로토타입을 parent 객체로 교체
Object.setPrototypeOf(me, parent);
me.sayHello();
```
`Object.setPrototypeOf(me, parent);` 해당 코드는 `me.__proto__ = parent;`와 동일하게 작동한다. <br>
생성자 함수에 의한 교체와 마찬가지로 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.  <br>따라서 me 객체의 생성자 함수는 Person이 아닌 Object로 나온다. <br>

생성자 함수와 인스턴스에 의한 프로토타입 교체의 차이점은 다음과 같다. <br>
생성자 함수에 의한 프로토타입 교체는 Person 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다. <br>
 <br>
그러나 인스턴스에 의한 프로토타입 교체에서는 Person 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지 않는다.  <br>
직접적으로 해당 인스턴스의 `__proto__`에 연결되기 때문이다. 또한 생성자 함수의 prototype 프로퍼티는 여전히 초기에 설정한 값을 유지한다.  <br>
단순히 특정 인스턴스의 프로토타입을 교체하는 것이기 때문에 생성자 함수의 프로토타입과는 별개로 동작한다. <br>
 <br>
그러나 프로토타입 교체를 통해 객체 간 상속 관계를 동적으로 변경하는 것은 꽤 번거롭기 때문에 프로토타입은 직접 교체하지 않는 편이 좋다.

## 10. instanceof 연산자

instanceof 연산자는 이항 연산자로서, 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다.  <br>
만약, 우변의 피연산자가 함수가 아닌 경우 TypeError가 발생한다.
`객채 instanceof 생성자 함수`
우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true로 평가되고, 그렇지 않으면 false로 평가된다.

```
function Person(name) {
  this.name = name;
}
const me = new Person('Lee');
const parent = {}; // 프로토타입으로 교체할 객체
Object.setPrototypeOf(me, parent); // 프로토타입의 교체

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.prototype === Person); // false
// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않는다.
console.log(me instanceof Person); // false
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재한다.
console.log(me instanceof Object); // true
```
me 객체는 비록 프로토타입과 생성자 함수 간의 연결이 파괴되었지만, Person 생성자 함수에 의해 생성된 인스턴스임은 여전하다.  <br>
그러나 `me instanceof Person`은 false로 평가된다. `Person.prototype`이 me 객체의 프로토타입 체인 상에 존재하지 않기 때문이다. <br>
이처럼 instanceof 연산자는 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라,  <br>
**생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인**한다. <br>
이는 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결은 파괴되지 않아 instanceof는 영향을 받지 않았기 때문이다!

## 11. 직접 상속
### 11-1. Object.create에 의한 직접 상속

`Object.create` 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다.  <br>
이는 다른 객체 생성 방식과 마찬가지로 추상 연산 OrdinaryObjectCreate를 호출한다. <br>
`Object.create` 메서드의 첫 번째 매개변수에는 생성할 객체의 프로토타입으로 지정할 객체를 전달한다. <br>
두 번째 매개변수에는 생성할 객체의 프로퍼티 키와 프로퍼티 디스크립터 객체로 이루어진 객체를 전달한다. <br>
해당 객체의 형식은 `Object.defineProperties`메서드의 두 번째 인수와 동일하며, 두 번째 인수는 옵션이므로 생략이 가능하다. <br>

```
/**
* 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체를 생성하여 반환
* @param {Object} prototype - 생성할 객체의 프로토타입으로 지정할 객체
* @param {Object} [propertiesObject] - 생성할 객체의 프로퍼티를 갖는 객체
* @returns {Object} 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체
*/
Object.create(prototype[, propertiesObject])
```

```
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인 종점에 위치한다.
let obj = Object.create(null); // obj->null
console.log(Object.getPrototypeOf(obj)) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError : obj.toString is not a function

// obj -> Object.prototype -> null
obj = Object.create(Object.prototype); // obj = {}과 동일
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj -> Object.prototype -> null
obj = Object.create(Object.prototype, {
  // 추가한 x라는 프로퍼티는 값이 1, 쓰기 가능, 열거 가능, 설정 가능한 프로퍼티
  x : { value : 1, writable : true, enumerable : true, configurable : true }
});
// obj = {x:1};, obj = Object.create(Object.prototype);, obj.x = 1;과 동일

const myProto = {x:10}; // 임의의 객체 직접 상속받음
// obj -> myProto -> Object.prototype -> null
obj = Object.create(myProto);
console.log(Object.getPrototype(obj) === myProto); // true

function Person(name) {
  this.name = name;
}
// obj->Person.prototype->Object.prototype->null
obj = Object.create(Person.prototype);
Console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```
첫 번째 매개변수에 전달한 객체의 프로토타입 체인에 속하는 객체를 생성하면서 직접적으로 상속을 구현해낼 수 있다.  <br>
이 메서드의 장점은 다음과 같다. <br>
- new 연산자가 없이도 객체를 생성할 수 있다.
- 프로토타입을 지정하면서 객체를 생성할 수 있다.
- 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

> `Object.prototype`의 빌트인 메서드인 `Object.prototype.hasOwnProperty`, `Object.prototype.isPrototypeOf`, `Object.prototype.propertyIsEnumerable` 등은 모든 객체가 상속받아 호출할 수 있다. 프로토타입 체인의 종점 메소드이기 때문이다. <br>
> 그러나 `Object.create` 메서드를 통패 프로토타입 체인의 종점에 위치하는 객체를 생성하면, 해당 객체는 `Object.prototype`의 빌트인 메서드를 사용할 수 없다.
> 이미 종점에 위치하므로 더이상 다른 객체를 프로토타입 체인에서 상속받지 않기 때문이다. <br>
> 따라서 `obj.hasOwnProperty`처럼 빌트인 메서드를 객체가 직접 호출하는 것은 권장되지 않고,
> `Object.prototype.hasOwnProperty.call(obj, 'a')`의 방식처럼 간접적으로 호출하는 것이 좋다. <br>

### 11-2. 객체 리터럴 내부에서 `__proto__`에 의한 직접 상속

`Object.create`의 단점은 두 번째 인자로 프로퍼티를 정의해야 한다는 점이다. <br>
ES6에서부터는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.
```
const myProto = {x : 10};
const obj = {
  y : 20,
  // 객체를 직접 상속받음
  __proto__ : myProto // obj -> myProto -> Object.prototype -> null
}
/*
const obj = Object.create(myProto, {
  y : {value : 20, writable : true, enumerable : true, configurable : true }
});
*/
```

## 12. 정적 프로퍼티/메서드

정적(static) 프로퍼티/정적 메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조 및 호출할 수 있는 프로퍼티/메서드를 말한다.
```
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name});
}

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staicMethod = function() {
  console.log('staticMethod');
}

const me = new Person('Lee')

// 생성자 함수에 추가한 정적 프로퍼티 또는 메서드는 생성자 함수로 참조 또는 호출한다.
Person.staticMethod(); // StaticMethod
me.staticMethod(); //TypeErroro : me.staticMethod is not a function
```

![image](https://github.com/Y0-0N63/STUDY-4242/assets/144354615/434a6af3-c2ff-49b6-9ccb-5a8d23c1c907)

일반적으로 정적 프로퍼티나 메서드는 생성자 함수 자체에 직접적으로 바인딩되어 있으므로 인스턴스에는 포함되지 않는다. <br>
정적 메서드인 `staticMethod` 또한 `Person` 생성자 함수에 정의된 정적 메서드이므로 `Person` 생성자 함수에 바인딩되어 있다. <br>
`me`는 `Person` 생성자 함수를 통해 생성되었기 때문에, 인스턴스는 `Person` 생성자 함수의 프로토타입 체인 상에 위치한다.<br>
그러나 `staticMethod`는 인스턴스의 프로토타입 체인 상에 존재하지 않는다. 따라서 `me.staticMethod()`를 호출할 수 없다. <br>
 <br>
즉, 인스턴스로 참조 또는 호출할 수 있는 프로퍼티 또는 메서드는 프로토타입 체인 상에 존재해야만 하며,  <br>
그렇지 않은 정적 프로퍼티 또는 메서드는 인스턴스가 참조하거나 호출할 수 없다! <br>
<br>
앞에서 본 `Object.create`메서드는 Object 생성자 함수의 정적 메서드이다.<br>
그리고 `Object.prototype.hasOwnProperty` 메서드는 `Object.prototype`의 메서드이다. <br>
 <br>
따라서 `Object.create` 메서드는 인스턴스인 `Object` 생성자 함수가 생성한 객체로 호출할 수 없다! <br>
하지만 `Object.prototype.hasOwnProperty` 메서드는 프로토타입 체인의 종점이기 때문에 모든 객체가 호출할 수 있다. <br>
```
// Object.create는 정적 메서드다.
const obj = Object.create({ name : 'Lee' });
// Object.prototype.hasOwnProperty는 프로토타입 메서드다.
obj.hasOwnProperty('name'); // false
```

만약 인스턴스나 프로토타입 메서드 내에서 this를 사용하지 않는다면, 그 메서드를 정적 메서드로 변경할 수 있다.  <br>
일반적으로 메서드 내부 this는 해당 메서드를 호출한 인스턴스를 가리키기 때문이다.  <br>
그러나 메서드 내에서 this를 사용하지 않는다면, 즉, 메서드가 객체의 상태나 속성을 참고하지 않고 독립적으로 동작한다면, 해당 메서드는 객체의 인스턴스와 관련이 없기 때문이다. <br>
그리고 정적 메서드는 특정 인스턴스에 속한 것이 아니라, 클래스 또는 생성자 함수 자체에 속해있다. <br>
 <br>
따라서 메서드를 정적 메서드로 변경하면, 해당 메서드가 객체의 인스턴스를 참조할 필요가 없어진다. <br>
정리하면, 프로토타입 메서드를 호출하기 위해서는 클래스나 생성자 함수로부터 생성된 인스턴스를 통해야 하지만,  <br>
정적 메서드는 클래스나 생성자 함수 자체에 바인딩되어 있으므로 인스턴스를 생성하지 않아도 직접 호출할 수 있다.

```
function Foo() {}

// 프로토타입 메서드 (this가 없으므로 정적 메서드로 변경하여도 동일한 효과)
Foo.prototype.x = function() {
  console.log('x');
};

const foo = new Foo();
// 프로토타입 메서드를 호출하기 위해서는 인스턴스 생성해야 함
foo.x(); // x

// 정적 메서드
Foo.x = function() {
  console.log('x);
};

// 정적 메서드는 인스턴스를 생성하지 않아도 호출 가능
Foo.x(); // x
```

## 13. 프로퍼티 존재 확인
### 13-1. in 연산자
in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다. <br>
`key in object` <br>
이때, key는 프로퍼티 키를 나타내는 문자열이며, object는 객체로 평가되는 표현식이다. <br>
```
const Person = {
  name : 'Lee',
  address : 'Seoul'
};

console.log('name' in person); // true
console.log('age' in person); // false
console.log('toString' in person); // true
```
in 연산자는 확인 대상 객체의 프로퍼티 뿐만 아니라 확인 대상 객체가 상속받는 모든 프로토타입(객체가 속한 프로토타입 체인 상에 존재하는 모든 프로토타입)의 프로퍼티를 확인하므로 주의가 필요하다.<br>
ES6에서는 in 연산자 대신 새로 도입된 `Reflect.has`메서드를 사용할 수도 있다.

### 13-2. Object.prototype.hasOwnProperty 메서드
`Object.prototype.hasOwnProperty` 메서드를 사용해도 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다. <br>
인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true를 반환하고, 상속받은 프로토타입의 프로퍼티 키인 경우 false를 반환한다는 점에서 in 연산자와 차이점이 있다. <br>
` console.log('toString' in person); // false`

## 14. 프로퍼티 열거
### 14-1. for ... in 문
객체의 모든 프로퍼티를 순회하며 열거(enumeration)하기 위해 사용한다. <br>
`for (변수선언문 in 객체) {...}` <br>
for  ... in 문은 객체의 프로퍼티 개수만큼 순회하며, 변수 선언문에서 선언한 변수에 프로퍼티 키를 반환한다.

```
const Person = {
  name : 'Lee',
  address : 'Seoul'
};

// in 연산자 : 객체가 상속받은 모든 프로토타입의 프로퍼티 확인 
console.log('toString' in person); // true

// for ... in : 상속받은 모든 프로토타입의 프로퍼티 열거하나
// Object.prototype의 프로퍼티(ex_toString)의 프로퍼티는 열거되지 않음
for (const key in person) {
  console.log(key + ': ' + person[key]);
}
```

열거되지 않는 이유는 `toString`이 열거할 수 없도록 `Object.prototype.toString`프로퍼티의 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false로 정의된 프로퍼티 때문이다. <br>
정리하자면, **for ... in 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]] 값이 true인 프로퍼티를 순회하며 열거**한다. <br>
 <br>
참고로, 프로퍼티 어트리뷰트 [[Enumerable]]은 프로퍼티 열거 가능 여부를 나타내며 불리언 값을 갖는다. <br>
만약 상속받은 프로퍼티를 제외한, 객체 자신의 프로퍼티만 열거하기 위해서는 `Object.prototype.hasOwnProperty`메서드를 사용하여 객체 자신의 프로퍼티인지 확인해야 한다.

```
for (const key in person) {
  // 객체 자신의 프로퍼티인지 확인
  if (!person.hasOwnProperty(key)) continue;
  console.log(key + ': ' + person[key]);
}
```

다만 for ... in 문은 프로퍼티를 열거할 때 순서를 보장하지 않는다.  <br>
하지만 대부분의 브라우저는 순서를 보장하고 숫자(사실은 문자열)인 프로퍼티 키에 대해서는 정렬을 실시한다. <br>
배열에서는 for ... in 문보다 일반적인 for 문 혹은 for ... of 문 또는 `Array.prototype.forEach` 메서드를 사용하는 것을 권장한다.  <br>
사실 배열도 객체이기 때문에 프로퍼티와 상속받은 프로퍼티가 포함될 수 있다.

### 14-2. Object.keys/values/entries 메서드
for ... in 문은 객체 자신의 고유 프로퍼티 뿐만 아니라 상속받은 프로퍼티 또한 열거한다. <br>
따라서 `Object.prototype.hasOwnProperty`메서드를 사용하여 객체 자신의 프로퍼티인지 확인하는 추가 처리가 필요하다. <br>
객체 자신의 고유 프로퍼티만 열거하기 위해서는, `Object.keys/values/entries` 메서드 사용이 권장된다. <br>
`Object.keys` 메서드는 객체 자신이 열거 가능한 프로퍼티 키를 배열로 반환한다. <br>
`Object.values` 메서드는 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환한다. <br>
`Object.entries` 메서드는 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환한다.
