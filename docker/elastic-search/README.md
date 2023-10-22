# Elasticsearch

엘라스틱서치 실무 가이드 책 버전에 맞게 `6.4.3` 버전을 사용합니다.

1. `docker network create elastic`
2. `docker compose up -d`

`http://127.0.0.1:5601/app/kibana`



## X-Pack

X-Pack은 Elasticsearch에서 개발하고 상용화한 플러그인 모음입니다.

- Elasticsearch 운영자가 보안, 모니터링, 분석 등 다양한 관리 기능을 위해 다수의 도구를 통합하여 운영해야 하는 불편함이 있었습니다.
- X-Pack은 이를 한 플러그인에 모두 통합함으로써 운영 편의성을 크게 향상시키고 있습니다.
- 2018년에 무료 기본 기능이 오픈소스로 전환되었고, 보안, ML 등 일부 기능은 유료 플러그인으로 제공하고 있습니다.

주요 기능은 다음과 같습니다:

- 보안(Security) - 인증, 권한 관리, 역할 기반 접근 제어, TLS/SSL 암호화 등의 보안 기능 제공
- 모니터링(Monitoring) - 클러스터, 노드, 인덱스 수준의 모니터링 기능 제공
- 보고(Reporting) - 기본 보고서 생성 기능 제공
- 경고(Alerting) - 조건 기반의 경고 설정 및 알림 기능 제공
- 기계학습(Machine Learning) - Anomaly Detection 등의 기계학습 기능 제공
- Graph - 연관성 분석 등 Graph 기능 제공

아래와 같은 옵션들이 제공되는데, 기본적으로 다 `false`임
```
xpack.security.enabled
xpack.security.http.ssl.enabled
xpack.security.transport.ssl.enabled
```