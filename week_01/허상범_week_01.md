> 러닝타입스크립트 책에서 가져온 내용은 ""를 붙임

## Chapter 2. 타입시스템

### 2.1.0 객체와 원시 타입 간의 차이점

- "타입스크립트에서는 원시 타입은 소문자로 참조하는 것이 모범 사례입니다."
- 'String'은 자바스크립트의 내장 객체다.
- 스트링 리터럴을 사용할 때 생성되는 래퍼 객체와 다르다.

```ts
const stringByObject = new String('stringString');
const stringByIiteral = 'stringString';

console.log(stringByIiteral === stringByObject); // false

console.log(typeof stringByObject); // 'object'
console.log(stringByObject instanceof String); // true

console.log(typeof stringByIiteral); // 'string'
console.log(stringByIiteral instanceof String); // false

const returnString = (text: string) => {
  return text;
};

console.log(returnString(stringByObject));
console.log(returnString(stringByIiteral));
```

<br>

### 2.1.2 오류 종류 - 타입 오류

```ts
const returnNumber = (number: Number) => {
  return number;
};

console.log(returnNumber(1));
console.log(returnNumber('1'));
```

<br>

### 2.2 할당 가능성 (Assignability)

- "타입스크립트는 변수의 초깃값을 읽고 해당 변수가 허용되는 타입을 결정합니다."
- "함수 호출이나 변수에 값을 제공할 수 있는지 여부를 확인하는 것"
- [타입 추론(Type Inference)](https://joshua1988.github.io/ts/guide/type-inference.html#%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0-type-inference)

```ts
let firstname = 'Sangbeom';
firstname = 'Heo';
firstname = 123; // Type 'number' is not assignable to type 'string'.
```

<br>

### 2.3 타입 애너테이션 (Type Annotation)

- `:` 를 이용하여 타입을 정의하는 방식
- "초깃값을 할당하지 않고도 변수의 타입을 선언할 수 있는 구문인 타입 애너테이션을 제공합니다."
- 런타입에 영향을 주지 않는다. 컴파일 중 JS코드에서 삭제된다.

```ts
let response; // let response: any
response = 100;
response = '100';

let response2: number;
response2 = 100;
response2 = '100'; // Type 'string' is not assignable to type 'number'.
```

<br>

### 2.4 타입 형태

- "변수에 할당한 값이 원래 타입과 일치하는지 확인하는 것 이상을 수행합니다."
- "객체에 어떤 멤버 속성이 존재하는지 알고 있습니다."

```ts
const arr = [1, 2, 3];
arr.reduce((a, c) => a + c);
console.log(arr.size()); // Property 'size' does not exist on type 'number[]'.
// js 파일에서는 런타임 에러
```

- 문맥상의 타이핑(Contextual Typing)
  - 타입을 추론하는 방식 중 하나

```ts
window.onclick = function (mouseEvent) {
  console.log(mouseEvent.target);
  console.log(mouseEvent.name); // Property 'name' does not exist on type 'MouseEvent'.
};
```

<br>

### 2.4.1 모듈

- 타입스크립트는 특정 파일이 모듈이 아닌 경우 전역 스코프로 간주한다.
- `export {}` 를 사용해 해결할 수 있다.

<br>

## Chapter 3. 유니언과 리터럴

### 3.1 유니언 타입 (OR)

- `|` 연산자를 사용
- 잠재적인 타입을 좁혀준다.
- "타입 애너테이션으로 타입을 정의하는 모든 곳에서 사용할 수 있다."
- 순서는 상관 없다.

```ts
let random = Math.random() > 0.5 ? undefined : Math.random();
// let random: number | undefined
```

<br>

### 3.1.2 유니언 속성

- 유니언 타입의 경우 타입스크립트가 멤버 변수를 체크할 때, 모든 경우의 수를 체크한다.(엄격)
  - 가능한 타입을 모두 체크해 하나라도 멤버 변수가 없으면 타입 오류가 난다.

```ts
let random: '123' | 123;
random = Math.random() > 0.5 ? '123' : 123;

console.log(random.toString()); // 둘 다 있는 메소드
console.log(random.toUpperCase()); // "123"만 있는 메소드
// Property 'toUpperCase' does not exist on type 123.
```

- "유니언 타입으로 정의된 여러 타입 중 하나의 타입으로 된 값의 속성을 사용하려면 코드에서 값이 보다 구체적인 타입 중 하나라는 것을 타입스크립트에 알려야 한다." (Narrowing)

<br>

### 3.2 내로잉 (Narrowing)

- 코드에서 더 구체적인 타입을 유추하는 것
- [Narrowing (handBook)](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
  - "It looks at these special checks (called type guards) and assignments, and the process of refining types to more specific types than declared is called narrowing."
- "타입을 좁히는 데 사용할 수 있는 논리적 검사를 타입가드(Type guard)라고 합니다."

<br>

### 3.2.2 조건 검사를 통한 내로잉

- HandBook 에서는 조건 검사는 아래 3가지 내로잉을 포함한다.
  - [Truthiness narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#truthiness-narrowing)
  - [Equality narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#equality-narrowing)
  - [The in operator narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#the-in-operator-narrowing)

<br>

#### **Truthiness narrowing**

- 자바스크립트에서, `&&`, `||`, `if/else`, '삼항연산자' 등을 사용할 경우, 값을 평가할 때 Boolean 값으로 변환하여 평가함

```ts
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }
  return "Nobody's here. :(";
}
```

<br>

#### **Equality narrowing**

- 자바스크립트에서, `&&`, `||`, `if/else`, '삼항연산자' 등을 사용할 경우, 값을 평가할 때 Boolean 값으로 변환하여 평가함

```ts
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    // We can now call any 'string' method on 'x' or 'y'.
    console.log(x.toUpperCase(), y.toLowerCase());
  } else {
    console.log(x, y);
  }
}

example('sTr', 'sTr'); // "STR",  "str"
example('sTr', true); // "sTr",  true
```

<br>

#### **The in operator narrowing**

- `in` 연산자를 사용하는 방법
  - [MDN : The in operator returns true if the specified property is in the specified object or its prototype chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in)

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ('swim' in animal) {
    return animal.swim();
  }

  return animal.fly();
}
```

### 3.2.3 typeof 검사를 통한 내로잉

- `typeof` 연산자 사용
  - [MDN : The typeof operator returns a string indicating the type of the operand's value.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === 'object') {
    for (const s of strs) {
      // 'strs' is possibly 'null'.
      // (string[] | null) 으로 좁혔지만 부족하다.
      console.log(s);
    }
  } else if (typeof strs === 'string') {
    // 이미 'string'로 좁혀진 상태
    // if 평가가 없어도 ok
    console.log(strs);
  }
}
```

<br>

### 3.4 엄격한 null 검사

- `strict null checking`
- https://www.typescriptlang.org/tsconfig#strictNullChecks

```ts
function doSomething(x: string | null) {
  console.log('Hello, ' + x.toUpperCase());
}

doSomething();

// strictNullChecks ON vs OFF 비교해보자
```

<br>

### 3.5 타입 별칭 (Type Alias)

- `type 별칭 = 타입` 형태
- 별칭은 파스칼 케이스로 작성한다.(PascalCase)

<br>

### 3.5.1 타입 벼칭은 자바스크립트가 아니다.

- 컴파일 중에 삭제된다.
- 따라서 런타임 코드에서는 참조할 수 없다.

<br>

## Chapter 4. 객체

### 4.2 구조적 타이핑

- "매개변수나 변수가 특정 객체 타입을 선언되면 타입스크립트에 어떤 객체를 사용하든 해당 속성이 있어야 한다고 말해야 합니다."
- "구조적 타이핑은 `덕 타이핑(duck typing)`과 다르다."

### 4.2.1 사용 검사

### 4.2.2 초과 속성 검사

<hr>

- 4.2.1, 4.2.2가 이해가 잘 안되서 학습해봄

## 구조적 서브 타이핑에 대한 간단 정리

### 타입스크립트의 [타입 호환성](https://www.typescriptlang.org/ko/docs/handbook/type-compatibility.html)

- TypeScript의 타입 호환성은 구조적 서브타이핑(structural subtyping)을 기반으로 합니다. 구조적 타이핑이란 오직 멤버만으로 타입을 관계시키는 방식입니다. 명목적 타이핑(nominal typing)과는 대조적입니다. TypeScript의 구조적 타입 시스템의 기본 규칙은 y가 최소한 x와 동일한 멤버를 가지고 있다면 x와 y는 호환된다는 것입니다.

```ts
// Str 타입은 StrNum 타입의 부분집합이다.
type StrNum = string | number;
type Str = string;

const doSomthing = (strNum: StrNum) => {
  console.log(strNum);
};

const num: StrNum = 123;
const str: Str = 'abc';

doSomthing(num);
doSomthing(str);
```

### 명목적 타이핑 (nominal typing)

- 먼저 타입을 정리할 때 상속 관계를 명시한 경우에만 타입 호환을 허용하는 것이다.

```ts
type Food = {
  protein: number;
  carbohydrates: number;
  fat: number;
};

type Burger = Food & {
  // Burger 타입은 Food 타입의 하위타입이다.
  burgerBrand: string;
};

function calculateCalorie(food: Food) {
  return food.protein * 4 + food.carbohydrates * 4 + food.fat * 9;
}

const food: Food = {
  protein: 10,
  carbohydrates: 5,
  fat: 5,
};

const burger: Burger = {
  protein: 20,
  carbohydrates: 10,
  fat: 10,
  burgerBrand: 'Burger King',
};

calculateCalorie(food);
calculateCalorie(burger);
```

<br>

### 구조적 서브 타이핑 (structural subtyping)

- 상속 관계가 명시되어 있지 않더라도 객체의 프로퍼티 기반으로 사용하는데 문제가 없다면 타입 호환을 허용하는 방식이다.

```ts
type Food = {
  protein: number;
  carbohydrates: number;
  fat: number;
};

function calculateCalorie(food: Food) {
  return food.protein * 4 + food.carbohydrates * 4 + food.fat * 9;
}

const food: Food = {
  protein: 10,
  carbohydrates: 5,
  fat: 5,
};

const burger = {
  protein: 20,
  carbohydrates: 10,
  fat: 10,
  burgerBrand: 'Burger King',
};

calculateCalorie(food);
calculateCalorie(burger);
calculateCalorie(mandu);
// Property 'fat' is missing in type '{ protein: number; carbohydrates: number; }' but required in type 'Food'.
```

- `calculateCalorie` 함수의 인자로 `protein` `carbohydrates` `fat` 프로퍼티만 있으면 타입 호환을 허용한다.
- `mandu`에는 `fat` 프로퍼티가 없기 때문에 타입 호환이 허용되지 않는다.

### 하지만 만약 인자로 객체 리터럴을 직접 전달한다면?

```ts
type Food = {
  protein: number;
  carbohydrates: number;
  fat: number;
};

function calculateCalorie(food: Food) {
  return food.protein * 4 + food.carbohydrates * 4 + food.fat * 9;
}

calculateCalorie({
  protein: 20,
  carbohydrates: 10,
  fat: 10,
  burgerBrand: 'Burger King',
  // Object literal may only specify known properties, and 'burgerBrand' does not exist in type 'Food'.
});
```

- 타입 호환의 예외가 발생한다.
- 함수에 인자로 들어온 값이 `FreshLiteral`인지 아닌지 여부에 따라 타입 호환 허용 여부가 결정된다.
- 즉 인자가 `FreshLiteral`이라면 구조적 서브 타이핑 x

### 예외처리를 하는 이유

```ts
function calculateCalorie(food: Food) {
  return food.protein * 4 + food.carbohydrates * 4 + food.fat * 9;
}

calculateCalorie({
  protein: 20,
  carbohydrates: 10,
  fat: 10,
  burgerBrand: 'Burger King',
});

// 인자의 타입이 'burgerBrand'를 포함한다고 오해할 수 있다.

calculateCalorie({
  protein: 20,
  carbohydrates: 10,
  fat: 10,
  burgerBrend: 'Burger King',
  // 오타 발생하더라도 오류가 발견되지 않는다.
});
```

<hr>

### 4.3.3 객체 타입 내로잉

- "타입스크립트는 `if (poem.pages)`와 같은 형식으로 참 여부를 확인하는 것을 허용하지 않습니다." => 타입오류

<br>

### 4.3.4 판별된 유니언 (Discriminated union)

```ts
type SuccessState = {
  result: 'success';
  // 객체의 속성이 객체의 형태를 나타내도록 하는 것
  // result가 판별 속성이다.
  // 판별 속성을 사용해 타입 내로잉을 수행한다.
  response: {
    body: string;
  };
};
type FailState = {
  result: 'fail';
  reason: string;
};
type LoginState = SuccessState | FailState;

function login(): LoginState {
  return {
    result: 'success',
    response: {
      body: 'logged in!',
    },
  };
}

function printLoginState(state: LoginState) {
  if (state.result === 'success') {
    console.log(`body: ${state.response.body}`);
  } else {
    console.log(`reason: ${state.reason}`);
  }
}
```
