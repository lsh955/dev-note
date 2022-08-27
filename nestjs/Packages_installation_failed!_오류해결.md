# Failed to execute command: yarn install --silent

nest.js 로 새로운 프로젝트를 생성하기 위해 ```$ nest new project_name``` 해당 명령어를 터미널에 입력했다.<br>
패키지 매니저로 yarn 을 선택하니 아래와 같은 오류 메시지가 나타났다.

## 문제상황

```shell
? Which package manager would you ❤️  to use? yarn

▹▹▹▹▸ Installation in progress... ☕
Failed to execute command: yarn install --silent
✖ Installation in progress... ☕
🙀  Packages installation failed!
In case you don't see any errors above, consider manually running the failed command yarn install to see more details on why it errored out.

                          Thanks for installing Nest 🙏
                 Please consider donating to our open collective
                        to help us maintain this package.


               🍷  Donate: https://opencollective.com/nest
```
```$ yarn install --silent``` 명령어를 실행시키다 실패했다. 그러면 터미널에 ```$ yarn install --silent``` 명령어를 쳐보자...

## 해결방법

```shell
sudo yarn install --silent

warning ../../../package.json: No license field
error @angular-devkit/schematics@14.0.5: The engine "node" is incompatible with this module. Expected version "^14.15.0 || >=16.10.0". Got "12.16.2"
error Found incompatible module.
```
이런 경우, node를 최신 버전으로 업데이트를 해야 하는 경우라고 볼 수 있다.