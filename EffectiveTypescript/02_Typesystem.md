# 타입스크립트의 타입 시스템

## 6. 편집기를 활용하여 타입 시스템 탐색하기

타입 스크립트를 실행하면 두 가지를 실행할 수 있습니다.

- 타입스크립트 컴파일러 tsc
- 단독으로 실행 가능한 타입스크립트 서버 tsserver

보통은 tsc를 사용하는 것이 주된 목적이지만 tssserver 역시 언어 서비스를 제공한다는 점에서 중요합니다. 보통은 편집기에서 언어 서비스를 사용하는데 tsserver에서도 언어 서비스를 제공하도록 설정하도록 하는 것이 좋습니다. 자동완성 서비스를 사용하면 타입스크립트 코드 작성이 간편해집니다. 이러한 서비스를 차치하더라도 편집기는 코드를 빌드하고 타입 시스템을 익힐 수 있는 최고의 수단입니다.

편집기마다 조금씨기 다르지만 보통의 경우에는 심벌 위에 마우스 커서를 대면 타입스크립트가 타입을 어떻게 판단하고 있는지 확인할 수 있습니다. 특정 시점에서 타입스크립트가 타입을 어떻게 이해하는지는 타입 넓히기 & 좁히기를 위해서 필수적으로 필요한 과정입니다. 조건문의 분기에서 타입이 어떻게 변하는지 살펴보는 것은 타입 시스템을 연마하는 매우 좋은 방법입니다.

> 타입 스크립트가 동작을 어떻게 모델링하는지 알기 위해 타입 선언 파일을 찾아보는 방법을 터득해야 합니다.

## 7. 타입이 값들의 집합이라고 생각하기

런타임에 모든 변수는 JS 세상에서 각자의 고유한 값을 가지며 변수에는 다양한 값을 할당할 수 있습니다.

그러나, 코드가 실행되기 전, 즉 TS가 오류를 체크하는 순간에는 타입을 가지고 있습니다. 타입은 _'할당 가능한 값들의 집합'_ 이라고 생각하면 됩니다. 이 집합은 타입의 범위라고 부르기도 합니다.

이러한 범위를 우리는 집합으로 치환 가능합니다.

가장 작은 집합은 아무것도 포함하지 않는 공집합으로 TS에서 never타입입니다. never 타입으로 선언된 변수의 범위는 공집합이기에 어떠한 값도 할당할 수 없습니다.

```typescript
const x: never = 12;
// ~ '12' 형식은 'never'형식에 할당할 수 없습니다.
```

그 다음으로 가장 작은 집합은 한가지 값만 포함하는 타입입니다. 이들은 TS에서 유닛 타입이라고도 불리는 리터럴 타입입니다.

```typescript
type A = "A";
type B = "B";
type Twelve = 12;
```

이러한 유닛 타입들을 두 개 혹은 세 개 또는 그 이상을 위해서는 유니온 타입을 사용합니다.
유니온 타입은 값 집합들으 합 집합을 의미합니다.

```typescript
type AB = "A" | "B";
const a: AB = "A";
const c: AB = "C";
// ~ "C" 형식은 'AB'형식에 할당할 수 없습니다.
```

다양한 타입스크립트 오류에서 할당 가능한 이라는 문구를 볼 수 있습니다. 이 문구는 집합의 관점에서 '~의 원소(값과 타입의 관계)' 또는 '~의 부분집합(두 타입의 관계)'를 의미합니다. 위의 예시에서 'C'는 AB의 부분집합이 아니므로 오류입니다.
집합의 관점에서 타입 체커의 역할은 하나의 집합이 다른 집합의 부분 집합인지 검사하는 것이라고 볼 수 있습니다.

앞 코드들의 타입은 집합의 범위가 한정되어 있기에 쉽게 이해할 수 있습니다. 하지만 실제 다루게 되는 타입은 대부분 범위가 무한대이므로 이해하기 훨씬 어려울 수 있습니다.

다음의 부분은 중요하다고 생각됩니다. (이 정리글의 필자가 생각하는!)

연산과 관련된 이해를 돕기 위해서 값의 집합을 타입이라고 생각해 봅시다.

