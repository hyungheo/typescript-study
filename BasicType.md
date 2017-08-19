
#Boolean
```TypeScript
let isDone: boolean = false;
```

#Number
```TypeScript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

#Any
타입을 지정하지 않아도 될 때
```TypeScript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```
