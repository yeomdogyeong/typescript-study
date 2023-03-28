## 제네릭

```ts
function identity(input) {
  return input;
}
//모든 가능한 타입으로 input을 받으면, 동일한 input으로 반환한다.

identity("abc");
identity(123);
identity({ quote: "I think..." });
```

여기서 input을 any로 설정하면, 함수의 반환 타입 역시 any가 된다.
input이 모든 입력을 허용한다면?

=> input 타입과 함수 반환 타입 간의 관계를 설명해야 하는데,

이 때 ts는 🍊제네릭을 사용해서 타입 간의 관계를 알아낸다.

### 제네릭 함수

```ts
function identity<T>(input: T) {
  return input;
}
//identity 함수는 input 매개변수에 대한 타입 매개변수 T를 설정

const numeric = identity("me"); // 타입 "me"
const stringy = identity(123); // 타입 123
```

화살표 함수로도 만들 수 있다!

```ts
const identity = <T>(input: T) => input;

identity(123); // 타입 123
```

🌧️주의 ! 제네릭 화살표 함수 구문은 .tsx 파일에서 JSX와 충돌하므로 일부 제한이 있다.

### 명시적 제네릭 타입 인수

```ts
function logWrapper<Input>(callback: (input: Input)=> void) {
return ( input : Input ) => {
console.log("Input:", input);
  callback(input);
	}
}

//타입: (input: string) => void
logWrapper((input: string) => {
console.log(input.length))
}

```

기본값이 unknown으로 설정되는 것을 피하기 위해서 ts에 해당 타입 인수가 무엇인지 명시적으로 알려주는 명시적 제네릭 타입 인수를 사용해서 함수를 호출 할 수 있다.

### 다중 함수 타입 매개변수

```ts
function makeTuple<First, Second>(first: First, second: Second) {
return [first, second] ast const;
}

let tuple = makeTuple(true, "abc") // value: readnoly [boolean, string] 타입
```

makeTuple은 두 개의 타입 매개 변수를 선언하고, 입력된 값을 읽기 전용 튜플로 반환합니다.

### 제네릭 인터페이스

```ts
interface Box<T> {
  inside: T;
}

let stringBox: Box<string> = {
  inside: "abc",
};

let numberBox: Box<number> = {
  inside: 123,
};

let incorrectBox: Box<number> = {
  inside: false, // Error
};
```

Box 선언의 속성에 대한 T 타입 매개변수가 이는데, 이 타입 인수로 Box로 선언된 객체를 생성하면 inside의 T 속성이 해당 타입 인수와 일치합니다.

### 제네릭 클래스

```ts
class Secret<Key, Value> {
	key: key;
  	value: value;

  	constructor(ket: key, value: Value) {
    	this.key = key;
      	this.value = value;
    }

  getValue(key: Key): Value | undefined {
  	return this.key === key
    ? this value : undefined;
  }
}

const storage = new Secret(12345, "luggage") //타입 : Secret<number, string>
storage.getValue(1987)
// 타입 : string | undefined
```

클래스의 각 인스턴스는 타입 매개변수로 각자 다르 타입 인수 집합을 가진다.

### 제네릭 인터페이스 구현

```ts
interface ActingCredit<Role> {
	role: string;
  	speaking: boolean;
}

class MoviePart implements ActingCredit<string> {
  	role: string;
  	speaking: boolean;

	constructor(role: string, speaking: boolean) {
    	this.role = role;
      this.speaking: speaking;
    }
}

const part = new MoviePart("Miranda", true)

part.role: //타입 : string

class IncorrectExtension implements ActingCredit<string> {
role: boolean
} //Error
```

MoviePart 클래스가 ActingCredit 인터페이스의 Role 타입 인수를 string으로 지정한다.

IncorrectExtension 클래스는 ActingCredit에 타입 인수로 string[]을 제공함에도 불구, role이 boolean 타입이므로 오류가 발생한다.

### 🍏Promise

Promise란?

네트워크 요청과 같이 요청 이후 결과를 받기까지 대기가 필요한 것

각 Promise는 대기 중인 작업이 resolve 또는 reject 하는 경우 콜백을 등록하기 위한 메서드를 제공한다.

ts에서 Promise 생성자는 단일 매개변수를 받도록 작성된다.
해당 매개변수의 타입은 제네릭 Promise클래스에 선언된 타입 매개변수에 의존한다.

```ts
class PromiseLike<Value> {
  constructor(
    executor: (
      resolve: (value: Value) => void,
      reject: (reason: unknown) => void
    ) => void
  );
}
```

결과적으로, 값을 resolve하려는 Promise를 만드려면 타입 인수를 명시적으로 선언해야 한다.

명시적 제네릭 타입 인수가 없다면, unknown으로 가정하기 때문이다.

Promise 생성자에 타입 인수를 명시적으로 제공하면, 타입스크립트가 결과로서 생기는 Promise 인스턴스의 resolve된 타입을 이해할 수 있다.

```ts
//타입이 Promise<unknown>
const resolveUnknown = new Promise((resolve) => {
  setTimeout(() => resolve("Done!"), 1000);
});

//타입이 Promise<string>
const resolveUnknown = new Promise<string>((resolve) => {
  setTimeout(() => resolve("Done!"), 1000);
});
```

Promise의 제네릭 .then 메서드는 반환되는 Promise의 resolve된 값을 나타내는 새로운 타입 매개변수를 받는다.

다음 코드는 1초 후에 string 값을 resolve 하는 textEventually와 number를 resolve 하기 위해 1초를 더 기다리는 lengthEventually를 생성하는 코드이다.

```ts
//타입 : Promise<string>
const textEventually = new Promise<string>((resolve) => {
	setTimeout(()=> resolve('done!'),1000);
}));

//타입 : Promise<number>
const lengthEventually = textEventually.then((text) => text.length)

```

### 🍎 async 함수

async 키워드를 선언한 모든 함수는 Promise를 반환한다.
async 함수에 따라서 반환된 값이 Thenable (.then() 메서드가 있는 객체, 실제로는 거의 항상 Promise)이 아닌 경우, Promise.resolve가 호출된 것처럼 Promise로 래핑된다.

다음의 lengthAfterSecond는 Promise<number>를 직접적으로 반환하는 반면에,
lenthImmediately는 async 함수이고, 직접 number를 반환하기 때문에 Promise<number>를 반환하는것으로 간주된다.

```ts
//타입: (text:string) => Promise<number>
async function lengthAfterSecond(text: string) {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  return text.length;
}

//타입: (text:string) => Promise<number>
async function lengthImmediately(text: string) {
  return text.length;
}
```

그러므로, Promise를 명시적으로 언급하지 않아도 async 함수에서 수동으로 선언된 반환 타입은 항상 Promise타입이 된다.

```ts
//ok
async function givesPromiseForString(): Promise<string> {
  return "done!";
}

//Error
async function giveSTring(): string {
  return "done!";
}
```