```typescript
interface Person {
  name: string;
}

interface LifeSpan {
  birth: Date;
  death?: Date;
}

type PersonSpan = Person & LifeSpan;
```

& 연산자는 두 타입의 intersection(교집합)을 계산합니다. 언뜻 보기에 Person과 LifeSpan 인터페이스는 공통으로 가지는 속성이 없기에 PersonSpan 타입을 공집합(never 타입)으로 예상하기 쉽습니다. 필자 역시 같은 생각이었습니다. 그러나 타입 연산자는 인터페이스의 속성이 아니라, 값의 집합(타입 범위)에 적용됩니다. **그리고 추가적인 속성을 가지는 값도 여전히 그 타입에 속합니다.**

바로 앞의 문장이 잘 이해가 안 가기는 하지만 이로 인해서 Person과 LifeSpan을 둘 다 가지는 값은 Intersection 타입에 속하게 됩니다.

```typescript
const ps: PersonSpan = {
  name = "alan Turing",
  birth = new Date("1912/03/23"),
  death = new Date("1911/02/33"),
};
```

앞의 세가지보다 더 많은 속성을 가지는 값도 PersonSpan 타입에 속합니다. Intersection 타입의 값은 각 타입 내의 속성을 모두 포함하는 것이 일반적인 규칙입니다.

규칙이 속성에 대한 인터섹션에서는 맞지만, 두 인터페이스의 유니온에서는 그렇지 않습니다.

```typescript
type K = keyof (Person | LifeSpan); // 타입이 never
```

앞의 유니온 타입에 속하는 값은 어떠한 키도 없기 떄문에 유니온에 대한 keyof는 공집합이어야만 합니다.

여기서 이제 중요한 식을 만나게 됩니다.

> ```typescript
>   keyof (A&B) = (keyof A) | (keyof B)
>   keyof (A|B) = (keyof A) & (keyof B)
> ```

본 등식은 TS의 타입 시스템을 이해하는 데 큰 도움이 될 것입니다.

조금 더 일반적으로 PersonSpan 타입을 선언하는 방법은 extends 키워드를 사용하는 것입니다.

```typescript
interface Person {
  name: string;
}

interface PersonSpan extends Person {
  birth: Date;
  death?: Date;
}
```

타입이 집합이라는 관점에서 extends의 의미는 '~에 할당 가능한'과 비슷하게 '~의 부분집합'이라는 의미로 받아들일 수 있습니다.PersonSpan 타입의 모든 값은 문자열 name 속성을 가져야 합니다.

위의 |, & 로 연결된 타입들에 대해서 한 번 더 살펴보도록 하겠습니다.

1. 교차 타입

   다양한 타입을 하나로 결합해서 모든 기능을 갖춘 단일 타입을 얻는 방법입니다. 예를 들어 Person & Serializable & Loggable은 Person, Serializable, Loggable의 모든 멤버를 가집니다.

2. 유니온 타입

   유니온 타입은 이름으로 유추했을 때 오히려 교차 타입의 동작을 따라야 할 것 같습니다. 하지만 유니온 타입의 동작은 교차타입의 그것과는 다릅니다. 간단하게 표현할 수 있습니다. | 은 or이라는 키워드와 대응되고 이를 타입에 적용한 것입니다. string | number라고 한다면 string, number일 것이고 이것이 하나의 타입이 되는 것입니다. 유니온 타입은 | 로 연결된 모든 멤버들이 공통적으로 가지는 공통적인 멤버에만 접근할 수 있습니다.

간단 정리

- 타입을 값의 집합으로 생각하면 이해하기 편합니다.

- TS의 타입은 엄격한 상속관계가 아니라 겹쳐지는 집합(벤 다이어그램)으로 표현합니다. 두 타입은 서로 서브타입이 아니면서도 겹쳐질 수 있습니다.

## 8. 타입 공간과 값의 심벌 구분하기

TS의 심벌은 타입 공간이나 값 공간 중의 한 곳에 존재합니다. 심벌은 이름이 같더라도 속하는 공간에 따라 다른 것을 나타내기에 혼란스러울 수 있습니다.

