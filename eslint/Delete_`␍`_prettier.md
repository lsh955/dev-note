# ESLint: Delete '␍' (prettier/prettier)

## 문제상황
eslint 와 prettier 를 같이 사용하면서 작업을 하는 도중 갑자기 `Delete '␍' prettier/prettier`라는 경고문구 발생.

## 해결방법
prettier 2.0 이상부터 endOfLine 기본 옵션이 'auto' 에서 'lf' 로 변경되어서 발생하는 오류<br>
.eslintrc.js 파일에서 아래와 같이 설정해주면 문제가 해결된다.

```javascript
rules: {
    
    ... 생략 ...
    
    "prettier/prettier": [
      "error", {
        endOfLine: "auto",  // 추가
      }]
    
    ... 생략 ...

},
```