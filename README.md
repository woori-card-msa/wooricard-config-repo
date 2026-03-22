# 📂 Woori Card Config Repository

우리카드 MSA 프로젝트의 모든 마이크로서비스 설정 파일(`.yml`)을 중앙 집중식으로 관리하는 **External Storage**입니다. 



본 저장소의 설정값은 **Config Server**(`port: 8888`)를 통해 각 서비스(승인, 정산, 매입 등)에 동적으로 주입됩니다.

---

## 🏗 주요 역할
* **Environment Management**: 서비스 코드와 설정 정보를 분리하여 관리
* **Global Configuration**: 모든 서비스에 공통으로 적용되는 설정(`application.yml`) 제공
* **Version Control**: 설정값의 변경 이력을 Git을 통해 추적 및 관리

---

## 📁 관리 파일 목록 (Config Files)

각 파일명은 마이크로서비스의 `spring.application.name`과 일치해야 합니다.

| 파일명 | 대상 서비스 | 주요 설정 내용 |
| :--- | :--- | :--- |
| **`application.yml`** | **전 서비스 공통** | Eureka 서버 주소, 공통 로깅 설정 등 |
| **`wooricard-gateway.yml`** | API Gateway | 라우팅 경로(Route), 필터 설정 |
| **`wooricard-approval-service.yml`** | 승인/결제 | DB 접속 정보, 서비스 고유 로직 설정 |
| **`wooricard-settlement-service.yml`** | 정산 서비스 | 정산 배치 주기, 외부 API 연동 설정 |
| **`wooricard-billing-service.yml`** | 매입 청구 | 청구 데이터 처리 규칙 설정 |

---

## 🚀 설정 수정 및 반영 방법 (Workflow)

본 저장소의 내용을 수정하면 실시간으로 서비스에 영향을 줄 수 있으므로 아래 절차를 권장합니다.

1. **Local 수정**: 본 저장소를 클론하여 로컬에서 YAML 파일을 수정합니다.
2. **Commit & Push**: 수정 사항을 `main` 브랜치에 반영합니다.
3. **Refresh (선택)**: 
    * 서비스 재시작 없이 반영하려면 각 마이크로서비스의 `POST /actuator/refresh` 엔드포인트를 호출하세요.
    * (단, 서비스에 `spring-boot-starter-actuator`와 `@RefreshScope`가 적용되어 있어야 합니다.)

---

## 🚦 서비스 연동 가이드

마이크로서비스에서 본 저장소의 설정을 읽어오려면 `application.yml`에 아래와 같이 **Config Server** 주소를 지정해야 합니다.

```yaml
spring:
  config:
    import: "optional:configserver:http://localhost:8888" # Config Server 주소
```

---

## ⚠️ 주의 사항 (Security)

* **Public Repository 주의**: 현재 실습을 위해 **Public**으로 설정되어 있습니다. 
* **민감 정보 노출 금지**: 실제 운영용 DB 비밀번호, API 키, 개인정보 등은 절대 평문으로 커밋하지 마세요. 
* **임시 조치**: 민감한 값은 로컬 환경 변수(`${DB_PASSWORD}` 등)를 사용하거나, Config Server의 암호화 기능을 활용해야 합니다.

---

### 💡 팁
* YAML 파일 작성 시 **들여쓰기(2 spaces)** 에 주의하세요. 문법 오류 시 Config Server가 설정을 읽어오지 못할 수 있습니다.
* 새로운 서비스를 추가할 경우, 해당 서비스의 `spring.application.name`과 동일한 이름의 `.yml` 파일을 이 곳에 생성하면 됩니다.