```typescript
interface Cylinder {
  radius: number;
  height: number;
}

const Cylinder = (radius: number, height: number) => ({ radius, height });
```

interface Cylinder에서 Cylinder는 타입으로 쓰입니다. const Cylinder에서 Cylinder와 이름은 같지만 값으로 쓰이며 서로 아무런 연관이 없습니다. 상황에 따라서 Cylinder는 타입으로 쓰일 수도 값으로 쓰일 수도 있으며 이런 점은 가끔 오류를 야기합니다. 컴퓨터 뿐만 아니라 사람이 보았을 때도 그렇습니다.

한 심벌이 타입인지 값인지는 언뜻 봐서 알 수 없습니다. 어떤 형태로 쓰이는 지 문맥을 살펴보아야 합니다. 많은 타입 코드가 값 코드와 비슷해 보이기 때문에 더더욱 혼란스럽습니다. 이 두 공간에 대한 개념을 제대로 잡으려면 TS Playground를 사용해 보면 됩니다.

한편 연산자 중에서 타입에서 쓰일 때와 값에서 쓰일 때 다른 기능을 하는 것들이 있습니다. 그 예 중 하나로 typeof를 들 수 있습니다.

```typescript
type T1 = typeof p; // 타입은 Person
type T2 = typeof email;
// 타입은 (p: Person, subject: string, body: string) => Response

const v1 = typeof p; // 값은 "object"
const v2 = typeof email; // 값은 "function"
```

타입의 관점에서 typeof는 값을 읽어서 TS타입을 반환합니다. 타입 공간의 typeof는 보다 큰 타입의 일부분으로 사용할 수 있고, type 구문으로 이름을 붙이는 용도로도 사용할 수 있습니다.

값의 관점에서 typeof는 JS 런타임의 typeof 연산자가 됩니다. 값공간의 typeof는 대상 심벌의 런타임 타입을 가리키는 문자열 반환하며 TS의 타입과는 다릅니다. JS의 런타임 타입 시스템은 TS의 타입 시스템과 다릅니다. JS의 런타임 타입 시스템은 TS의 정적 타입 시스템보다 훨씬 간단합니다. TS의 타입은 종류가 무수히 많은 반면, JS에는 과거부터 지금까지 단 6개(string, number, boolean, undefined, object, function)의 런타임 타입만이 존재합니다. TS에서 코드가 잘 동작하지 않는다면 앞선 타입 시스템에 대한 인지 부족으로 타입 공간과 값 공간을 혼동했을 가능성이 큽니다.

간단 요약

- TS 코드를 읽을 때, 타입인지 값인지 구분하는 방법을 터득해야 합니다. 타입스크립트 플레이그라운드를 활용해 개념을 잡는 것이 좋습니다.

- 모든 값은 타입을 가지지만, 타입은 값을 가지지 않습니다. Type과 interface 같은 키워드들은 타입 공간에만 존재합니다.

## 9. 타입 단언보다는 타입 선언 사용하기

TS에서 변수에 값을 할당하고 타입을 부여하는 방법은 두 가지입니다.

```typescript
interface Person {
  name: string;
}
const alice: Person = { name: "alice" };
const bob = { name: "Bob" } as Person;
```

이 두가지 방법은 전혀 같지 않습니다. 첫 번째 alice : Person은 변수에 **'타입 선언'** 을 붙여서 그 값이 선언된 타입임을 명시합니다. 두 번째 as Person은 **'타입 단언'** 을 수행합니다. 그렇다면 TS에서 추론한 타입이 있더라도 Person 타입으로 간주합니다.

> 타입 단언보다는 선언을 사용하는 것이 낫습니다.

이유는 다음 코드에서 확인할 수 있습니다.

```typescript
  const alice : Person = {};
    // ~~~~ Person 유형에 필요한 'name'속성이 {} 유형에 없습니다.
  const bob : {} as Person; // 오류 발생 X
```

타입 선언은 할당되는 값이 해당 인터페이스를 만족하는지 검사합니다. 이로 인해 오류가 표시되었습니다. 하지만 타입 단언은 타입을 강제로 지정했으니 타입 체커에게 오류를 무시하라고 하는 것과 같습니다. 타입 선언과 단언의 차이는 속성을 추가할 때 또한 동일하게 적용됩니다. 위와 같이 타입 선언은 속성 추가 시에 인터페이스와 비교를 하는 반면, 단언은 오류가 무시되고 타입이 강제로 지정됩니다.

