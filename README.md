# Doongji 플랫폼 프로젝트

## 목차

- [1. 개요](#1-개요)
- [2. 시스템 아키텍처](#2-시스템-아키텍처)
- [3. 프로젝트 목록](#3-프로젝트-목록)
  - [3.1. Doongji1](#31-doongji1)
  - [3.2. contactus-doongji](#32-contactus-doongji)
- [4. 개발 환경](#4-개발-환경)
- [5. 데이터베이스 설계](#5-데이터베이스-설계)
- [6. 배포 및 운영](#6-배포-및-운영)
- [7. 운영 서버 크론 작업](#7-운영-서버-크론-작업)
- [8. 인프라](#8-인프라)

## 1. 개요

- **회사 홈페이지:** [https://doongji.co.kr/](https://doongji.co.kr/)
- **내부 업무용 페이지:** [https://plat.doongji.co.kr/](https://plat.doongji.co.kr/)
- **내부 테스트 페이지:** [https://dev.doongji.co.kr/](https://plat.doongji.co.kr/)

## 2. 시스템 아키텍처

- **API 서버:** Django 기반 API 서버
- **웹 서버:** Gunicorn을 사용한 WSGI 서버
- **리버스 프록시:** Nginx를 사용한 리버스 프록시 서버
- **데이터베이스:** MySQL
- **메시지 큐:** Celery와 Redis를 이용한 비동기 작업 처리
- **캐시:** Redis
- **파일 저장소:** AWS S3
- **CDN:** AWS CloudFront

## 3. 프로젝트 목록

### 3.1. Doongji1

- **doongji_phps_fe**
    - 홈페이지 프론트엔드 프로젝트
    - 사용 기술: TypeScript, React, Tailwind
- **doongji_phps_be**
    - 홈페이지 백엔드 프로젝트
    - 사용 기술: Python, Django, MySQL

### 3.2. contactus-doongji

- **Doongji-FE**
    - 내부 플랫폼 프론트엔드 프로젝트
    - 사용 기술: TypeScript, React, Tailwind
- **Doongji-BE**
    - 내부 플랫폼 백엔드 프로젝트
    - 사용 기술: Python, Django, MySQL
- **Doongji-Celery**
    - 내부 플랫폼 대량 발송 관련 프로젝트
    - 사용 기술: Python, Django
- **doongji-backend**
    - 백엔드 리뉴얼 프로젝트
    - 사용 기술: Java, SpringBoot, JPA, Gradle
- **pdr_custom**
    - 건축물대장 공공 API 관련
    - 사용 기술: Python

## 4. 개발 환경

- 프론트엔드
    - Node: 20.11.1
    - React: 18.2.0
    - npm: 10.2.4
    - nvm: 0.39.1
- 백엔드
    - Python: 3.9
    - Django: 4.1.4
    - Java: 17
    - SpringBoot: 3.1.4
    - MySQL: 8.0.37

## 5. 데이터베이스 설계

- 현재: [https://www.erdcloud.com/d/T5xXwbgoW4Lca2W6f](https://www.erdcloud.com/d/T5xXwbgoW4Lca2W6f)
- 재구성: [https://www.erdcloud.com/d/bs68hTNCQEN4GAY7k](https://www.erdcloud.com/d/bs68hTNCQEN4GAY7k)

## 6. 배포 및 운영

- **배포 도구:** Jenkins, AWS ECS
- **배포 프로세스:**
    1. **젠킨스 접속 정보:**
        - **URL:** [https://jenkins.doongji.co.kr](https://jenkins.doongji.co.kr/)
    2. **소스코드 리모트에 반영 및 태그 자동 생성:**
        - **GitHub Action을 이용한 태그 자동 생성**
        - **태그 자동 생성 조건:**
            - `dev` 브랜치 remote에 push 할 때
            - 타 브랜치(예: `feat/`, `refactor/` 등)에서 `dev` 브랜치로 merge 할 때
            - `dev` 브랜치에서 `main` 브랜치로 merge 할 때
        - **태그 용도:** 프로젝트 버전 관리
    3. **프로젝트 빌드:**
        - 빌드하고자 하는 Job 확인 후 빌드 버튼 클릭
        - 빌드 시점 선택 후 빌드 실행
        - 빌드 결과 확인
- **서버 설정:**
    - **Gunicorn:** WSGI 서버로 Django 애플리케이션 실행
    - **Nginx:** 리버스 프록시 설정, 정적 파일 제공

## 7. 운영 서버 크론 작업

- **일일 작업**
    - 계약 만기 알림톡 발송 API 호출(**`/home/rocky/cron/daily_job.sh`**)
        - 매일 오전 9시에 실행
    - 운영 데이터베이스 백업 (**`/home/rocky/cron/ver2_mysqldump.sh`**)
        - 매일 오후 11시 55분에 실행

- **월간 작업**
    - 임대료(lmpayment) 자동 생성 (**`/home/rocky/cron/monthly_job.sh`**)
        - 매월 1일 오전 2시에 실행
    - 건물관리비 핀 고정 항목 생성 (**`/home/rocky/cron/pmpayment_monthly_create.sh`**)
        - 매월 25일 오전 2시에 실행
    - 이슈 스프레드 시트 업데이트 (**`/home/rocky/cron/issue_export_spread.sh`**)
        - 매월 마지막 날 오후 11시 55분에 실행

- **분기 작업**
    - 임차사 일괄 안내 알림톡 발송 (**`/home/rocky/cron/quarter_job.sh`**)
        - 2, 5, 8, 11월 15일 오전 10시에 실행

## 8. 인프라

### EC2

| 서버 ID                | 용도                | 운영체제          | Nginx 버전 | 비고                             |
|----------------------|--------------------|------------------|------------|----------------------------------|
| prd-doongji-openvpn  | 운영 VPN 용도       | Rocky Linux 8.9  | 1.14.1     |                                  |
| test-doongji-openvpn | 테스트 VPN 용도     | Rocky Linux 8.9  | 1.14.1     |                                  |
| prd-doongji-batch    | 추후 구현 기능 대비 | Rocky Linux 8.9  | 1.14.1     |                                  |
| prd-doongji-gpu      | 추후 구현 기능 대비 | Rocky Linux 8.9  | 1.14.1     |                                  |
| prd-doongji-backend  | 회사 홈페이지 백엔드, 플랫폼 백엔드 배포 | Rocky Linux 8.9  | 1.14.1     | pyenv 2.3.35<br>홈페이지: envs/ver1<br>플랫폼: envs/doongji2 |
| test-doongji-backend | 플랫폼 테스트 백엔드 배포 | Rocky Linux 8.9  | 1.14.1     | envs/doongji2                    |
| prd-doongji-front    | 프론트엔드 배포      | Rocky Linux 8.9  | 1.14.1     | nvm 0.39.1<br>Node 20.11.1<br>npm 10.2.4 |
| test-doongji-front   | 프론트엔드 테스트 배포 | Rocky Linux 8.9  | 1.14.1     | nvm 0.39.1<br>Node 20.11.1<br>npm 10.2.4 |

### RDS

| RDS 인스턴스 ID     | 용도                | 비고                             |
|--------------------|--------------------|----------------------------------|
| prd-doongji-mysql  | 서비스, 테스트 스키마 모두 포함 |                                  |

### S3 및 CDN

| S3 버킷 ID          | 용도                | 비고                             |
|--------------------|--------------------|----------------------------------|
| doongji-s3         | 서비스 용도         | 클라우드프론트 연동              |
| doongji-s3-test    | 테스트 용도         | 클라우드프론트 연동              |

---
