# 코딩 컨벤션의 중요성 그리고 ESLint 와 Prettier 로 스스로 협업 강화하기

개발자마다 프로젝트 폴더구조, 코드 스타일이 조금씩 다를 것이고,<br>
이것은 협업 시 한 프로젝트에서 각기 다른 스타일의 코드를 한다는 것을 의미한다.

앞서 언급한 내용대로 할 경우 유지보수 하기에 까다롭고 다른 팀원이 투입되거나 인수인계 시<br>
서로 다른 코드 스타일에 당황하거나 어려움을 겪을 수 있다.<br>

따라서 코드의 문법, 안티패턴 검사와 함께 일관된 코드 스타일을 제공하고, 규칙을 정해주는 도구를 사용하는 게 좋다.

처음 구상은 [Github Wiki](https://github.com/EdenFN/Node_Coding_Convention/wiki) 에 적어두고 검토 후 팀원과 공유 후 컨벤션에 맞게 리펙토링 할 예정<br>
이었으나, [Github Wiki](https://github.com/EdenFN/Node_Coding_Convention/wiki) 가 잘 정리되어 있어도 많은 분량의 컨벤션을 읽고 일일이 프로젝트의 코드를<br>
변경하는 일은 코드누락과 또 다른 에러를 유발 할 수 있다.

코드 컨벤션을 먼저 작성 하거나 또는 레퍼런스([Airbnb](https://github.com/airbnb/javascript))를 참조해서 이를 자동화 할 수 있는<br>
[Prettier](https://prettier.io/) 와 [ESLint](https://eslint.org/) 가 있으며, [Github Wiki](https://github.com/EdenFN/Node_Coding_Convention/wiki) 에 작성하기 전 기술검토([Prettier](https://prettier.io/) 와 [ESLint](https://eslint.org/))와 공유 후<br>
의견을 들어보고 실제 팀에 적용해보는 방향으로 나아 가 보자.

---
# Prettier
> Javascript Code Formatter로는 대표적으로 Prettier 와 Beautify 가 존재하지만,<br>
> 인지도 와 기능인 측면에서도 많고, 활발한 기여가 이뤄지고 있는 Prettier 에 대해 설명한다.

Prettier 는 코드 포매터이며, 자기의견이 확고한 코드 포매터라고 소개하고 있다.

줄 바꿈, 공백, 들여 쓰기 등 에디터에서 텍스트를 일관성있게 작성되도록 도와주는 역할이다.<br>
예를 들어 들여쓰기는 2칸만, 줄 바꿈의 너비는 80까지 등으로 설정 해 놓는 것이다.

```javascript
...생략

var insertData = [(start - 1) * limit, limit]
            var rows = await MariaQuery(query, insertData)
			            rows.map(
										row => {
			                row['user_name'] = MaskingByReplaceName(row['user_name']);
			                row['user_phone'] = MaskingByReplacePhone(row['user_phone']);
							                // 최초 문의글 등록 후 조회요청 데이블의 데이터는 없으므로 상태는,
							                if (row['approval_status'] === null)
							                    row['approval_status'] = 1// 1:상담전
							                if (row['reply_status'] === null)
							                    row['reply_status'] = 1// 1:상담전
			            })
                                 resolve({status: 200,value : {success: true, totalCount: count, data: rows}})

생략...
```
예를들어 위 와 같은 코드를

```javascript
...생략

const insertData = [(start - 1) * limit, limit]
const rows = await MariaQuery(query, insertData)

rows.map(row => {
    row['user_name'] = MaskingByReplaceName(row['user_name']);
    row['user_phone'] = MaskingByReplacePhone(row['user_phone']);

    // 최초 문의글 등록 후 조회요청 데이블의 데이터는 없으므로 상태는,
    if (row['approval_status'] === null)
        row['approval_status'] = 1   // 1:상담전

    if (row['reply_status'] === null)
        row['reply_status'] = 1     // 1:상담전
})
resolve({
    status: 200,
    value : {
        success: true, totalCount: count, data: rows
    }
})

생략...
```
위 와 같이 정렬을 해줄 수 있다.

하지만,<br>
ESLint 도 포매팅이 되지만 Prettier 는 좀더 일관성 있는 코드, 엄격하고 더 많은 자동 포매팅,<br>
가독성이 좋은 코드 스타일을 만들어준다.

---
# ESLint
> ES 는 ECMA Script 와 동일하며, Lint 는 코드 오류나 버그가 있는지 확인하고, 정해진 규칙을<br>
> 잘 지키고 있는지에 대한 것 들을 확인과 점검을 하기 위해 사용하는 도구 이다.<br>
> ESLint 는 ES 와 Lint 보면 알듯이 Javascript 언어의 소스코드를 분석하는 도구임을 알 수 있다.<br>

ESLint 는 코드 포매터 기능도 가능 하지만 정적 분석 도구에 더 특화되어 있으며,<br>
코드를 분석해 문법적인 오류나 안티 패턴을 찾아주는 역할을 한다.

```javascript
// function 키워드 사용
function GetSupportListCount() {
 ...
}

// arrow function 사용
const GetSupportListCount = () => {
 ...
}
```
예를들어 위와같이 각기다른 함수를 생성하는 방식이 있다.
```javascript
const count = [1,2,3,4,5];

// for문 사용
for(let i = 0; i < count.length; i++){
 ...
}

// Array 내장 함수 사용
count.forEach((value) => {
 ...
})
```
또 위와같은 각기다른 반복문 을 생성하는 방식이 있다.

일반 function 키워드의 함수로 정의할 수도 있고, arrow function 을 쓸 수도 있다.<br>
그리고 배열의 반복문을 돌릴 때 일반 for 문 을 돌릴수도 있지만 forEach, map 등 Array 내장 함수를 사용할 수도 있다.<br>
이처럼 다양한 방식으로 구현할 수 있는 코드 방식을 일관성 있게 구현할 수 있도록 바로 잡아주는 것이 ESLint 가 하는 역할이다.<br>
---

# Prettier 와 ESLint 는 같이 활용 가능한거야?

```javascript
const express=require('express');
const app=express(    )
var a
const port= 3000;

app.get("/", (  req,res)=> {res.send("Hello World!")})

app.listen(
    port, function ( ){
        console.log(`app listening on port ${port}!`)
})
```
ESLint 를 활용하기 전
```javascript
const express = require('express');
const app = express();
let a;
const port = 3000;

app.get('/', (req, res) => { res.send('Hello World!'); });

app.listen(
    port, () => {
        console.log(`app listening on port ${port}!`);
    },
);
```
ESLint 를 활용하기 후
```javascript
const express = require("express");

const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`app listening on port ${port}!`);
});
```
이때 Prettier 를 활용한다면, 코드 스타일에 맞게 바꿀 수 있다.

---
# 둘 다 써야하는 거야?

권장은 아니다. 둘 중 하나만 사용해도 된다.

Prettier 는 '한 줄의 최대 길이', 'tab'을 쓸 것인지 'space'를 쓸 것인지, 문자열을 홑 따옴표로 할 것인지 큰 따옴표로 할 것인지 같이 정해진 코딩 스타일에 맞게 코드를 변환하기 위해 사용한다.

ESLint 는 문법 에러를 잡아내고, 코드 품질을 검사하기 위해 사용한다.<br>
따라서 Prettier 와 ESLint 를 같이쓰면 코드 품질을 바로 잡은 뒤 코드를 정해진 코딩 스타일에 맞게 바꿀 수 있기 때문이다.