타입 선언문에서는 잉여 속성 체크가 작동하지만 단언문에서는 작동하지 않습니다. 고로 타입 단언이 꼭 필요한 경우가 아니라면 안전성 체크도 되는 타입 선언을 사용하는 것이 좋습니다.

map과 같은 함수를 사용할 때 어떠한 방식으로 타입 선언을 진행하는지 예시를 살펴보겠습니다.

```typescript
  const people = ['alice', 'bob', 'jan'].map(
    const person : Person = {name};
    return person
  ) // 타입은 Person[]
```

조금 복잡해 보이므로 이를 조금 더 간결하게 표시해 보겠습니다.

```typescript
const people = ["alice", "bob", "jan"].map((name): Person => ({ name })); // 타입은 Person[]
```

여기서 소괄호가 중요한 의미를 지닙니다. (name): Person은 name의 타입이 없고 반환 타입이 Person이라고 명시합니다. 그러나 (name: Person)은 name의 타입이 Person이고 반환 타입은 없기 때문에 오류가 발생합니다.

이보다 더 나은 코드 역시 존재합니다.

```typescript
const peopel: Person[] = ["alice", "bob", "jan"].map(
  (name): Person => ({ name })
);
```

해당 코드는 최종적으로 원하는 타입을 명시하고 타입스크립트가 할당문의 유효성을 검사하게 합니다.

그러나 함수 호출 체이닝이 연속되는 곳에서는 체이닝 시작에서부터 명명된 타입을 가져야 정확한 곳에 오류가 표시됩니다.

다음으로 타입 단언이 필요한 경우에 대해 살펴보겠습니다.
타입 단언이 필요한 경우는 간단합니다. 타입체커가 추론한 타입보다 여러분이 판단한 타입이 더 정확할 떄입니다. TS는 DOM에 접근할 수 없기 때문에 엘리먼트에 대해서 알지 못하고 우리는 이 경우에 타입스크립트가 알지 못하는 정보를 알고 있기 때문에 타입 단언문을 사용하는 것이 타당합니다.

자주 사용되는 !을 사용하여 null이 아님을 단언하는 경우도 이러한 경우입니다.

```typescript
const elNull = document.getElementyById("foo"); //HTMLElement | null
const el = document.getElementyById("foo")!; // HTMLElement
```

변수의 접두사로 사용된 !는 boolean의 부정문입니다. 그러나 접미사로 쓰인 !는 그 값이 null이 아니라는 단언문으로 해석됩니다. 우리는 !를 일반적인 단언문으로 사용해야 합니다. 단언문은 컴파일 과정 중에서 제거되므로 타입 체커는 알지 못하지만 그 값이 null이 아니라고 확신할 수 있을 때 사용해야 합니다. 만약 그렇지 않다면 null인 경우를 체크하는 조건문을 사용해야 합니다.

하지만, 타입 단언문으로 임의의 타입 간에 변환을 할 수는 없습니다. A가 B의 부분집합인 경우에 타입 단언문을 이용해 변환할 수 있습니다만 앞에서의 Person과 HTMLElement는 서로의 서브타입이 아니기 때문에 변환이 불가합니다.

요약

- 타입 단언보다는 타입 선언

- 화살표 함수의 반환 타입을 명시하는 방법을 터득해야 함

- 타입스크립트보다 타입 정보를 더 잘 알고 있는 상황에서는 타입 단언문과 null 아님 단언문을 사용하면 됩니다.

## 10. 객체 래퍼 타입 피하기

이 부분은 개인적으로 중요하다고 생각하는 부분중 하나입니다. 동작을 이해하는 데 있어서 중요한 부분이라고 생각됩니다.

JS에는 객체 이외에도 기본형 값들에 대한 일곱 가지 타입(string, number, boolean, null, undefined, symbol, bigint)이 있습니다. string, nubmer, boolean, null은 초창기부터 존재해왔으며, symbol은 ES2015에서 추가되었으며 bigint는 최종 확정 단계에 있습니다.

