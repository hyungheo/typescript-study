
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

### node 방식에서 상대주소를 사용하지 않을경우

위에서는 상대주소 (./ or ../ 으로 시작하는)로 모듈의 위치를 명시했는데 상대주소를 사용하지 않고 아래와 같이 모듈의 이름만을 명시하게되면 node_modules 에서 모듈을 찾게 된다. 
```TypeScript
var x = require("moduleB");
```

1. /root/src/node_modules/moduleB.js
2. /root/src/node_modules/moduleB/package.json (if it specifies a "main" property)
3. /root/src/node_modules/moduleB/index.js 

4. /root/node_modules/moduleB.js
5. /root/node_modules/moduleB/package.json (if it specifies a "main" property)
6. /root/node_modules/moduleB/index.js 

7. /node_modules/moduleB.js
8. /node_modules/moduleB/package.json (if it specifies a "main" property)
9. /node_modules/moduleB/index.js


### package.json ts, tsx d.ts 파일참조하는 것을 명시하려면?

아래와 같이 moduleB 를 참조할 때, moduleB 가 js 대신 TypeScript 파일을 참조하도록 명시하려면 main 대신에 type 을 사용하면 된다.   
  
/root/src/moduleA.ts
```TypeScript
import { b } from "./moduleB"
```
  
/root/src/moduleB/package.json
```TypeScript
{ 
  "type": "lib/mainModule.ts" 
}
```
  
탐색순서는 다음과 같다.  
  
    1. /root/src/moduleB.ts
    2. /root/src/moduleB.tsx
    3. /root/src/moduleB.d.ts
    4. /root/src/moduleB/package.json (if it specifies a "types" property)
    5. /root/src/moduleB/index.ts
    6. /root/src/moduleB/index.tsx
    7. /root/src/moduleB/index.d.ts

상대경로로 하지 않을 때는 다음 순서로 탐색된다.  
```TypeScript
import { b } from "moduleB"
``` 
    1. /root/src/node_modules/moduleB.ts
    2. /root/src/node_modules/moduleB.tsx
    3. /root/src/node_modules/moduleB.d.ts
    4. /root/src/node_modules/moduleB/package.json (if it specifies a "types" property)
    5. /root/src/node_modules/moduleB/index.ts
    6. /root/src/node_modules/moduleB/index.tsx
    7. /root/src/node_modules/moduleB/index.d.ts

    8. /root/node_modules/moduleB.ts
    9. /root/node_modules/moduleB.tsx
    10. /root/node_modules/moduleB.d.ts
    11. /root/node_modules/moduleB/package.json (if it specifies a "types" property)
    12. /root/node_modules/moduleB/index.ts
    13. /root/node_modules/moduleB/index.tsx
    14. /root/node_modules/moduleB/index.d.ts

    15. /node_modules/moduleB.ts
    16. /node_modules/moduleB.tsx
    17. /node_modules/moduleB.d.ts
    18. /node_modules/moduleB/package.json (if it specifies a "types" property)
    19. /node_modules/moduleB/index.ts
    20. /node_modules/moduleB/index.tsx
    21. /node_modules/moduleB/index.d.ts
  
## paths
  
특정 모듈의 이름과 경로를 분명하게 명시해 줘야할 필요가 있는 경우들이 있다. 이런 경우에 사용하는 것이
paths 플래그이다.  
  
tsconfig.ts
```TypeScript
{
  "compilerOptions": {
    "baseUrl": "."
    "paths": {
      "jquery": ["node_modules/jquery/dist/jquery"]
    }
  }
}
```

path 플래그로 명시되는 경로는 barUrl 의 경로를 prefix 로 붙이기 때문에 paths 를 쓰기 위해서는 반드시 baseUrl 을 명시해 줘야 한다.  

path 플래그는 매우 편리하게 경로를 지정할 수 있는데, 예를 들면 전체 모듈중에 특정 몇개의 파일만 경로가 다른 경우 아래와 같이 간편하게 표현할 수 있다.

```
projectRoot
├── folder1
│   ├── file1.ts (imports 'folder1/file2' and 'folder2/file3')
│   └── file2.ts
├── generated
│   ├── folder1
│   └── folder2
│       └── file3.ts
└── tsconfig.json
```

tsconfig.ts
```TypeScript
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": [
        "*",
        "generated/*"
      ]
    }
  }
}
```
  
path 에서 명시한 내용을 풀어서 말하면  
```
모든 모듈 ("*": []) 의 경로는 BaseUrl 밑의 모든 디렉토리와  generated 및의 모든 디렉토리의 경로를 포함한다. 
```
라고 해석할 수 있다.

## rootDirs
  
현재는 존재하지 않지만 컴파일 타임에 가변적으로 생성되는 디렉토리의 모듈들을 참조해야 하는 경우가 있다. 이럴 떄 사용하는 것이 rootDirs 플래그이다. 
  
tsconfig.json
```TypeScript
{
  "compilerOptions": {
    "rootDirs": [
      "src/zh",
      "src/de",
      "src/#{locale}"
    ]
  }
}
```

#{locale} <== 패스토큰이라고 한다.  
locale 과 같은 컴파일타임에 가변적으로 생성되는 경로를 명시해 주고자 할때 rootDirs 플래그를 사용하면 된다.


## moduleResolution 을 굳이 알고 있어야 할까?

작은 규모의 소스코드를 관리함에 있어서는 굳이 moduleResolution 플래그를 활용할 필요가 없을 수도 있다. 어차피 TypeScript 컴파일러가 탐색을 해줄테니 말이다. 하지만 TypeScript 는 보다 범용적인 활용을 위해서 만들어진 언어이고, C/C++ 이나 java 가 쓰이는 분야처럼 좀더 구조화된 대규모 프로그램을 지원하기 위해 디자인 되었다. 구조화된 거대한 프로그램에서는 의존성관계와 모듈들의 상관관계에 대한 명시가 상당히 중요하다.
  
moduleResolution 플래그는 TypeScript 에서 이러한 구조화된 관계를 명시하기 위해서 필수적인 요소이다.









