# 타입스크립트 알아보기

## 1. 타입스크립트와 자바스크립트의 관계

> "Typescript(TS)는 Javascript(JS)의 Superset이다. "

아마도 TS를 접할 때 가장 처음에 듣는 말일 것이고 가장 기억에 남는 말이기도 하다. 일단 앞에서 서술한 대로 TS는 JS와 밀접한 관계를 가지며 이는 문법적으로도 그러하다. JS 프로그램에서 어떠한 이슈를 겪는다면 TS의 타입 체커에게도 지적받을 확률이 높다. 물론 타입 체커에 의해 지적을 받는다 하여도 이것이 동작하지 않는다는 의미는 아니나 이는 3번에서 자세히 다룰 것이다. 앞서 말한 문법적 유사성은 JS를 TS로 마이그레이션하는데 지대한 도움을 준다. 만약 어떠한 JS 프로그램을 다른 정적 타입 언어 프로그램으로 적고자 한다면 이를 새로 적는 것이 훨씬 나은 판단일 수 있으나 JS를 TS로 마이그레이션하는 것은 일단은 형식을 바꾸는 것으로부터 시작하며 이슈가 없는 JS를 TS로 바꾸는 것은 별 문제를 일으키지 않는다.

> "모든 JS 프로그램은 TS 프로그램이나 역은 성립하지 않는다."

```typescript
function greet(who: string) {
  console.log('Hello', who);
}
```

:string을 붙이면서 JS에서 TS로 가게 되고 이는 who라는 변수가 string 타입을 가지는 변수임을 알려줍니다. 이러한 방식으로 TS에게 who라는 변수의 타입을 추론하는데 도움이 되는 정보를 줄 수 있지만 타입 체킹 시스템 자체가 변수의 타입을 추론하기도 합니다. TS는 변수의 초깃값을 통해서 충분히 신뢰도 있게 변수의 타입을 추측할 수 있고 이를 통해 오류를 잡아낼 수 있습니다. 그러함에도 직접적으로 타입을 명시해주는 것은 TS의 타입 체킹 동작이 가지는 신뢰도를 높일 뿐 아니라 원하는 대로 흘러가는지 더 쉽게 TS가 잡아낼 수 있도록 합니다.

TS란 결국 JS의 런타임 동작을 모델링하는 타입 시스템을 가지고 있기에 최대한 런타임 내에서 발생할 오류를 잡고자 노력하나 이는 100%를 보장하지는 못합니다. 타입 체커를 통과하면서 실제 동작에서 오류를 발생하는 것은 충분히 가능한 일이고 그렇다 하더라도 TS는 타입으로 인해 발생하는 오류를 최소한으로 만들어줄 것입니다. 앞서 말한 TS를 사용함에도 발생하는 오류는 추후에 다시 한 번 예시를 통해 다루도록 하겠습니다.

## 2. 타입스크립트 설정 이해하기

## 3. 코드 생성과 타입은 관계가 없다

## 4. 구조적 타이핑(Duck Typing) 익숙해지기

## 5. any 타입 지양하기