기본형들은 불변이며 메서드를 가지지 않는다는 점에서 객체와 구분됩니다. 그런데 기본형인 string의 경우에 메서드를 가지고 있는 것처럼 보입니다.

```typescript
"primitive".charAt(3);
("m");
```

하지만 사실 charAt은 string의 메서드가 아니며 string을 사용할 때, 자바스크립트 내부에서 많은 동작이 일어납니다. string '기본형' 에는 메서드가 없습니다만, JS에서 메서드를 가지는 String '객체' 타입이 정의되어 있습니다. JS에서 기본형과 객체 타입을 서로 자유롭게 변환합니다. string 기본형에 charAt 같은 메서드를 사용할 때, JS는 기본형을 String 객체로 wrapping하고 메서드를 호출하고, 마지막에 래핑한 객체를 버립니다.

상기의 String 객체를 직접 생성할 수도 있으며, 이 또한 string 기본형처럼 동작합니다. 그러나 string 객체와 String 객체 래퍼가 항상 동일하게 동작하는 것은 아닙니다. 예를 들어 String 객체는 오직 자기 자신하고만 동일합니다.

```typescript
  "hello" === new String("hello");
  >false
  new String === new String("hello");
  >false
```

객체 래퍼 타입의 자동 변환은 종종 당황스러운 동작을 보일 때가 있습니다. 예를 들어 어떤 속성을 기본형에 할당한다면 그 속성이 사라집니다.

```typescript
x = "hello";
x.language = "English";

x.language > undefined;
```

실제로 작동하는 바는 x가 String 객체로 변환된 후 language 속성이 추가되었고, language 속성이 추가되었고, language 속성이 추가된 객체는 버려진 것입니다.
다른 기본형에도 동일하게 객체 래퍼가 존재합니다. number에는 Number, boolean에는 Boolean, .... 등등 대문자로 바뀌어진 객체 래퍼 타입이 존재합니다. (null과 undefined에는 객체 래퍼가 없습니다) 이 래퍼 타입들 덕분에 기본형 값에 메서드를 사용할 수 있고, 정적 메서드(String.fromCharCode 같은)도 사용할 수 있습니다. 그러나 보통은 래퍼 객체를 직접 생성할 필요가 없습니다.

TS에서는 기본형과 객체 래퍼 타입을 모델링합니다.

- string과 String

- number과 Number

- boolean과 Boolean

- symbol과 Symbol

- bigint와 BigInt

그러나 string을 사용할 때는 특히 유의해야 합니다. string을 String이라고 잘못 타이핑하기 쉽고, 실수를 하더라도 처음에는 잘 동작하는 것처럼 보이기 떄문입니다.

그러나 string을 매개변수로 받는 메서드에 String 객체를 전달하는 순간 문제가 발생합니다.
대부분의 라이브러리와 마찬가지로 TS가 제공하는 타입 선언은 전부 기본형 타입으로 되어 있습니다.
당연히 런타임의 값은 객체가 아니고 기본형입니다. 그러나 기본형 타입은 객체 래퍼에 할당할 수 있기 때문에 TS는 기본형 타입을 객체 래퍼에 할당하는 선언을 허용합니다. 그러나 기본형 타입을 객체 래퍼에 할당하는 구문은 오해하기 쉽고, 굳이 그렇게 할 필요도 없습니다. 그냥 기본형 타입을 사용하는 것이 좋습니다.

요약

- 기본형 값에 메서드를 제공하기 위해 객체 래퍼 타입이 어떻게 쓰이는지 이해해야 하며, 직접 사용하거나 인스턴스를 생성하는 것을 피해야 합니다.

- TS 객체 래퍼 타입은 지양하고, 대신 기본형 타입을 사용해야 합니다. String 대신 string, Number 대신 number, Boolean 대신 boolean 등등 기본형 타입을 사용해야 합니다.

## 11. 잉여 속성 체크의 한계 인지하기

타입이 명시된 변수에 객체 리터럴을 할당할 때, TS는 해당 타입의 속성은 있는지, 그리고 **그 외의 속성은 없는지** 확인합니다.

