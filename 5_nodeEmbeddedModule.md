# 3.5 노드 내장 모듈

3.5.1 ~ 3.5.4 생략

# 3.5.5. crypto
: 다양한 방식의 암호화를 도와주는 모듈입니다.

## 3.5.5.1. 단방향 암호화

- 단방향 암호화는 한 번 암호화하면 원래 문자열을 찾을 수 없습니다. <br>
  복호화할 수 없으므로 암호화 대신 해시 함수라고 부르기도 합니다.

<br>

> 노드에서 해시 함수는 다음과 같이 사용합니다.

> hash.js
```js
const crypto = require('crypto');

console.log('base64:', crypto.createHash('sha512').update('비밀번호').digest('base64'));
console.log('hex:', crypto.createHash('sha512').update('비밀번호').digest('hex'));
```

> 콘솔
```js
$ node hash
```
![image](https://user-images.githubusercontent.com/65898889/100683587-fbb86a00-33bb-11eb-9350-3095fb65d36d.png)

<br>

## 3.5.5.2. 양방향 암호화

- key가 필요합니다.
- 암호를 복호화하려면 암호화할 때 사용한 키와 같은 키를 사용해야 합니다.

> cipher.js 
```js
const algorithm = 'aes-256-cbc';
const key = 'abcdefgasdfl;kjasf123456';
const iv = '1234567890123456';
const cipher = crypto.createCipheriv(algorithm, key, iv);
let result = cipher.update('암호화할 문장', 'utf8', 'base64');
result += cipher.final('base64');
console.log('암호화:', result);

const decipher = crypto.createDecipheriv(algorithm, key, iv);
let result2 = decipher.update(result, 'base64', 'utf8');
result += decipher.final('utf8');
console.log('복호화:', result2);
```
> 콘솔
```js
$ node cipher

암호화: iiopeG2GsYlk6cc....
복호화: 암호화할 문장
```
<br>

# 3.5.6. util
: 각종 편의 기능을 모아둔 모듈입니다.

- deprecated와 promisify가 자주 쓰입니다.

> util.js
```js
const util = require('util');
const crypto = require('crypto');

const dontUseMe = util.deprecate((x, y) => {
  console.log(x + y);
}, 'dontUseMe 함수는 deprecated 되었으니 더 이상 사용하지 마세요!');
dontUseMe(1, 2);

const ramdomBytesPromise = util.promisify(crypto.randomBytes);
randomBytesPromise(64)
  .then((buf) => {
    console.log(buf.toString('base64'));
  })
  .catch((error) => {
    console.error(error);
  });
```

- **util.deprecate**

함수가 deprecated 처리되었음을 알립니다.<br>
첫 번째 인수로 넣은 함수를 사용했을 때 경고 메시지가 출력됩니다.<br>
두 번째 인수로 경고 메시지 내용을 넣으면 됩니다.<br>
함수가 조만간 사라지거나 변경될 때 알려줄 수 있어 유용합니다.

- **util.promisify**

콜백 패턴을 프로미스 패턴으로 바꿉니다. <br>
바꿀 함수를 인수로 제공하면 됩니다. <br>
이렇게 바꿔두면 async/await 패턴까지 사용할 수 있어 좋습니다.<br>
단, 콜백이 (error, data) => {} 형식이어야 합니다.

> 콘솔
```js
$ node util
```
> ![image](https://user-images.githubusercontent.com/65898889/100685757-50f67a80-33c0-11eb-9299-bd69e210f844.png)


---

# 레퍼런스

[노드 교과서](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791165212308&orderClick=LEa&Kc=) / 조현영 지음