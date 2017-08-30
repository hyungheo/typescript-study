
# Module Resolution
모듈 레졸루션은 TypeScript 컴파일러가 어떤 기준으로 모듈들의 구현부가 정의되어 있는 파일들을 찾아내는지에 대한 내용이다. 
또한 우리가 컴파일러에게 참조해야할 모듈의 경로를 어떻게 명시해 줄 수 있는지도 포함하고 있다.  
  
## Module Resolition FLAG
컴파일러 옵션에 추가할 수 있는 moduleResolution 플래그는 모듈을 참조하는 방식을 정의합니다. 크게 classic 과 node 가 있으며 명시되지 않을 경우 기본값은 Classic 입니다.  
tsconfig.json 에 명시하거나
```TypeScript
{
  "compilerOptions": {
    "moduleResolution": "node",
  }
}
```
컴파일 커맨드에 옵션을 주면 된다. (classic 으로 명시하고 싶으면 node 대신 classic 으로 바꾼다.)
```
tsc --moduleResolution node
```

### classic 방식
classic 방식은 컴파일러가 모듈을 탐색하는 순서가 다음과 같다.
  
/root/src/folder/A.ts 에서 아래와 같이 참조할 경우...  
```TypeScript
import { b } from "./moduleB"
```
컴파일러는 파일의 현재 위치에서 부터 Bottom-up 순서로 다음과 같이 모듈이 있는 파일이름을 탐색한다.

1. /root/src/folder/moduleB.ts
2. /root/src/folder/moduleB.d.ts
3. /root/src/moduleB.ts
4. /root/src/moduleB.d.ts
5. /root/moduleB.ts
6. /root/moduleB.d.ts
7. /moduleB.ts
8. /moduleB.d.ts

### node 방식
node 방식은 기본적으로 node.js 의 패키지 참조방식을 따르게 된다.

/root/src/moduleA.js
```TypeScript
var x = require("./moduleB");
```

1. /root/src/moduleB.js
2. root/src/moduleB/package.json 에 아래와 같이 main 이 정의되어 있으면 

```TypeScript
{ 
  "main": "lib/mainModule.js"
}
```
root/src/moduleB/lib/mainModule.js 를 참조  
  
3./root/src/moduleB/index.js