```typescript
interface Room {
  numDoors: number;
  ceilingHeightFt: number;
}
const r: Room = {
  numDoors: 1,
  ceilingHeightFt: 10,
  elephant: "present",
  // ~~~ 개체 리터럴은 알려진 속성만 지정할 수 있으며, 'Room' 형식에 'elephant'이(가) 없습니다.
};
```

Room 타입에 생뚱맞게 elephant 속성이 있는 것은 어색하긴 하지만, 구조적 타이핑 관점으로 생각해보면 오류가 발생하지 않아야 합니다. 임시 변수를 도입해 보면 알 수 있는데, obj 객체는 Room 타입에 할당이 가능합니다.

```typescript
const obj = {
  numDoors: 1,
  ceilingHeightFt: 10,
  elephant: "present",
};
const r: Room = obj; // 정상
```

이 때 obj의 타입은 { numDoors: number, ceilingHeightFt: number; elephant: string }으로 추론됩니다. obj 타입은 Room 타입의 부분집합을 포함하므로, Room에 할당 가능하며 타입 체커도 통과합니다.

앞의 두 예제의 차이점을 살펴보겠습니다. 위의 첫 번째 예제의 경우 구조적 타입 시스템에서 발생할 수 있는 중요한 종류의 오류를 잡을 수 있도록 '잉여 속성의 체크'라는 과정이 수행되었습니다. 그러나 잉여 속성 체크 역시 조건에 따라 동작하지 않는다는 한계가 있습니다.

TS는 단순히 런타임에 예외를 던지는 코드에 오류를 표시하는 것뿐 아니라, 의도와 다르게 작성된 코드까지 찾으려고 합니다. 다음에 그 예제가 있습니다.

```typescript
interface Options {
  title: string;
  darkMode?: boolean;
}

function createWindow(options: Options) {
  if (options.darkMode) {
    setDarkMode();
  }
}

createWindow({
  title: "Spider Solitaire",
  darkmode: true,

  /// 개체 리터럴은 알려진 속성만 지정할 수 있지만 'Options' 형식에 'darkmode'가 없습니다.
  //'darkMode'를 쓰려고 했습니까?
});
```

상기의 코드는 런타임에 어떠한 종류의 오류도 발생시키지 않습니다. 그러나 타입스크립트가 알려주는 오류메시지처럼 의도한 대로 동작하지 않을 수 있습니다. 앞선 구조적 타이핑과 관련한 내용들을 통해 Options는 굉장히 범위가 넓다는 사실을 알 수 있습니다. darkMode에 boolean 속성을 가지며 string 타입인 title속성과 '또 다른 어떤 속성'을 가지는 모든 객체는 Options의 범위에 속합니다.

고로 순수한 구조적 타입 체커로는 이런 위와 같은 종류의 오류를 잡아낼 수 없습니다. 잉여 속성 체크를 이용하면 기본적으로 타입 시스템의 구조적 본질을 해치지 않으면서 객체 리터럴에 알 수 없는 속성을 허용하지 않음으로써 앞에서 다룬 Room이나 Options 예제 같은 문제점을 방지할 수 있습니다.

그러나 객체 리터럴이 아닌 경우에는 잉여 속성 체크가 작동하지 않으며, 타입 단언의 경우에도 작동하지 않습니다.

```typescript
const o: Options = { darkmode: true, title: "Ski Free" };
//~~~~~~ 'Options' 형식에 'darkmode'가 없습니다.

const intermediate = { darkmode: true, title: "Ski Free" }; // 객체 리터럴 O
const o: Options = intermediate; // 정상 객체 리터럴 X

const o: Options = { darkmode: true, title: "Ski Free" } as Options; //정상 타입 단언
```

타입 단언보다는 선언을 사용해야할 단적인 이유 중 하나입니다.

잉여 속성 체크는 구조적 타이핑 시스템에서 허용되는 속성 이름의 오타 같은 실수를 잡는데 효과적인 방법입니다. 오직 객체 리터럴에만 적용되지만 이런 한계점을 인지하고 잉여 속성 체크와 일반 타입 체크를 구분한다면, 두 가지 모두의 개념을 잡는데 도움이 될 것입니다.

