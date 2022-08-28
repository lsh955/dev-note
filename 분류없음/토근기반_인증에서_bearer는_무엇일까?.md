# 토근기반 인증에서 bearer 는 무엇일까?

`bearer`이라는 문자열은 일반적으로 요청 헤더의 Authorization 필드에 담아져 보내진다.

```shell
Authorization: <type> <credentials>
```
`bearer`는 위 형식에서 `type`에 해당한다.<br>
토큰에는 많은 종류가 있고, 서버는 다양한 종류의 토큰을 처리하기 위해 전송받은 `type`에 따라 토큰을 다르게 처리한다.

`type`은 아래와 같이 다양한 `인증타입` 이 있다.

- Basic: 사용자 아이디와 암호를 `Base64`로 인코딩한 값을 토큰으로 사용
- Bearer: JWT 혹은 OAuth 에 대한 토큰을 사용
- Digest: 서버에서 난수 데이터 문자열을 클라이언트에 보낸다. 클라이언트는 사용자 정보와 nonce 를 포함하는 해시값을 사용하여 응답
- HOBA: 전자서명 기반 인증
- Mutual: 암호를 이용한 클라이언트-서버 상호 인증
- AWS4-HMAC-SHA256: AWS 전자서명 기반 인증

위 인증 타입의 종류에서 나타난 것 처럼, `bearer`는 JWT 와 OAuth 를 타나내는 `인증타입`이다.