# ESLint: Replace '' with '··'(prettier/prettier)

ESLint 는 보통 잘못 입력한 문법을 자동으로 수정하기 위해서 사용하고 Prettier 는 팀원간의 코딩 컨벤션을 맞추기 위해서 사용한다.<br>
하지만, ESLint 에는 포매팅 기능이 포함되어 있어서 ESLint 와 Prettier 를 같이 사용하는 경우 충돌이 나게 된다.<br>
따라서, ESLint 의 포매팅 기능을 종료시키고 Prettier 문법 수정 기능만 사용하게 해야 하지만...<br>

해당 본문에서는 Prettier 파일을 따로 생성하지않고, ESLint 에서 한번에 해결하는 방법을 소개하고자 한다.<br>
나중에 추가해야 할 룰이 많아진다면 eslint-config-pretteir 로 ESLint 의 원래 포매팅 기능을 없애버리고,<br>
eslint-plugin-prettier 로 prettier 의 포매팅 기능을 사용 할 예정이다.

## 문제상황

![](../../../../../../var/folders/qn/4s6f3vwj7jq8hxypjkmjrmjr0000gn/T/TemporaryItems/NSIRD_screencaptureui_Q3D3VF/스크린샷 2022-08-15 오후 4.19.10.png)

import {} 변수명을 기제하는 구간에 에러같은 빨간 줄이 보인다. 이는 {변수명} 에서 { 변수명 } 으로 수정하면 되는 문제다.<br>
개인적으로 하는 플젝이나 간단한 테스트같은 경우에는 구지 지키고 싶지은 않아 해당 기능을 끄고 싶었다.

## 해결방법

.eslintrc.js 파일에서 아래와 같은 코드를 추가 해주면 되면 해결된다.

```shell
# .eslintrc.js

rules: {
    ... 생략 ...
    
    "prettier/prettier": [
      "error", {
        bracketSpacing: false   // 기본값은 true
      }]
    
    ... 생략 ...
  },
```

아래와 같이 빨간 줄 없이 해결

![](../../../../../../var/folders/qn/4s6f3vwj7jq8hxypjkmjrmjr0000gn/T/TemporaryItems/NSIRD_screencaptureui_Fb1TVN/스크린샷 2022-08-15 오후 4.43.05.png)