간단 요약

- 객체 리터럴을 변수에 할당하거나 함수에 매개변수로 전달할 떄, 잉여 속성 체크가 수행됩니다.

- 잉여 속성 체크는 오류를 찾는 효과적인 방법이지만 TS 타입 체커가 수행하는 일반적인 구조적 할당 가능성 체크와 역할이 다릅니다. 할당의 개념을 정확히 알아야 잉여 속성 체크와 **일반적인 구조적 할당 가능성 체크**를 구분할 수 있습니다.

- 잉여 속성 체크는 한계가 있습니다. 임시 변수를 도입하면 잉여 속성 체크를 건너뛸 수 있다는 점을 기억해야 합니다.

## 12. 함수 표현식에 타입 적용하기

JS(TS)에서는 함수 statement와 expression을 다르게 인식합니다.

```typescript
function rollDice1(sides: number) : number { ... }; // statement
const rollDice2 = function(sides: number) : number { ... }; //expression
const rollDice3 = (sides: number) : number =>  { ... }; // expression

type DiceRollFn = (sides: number) => number;
const rollDice: DiceRollFn = sides => { ... };
```

TS에서는 함수 expression을 사용하는 편이 좋습니다. 함수의 매개변수부터 반환값까지 전체를 함수 타입으로 선언하여 함수 표현식에 재사용할 수 있는 장점이 있기 때문입니다.

예시가 간단하여 함수 타입의 장점이 덜 느껴질 수도 있습니다. 더 자세한 예시를 살펴보겠습니다.

함수 타입의 선언은 불필요한 코드의 반복을 줄입니다. 반복되는 함수 시그니처를 하나의 함수 타입으로 통일하여 재사용성을 증가시킵니다.

```typescript
function add (a: number, b: number) { return a + b };
function minus (a: number, b: number) { return a - b };
..
..
..

type BinaryFn = (a: number, b: number ) => number
```

라이브러리는 공통 함수 시그니처를 타입으로 제공하기도 합니다. 예를 들어 React는 함수의 매개변수에 명시하는 MouseEvent 타입 대신 함수 전체에 적용할 수 있는 MouseEventHandler 타입을 제공합니다. 만약 라이브러리를 직접 만드는 입장이라면 공통 콜백 함수를 위한 타입 선언을 제공하는 것이 좋습니다.

자주 사용되는 fetch에 대해서 생각해봅시다. fetch에 대해서 우리는 json형식의 return을 받기를 기대하지만 이러한 기대는 언제나 깨질 수 있습니다. 존재하지 않는 API에 대한 호출이라면 응답은 JSON형식이 아닐 수 있습니다. 또한 fetch가 실패하면 거절된 프로미스를 응답하지 않는다는 점 역시 간과하기 쉽습니다. 이를 개선하여 checkedFetch를 통해 fetch 성공 여부에 따른 분기를 나눠보도록 하겠습니다.

```typescript
declare function fetch(
  input: RequestInfo, init?: RequestInit
); Promise<Response>

const checkedFetch: typeof fetch = async(input, init) => {
  const response = await fetch(input, init);
  if(!response.ok)
    throw new Error('Request failed: ' + response.status );
  return response;
}
```

간단 요약

- 매개변수나 반환 값에 타입을 명시하기보다는 함수 표현식 전체에 타입 구문을 적용하는 것이 좋습니다.

- 만약 같은 타입 시그니처를 반복적으로 작성한 코드가 있다면 함수 타입을 분리해내거나 이미 존재하는 타입을 찾아보도록 합시다. 라이브러리를 직접 만든다면 공통 콜백에 타입을 제공해야 합니다.

- 다른 함수의 시그니처를 참조하려면 typeof fn를 사용하면 됩니다.

## 13. 타입과 인터페이스의 차이점 알기

## 14. 타입 연산과 제너릭 사용으로 반복 줄이기

## 15. 동적 데이터에 인덱스 시그니처 사용하기

## 16. number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

## 17. 변경 관련된 오류 방지를 위해 readonly를 사용하기

## 18. 매핑된 타입을 사용하여 값을 동기화하기
