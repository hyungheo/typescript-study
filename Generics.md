# Generics

## 제네릭이란?
제네릭은 함수나 클래스등을 선언할 떄 타입을 확정짓지 않고 함수를 호출하거나 인스턴스를 생성하는 시점에서 타입을 확정짓는 기능인다.

아래 함수는 string 타입의 매개변수를 받아 string 타입을 리턴한다.
```TypeScript
function f(arg: string): string {
  return arg;
}
```

이를 제네릭 함수로 다음과 같이 표현할 수 있다.
```TypeScript
function f<T>(arg: T): T {
  return arg;
}
```

위에서 보면 함수 이름 뒤에 <T> 가 추가된 것을 볼수 있다. <T> 를 타입변수라고 한다.
함수이름 뒤에 타입변수가 추가되면 함수안에서는 타입변수를 타입대신에 사용할 수가 있다.

타입변수는 제네릭 함수가 호출될 때 실제 타입으로 바꾸어 줘야 한다.
```TypeScript
let ret: string = f<string>("hello world");
```

제네릭을 사용하게 되면 함수를 호출할 때 원하는 타입으로 바꿀 수 있는 유연함을 제공한다.
```TypeScript
let ret: number = f<number>(100);
```
## any 와 제네릭이 다른점
그런데 any 타입을 쓰면 제네릭과 마찬가지로 어떤 타입이던지 사용할 수 있기때문에 둘이 유사해 보인다.
```TypeScript
function f(arg: any): any {
    return arg;
}

//any 타입이기 때문에 어떤 타입이던 사용가능하다.
let retNum: number = f(100);
let retString: string = f("hello world");
```

제네릭과 any가 다른 부분은 제네릭이 좀 더 타입을 엄격하게 적용한다는 것이다.
예를 들면 any를 사용한 아래 코드는 아무 이상없이 컴파일 된다.
```TypeScript
function f(arg: any): any {
  return arg;
}

let ret: number = f("hello world");
```
하지만 함수의 리턴값을 할당받는 변수 ret 은 number 를 기대하고 있으나 실제 할당받게 되는 것은 문자열이다.

제네릭을 사용할 경우 위와 같은 사용은 컴파일시 에러를 발생시킨다.
```TypeScript
function f<T>(arg: T): T {
  return arg;
}

let ret: number = f<number>("hello world"); //<== 컴파일 에러 발생!!
```
결국 제네릭은 any와는 다르게 함수 호출시 타입을 확정함으로써 잘못된 타입사용의 오류를 컴파일에러로 막아준다.
