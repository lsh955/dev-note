# HashiCorp-Vault

## 1. HashiCorp
HashiCorp은 오픈 소스 도구인
[Vagrant](https://www.vagrantup.com/),
[Packer](https://www.packer.io/),
[Terraform](https://www.hashicorp.com/products/terraform),
[Vault](https://www.hashicorp.com/products/vault),
[Consul](https://www.hashicorp.com/products/consul),
[Nomad](https://www.hashicorp.com/products/nomad)를 제공하는 클라우드 인프라 자동화 도구를 제공.


## 2. Vault
Vault는 공개되면 안 되는 비밀번호, API 키, 토큰 등을 저장하고 관리하는 도구.<br>
여기서 비밀 정보라는 것은 애플리케이션 등에서는 사용해야 하는데 외부에 노출이 되면 안 되는 정보를 얘기하는 것.<br>
프로젝트가 간단하다면 괜찮지만, 프로젝트 수가 많고 사람 수도 많아진다면 문제는 더욱 커진다.<br>
Vault는 이러한 정보를 안전하게 보관하고 관리해 주는 도구라고 생각하면 된다.<br>
좀 더 쉽게 얘기하면 Vault에 이 정보를 저장해 놓고 애플리케이션이나 서버에서는 가져다가 사용하겠다는 의미.<br>

## 3. Vault에서 사용하는 용어
Vault를 사용할 때 기능을 확장할 수 있는데 이를 백엔드(Backend)라고 통칭해서 부른다.

### 3-1. 스토리지 백엔드
Vault의 정보를 저장하면 Vault가 이를 어딘가에는 저장해야 하는데 이를 스토리지 백엔드라고 부른다. <br>
스토리지 백엔드를 PostgreSQL로 설정하면 정보를 PostgreSQL에 저장하고 S3로 설정하면 파일에 저장한다. <br>
Consul, DynamoDB, S3, Google Cloud Storage, Azure, MySQL, PostgreSQL 등 다양한 백엔드를 지원하고 있다.<br>

### 3-2. 시크릿 백엔드
시크릿 백엔드는 문서를 보면 꽤 많은 시크릿 백엔드가 있다. <br>
대표적으로 Generic과 Cubbyhole이 있는데 Gerneric 시크릿 백엔드를 사용할 때 정보를 저장하면 위의 스토리지 백엔드로 설정한 물리 스토리지에 정보를 저장하게 된다. <br>
Cubbyhole는 Gerneric과 똑같이 동작하지만, 연습용 플레이그라운드라고 생각하면 된다. <br>
일반적으로 비밀정보를 저장할 때 생각하게 되는 username/password 등을 저장한다고 하면 이 Generic을 사용하면 된다.<br>

### 3-3. 인증 백엔드
Vault에 중요한 정보를 관리하기 시작했으면 이 Vault에 접근할 수 있는 권한을 제어해야 하는데 이게 인증 백엔드이다. <br>
인증 백엔드는 username/password도 있고 Token 백엔드도 있어서 필요에 따라 사람이나 애플리케이션 등에서 사용하도록 만들 수 있다. <br>
그 외에 LDAP이나 GitHub 등을 연결할 수 있다. <br>
회사에서 LDAP를 쓰고 있다면 LDAP auth backend를 연결해서 사용하게 될 테고 이때 퇴사를 해서 LDAP 계정을 삭제한다면 더는 Vault에 접근할 수 없으므로 관리 포인트를 줄일 수 있다. <br>
GitHub 같은 경우는 GitHub의 특정 org에 연결해서 해당 org의 사용자만 Vault에 접근할 수 있게 하는 식으로 연결할 수 있다.<br>