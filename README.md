### 프로젝트 이름

- Heady (혈당 관리 서비스)

### 프로젝트 소개

- **Heady**는 사용자의 혈당을 체계적으로 관리하고 분석할 수 있는 웹 애플리케이션입니다. 
혈당 기록, 영양소 추적, 통계 분석 등의 기능을 제공하여 사용자의 건강한 생활 습관 형성을 돕습니다.
- **기간**: 2025.04.01 ~ 2025.05.28
- **인원**: 1명 (개인 프로젝트)
- **배포 링크** : https://healthsteady.site
    - **guest 계정**
        - guest123@gmail.com
        - guest!123
- **깃헙 링크** : https://github.com/Joowon-Seo/heady-back
<p align="center">
  <table align="center">
    <tr>
      <td align="center">
        <p><b>메인 화면</b></p>
        <img src="https://github.com/user-attachments/assets/9fc5f64d-9daf-4f28-bef2-6a75f1ee265d" width="90%">
      </td>
      <td align="center">
        <p><b>기록 화면</b></p>
        <img src="https://github.com/user-attachments/assets/278a1f9c-7ab5-429a-a4cc-d1172bf80338" width="90%">
      </td>
      <td align="center">
        <p><b>통계 화면</b></p>
        <img src="https://github.com/user-attachments/assets/2fc821d9-2b66-4b46-a5dc-385290a4f02e" width="90%">
      </td>
      <td align="center">
        <p><b>내 정보 화면면</b></p>
        <img src="https://github.com/user-attachments/assets/7e5d4021-d534-493f-b500-1e157e3682c9" width="90%">
      </td>
    </tr>
  </table>
</p>

<p align="center">
  <table align="center">
    <tr>
      <td align="center">
        <p><b>AI 분석 1</b></p>
        <img src="https://github.com/user-attachments/assets/d55e3828-fad8-458d-b050-4cd3f3bdfd31" width="90%">
      </td>
      <td align="center">
        <p><b>AI 분석 2</b></p>
        <img src="https://github.com/user-attachments/assets/90ec4a2a-00d3-4429-8e16-0ae7eb7167b5" width="90%">
      </td>
    </tr>
  </table>
</p>

### 주요 내용

- **혈당 관리**
    - **실시간 혈당 기록**: 아침, 점심, 저녁 혈당 수치 입력 및 추적
    - **목표 설정**: 개인별 공복/식후 혈당 목표값 설정
    - **트렌드 분석**: 혈당 변화 패턴 시각화 및 분석
- **식사 및 영양 관리**
    - **식사 기록**: 상세한 식사 내용 입력 및 관리
    - **영양소 추적**: 탄수화물, 단백질, 지방 섭취량 모니터링
    - **칼로리 계산**: 일일 칼로리 섭취량 자동 계산

- **분석 및 리포팅**
    - **건강 리포트**: AI 기반 개인화된 건강 분석 리포트 생성
    - **통계 대시보드**: 혈당 패턴 및 영양소 섭취 현황 시각화
    - **PDF 내보내기**: 건강 데이터를 PDF로 내보내기 가능
- **사용자 관리**
    - **카카오 소셜 로그인**: 회원가입 및 로그인
    - **개인 프로필**: 사용자 정보 및 건강 목표 관리
    - **데이터 백업**: 클라우드 기반 데이터 동기화

### 사용 기술

- **Frontend**
    - Next.js , React , TypeScript, Tailwind CSS
- **Backend**
    - SpringBoot 3.4, Java 17, JPA, Querydsl, AWS RDS(MySQL), EC2, Gemini 2.0 flash, Docker, Github Actions, 식품영양정보 Open API, Prometheus, Grafana

### 아키텍처
![Heady System Architecture](https://github.com/user-attachments/assets/60ce3488-ea5d-4425-b389-e11b80dce973)

### ERD
![heady ERD](https://github.com/user-attachments/assets/6d70b371-c4b1-43a8-aa79-a4c9df404d0c)




---
### 트러블 슈팅 - 1

**[로그인 API 비밀번호 해싱 병목 현상 및 성능 최적화](https://blog.naver.com/smileman___/223891986773) (결과: 평균 응답 시간 504ms → 75ms)**

- **문제 발생**
    - 로그인 API 평균 응답 시간 **500ms 초과**, 부하 시 **1초 이상 급증**하는 심각한 지연 발생. 상세 로깅 결과 **비밀번호 해시 검증 구간에서 200ms 이상 소요되는 것이 주된 병목** 확인
        ![image](https://github.com/user-attachments/assets/bf980bb9-33ab-4320-8de2-add558f008f4)
        

- **실패한 시도**
    - **JPA 쿼리 과다 로딩 문제 의심**: EXPLAIN을 통해 인덱스 적용 여부를 확인, 적절히 인덱스를 타고 있었으며 DB 조회 속도는 병목이 아님
      ![image](https://github.com/user-attachments/assets/3bfc4800-09c4-43e8-8e83-9f5cbb7084e3)


        
    
    - **Argon2id 적용**: BCrypt 대안으로 Argon2id 알고리즘 적용했으나, 메모리 집약적 특성으로 잦은 Major GC 발생 및 **평균 120ms, 최대 423ms에 달하는 STW 시간 유발**하여 오히려 전체 응답 지연 심화
        ![image](https://github.com/user-attachments/assets/f903a3e4-fe4f-4f83-92cd-a79d3d0c9809)

        
    
    - **GC 튜닝 및 스레드 풀 조정**: Argon2id 적용 시 발생한 GC 문제 해결 위해 -Xms256m -Xmx256m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 옵션 사용하여 JVM GC 튜닝 시도 및 스레드 풀 조정. 이로 인해 **GC STW 시간 평균 4.53ms, 최대 7.80ms 수준으로 크게 감소**하는 성과를 얻었으나, 비밀번호 해시 검증이라는 CPU 및 메모리 집약적 작업 본질적인 시간 단축시키지 못해 유의미한 전체 응답 시간 개선은 이끌어내지 못함
        
        ![image](https://github.com/user-attachments/assets/2f83392f-5628-425f-809f-e60e684dec1e)

        

- **최종 해결 전략**
    - **BCrypt cost 값 조정**: BCrypt 알고리즘 cost 값을 **10에서 9로 낮춰** 해싱 연산 부담 줄임
    - **서버 인스턴스 스케일 업**: t2.micro 인스턴스 CPU 자원 한계 인지, **t3.medium으로 서버 인스턴스 스케일 업**하여 CPU 집약적인 BCrypt 연산에 더 많은 자원 할당

- **결과: 평균 응답 시간 504ms → 75ms, 최대 응답 시간 1.03s → 135ms 대폭 감소**
    
    ![image](https://github.com/user-attachments/assets/812b4c4c-b0a4-4c02-84ab-d5614dcbd4b4)

    



---
### 트러블 슈팅 - 2

**[음식 검색 API Full Scan 및 Slow Query 문제 해결](https://blog.naver.com/smileman___/223889586212) (결과: 평균 응답 시간 552ms → 23.4ms)**

- **문제 발생**
    - 음식 이름 검색 API **평균 552ms 이상 응답 지연** 발생
        
        ![image](https://github.com/user-attachments/assets/b8975820-e539-493f-9e5c-d138b8322f3a)

        
    
    - RDS 모니터링에서 **평균 4.6/sec, 최대 10/sec Slow Queries** 관측.
        
        ![image](https://github.com/user-attachments/assets/0de2b3e1-bd1f-4d70-90f5-b1f2eaf45258)

        
    
    - HikariCP 커넥션 점유 시간 **최대 436ms까지 상승**하여 커넥션 풀 병목 현상 유발.
        
        ![image](https://github.com/user-attachments/assets/16ff2391-8814-4d67-923f-03903ac19487)

        
    
    - EXPLAIN 분석 결과, 기존 LOWER(name) LIKE '%keyword%' 쿼리는 **type=ALL (Full Scan)과 Extra=Using filesort (정렬 비용) 발생**하며 인덱스 전혀 사용하지 않는 것이 근본적인 원인
        
        ![image](https://github.com/user-attachments/assets/d759578e-4f64-4f16-a8a3-a71448c3103d)

        

- **해결 과정**
    - 문제 분석 및 대안 검토: LIKE '검색어%' 방식은 B-Tree 인덱스 활용 가능하나, 부분/중간 일치 검색 비즈니스 요구사항 충족 불가. Elasticsearch 도입은 현 시스템 규모 대비 과도한 선택이었으며, Redis 캐시도 직접적인 검색 인덱스 대안 아님
    - **Full-Text Index와 N-gram Parser 도입 결정**: 중간 문자열 검색 지원하고, 검색 및 정렬 모두에 인덱스 활용 가능한 MySQL Full-Text Index와 한글 처리 위한 N-gram Parser 조합 가장 현실적인 대안으로 판단

- **구현 절차**
    - **RDS 파라미터 및 인덱스 설정**: RDS ngram_token_size=2 설정 후 인스턴스 재시작, ALTER TABLE foods ADD FULLTEXT INDEX idx_food_name_ft(name) WITH PARSER ngram;
        
        ![image](https://github.com/user-attachments/assets/42eedf7b-a871-41aa-a77c-7314b43b29b5)

        
    
    - 명령어로 N-gram Full-Text 인덱스 생성
        
        ![image](https://github.com/user-attachments/assets/7f674efa-baac-468d-9ca9-a822c472af03)

        
    
    - **쿼리 구조 변경**: 기존 LIKE 기반 JPQL 대신, MATCH(name) AGAINST(:keyword IN NATURAL LANGUAGE MODE)를 사용하는 Native Query로 변경하여 Full-Text 인덱스 명시적으로 활용
        
        ![image](https://github.com/user-attachments/assets/15900fd3-61aa-4b30-9f7c-d8b21121c768)

        
    
- **결과**
    - 음식 검색 API **평균 응답 시간 552ms 이상에서 23.4ms로 약 23.6배 단축**.
        
        ![image](https://github.com/user-attachments/assets/75cbcae7-ec31-4762-a396-e761c559b874)

        
    
    - 이와 함께 MySQL Slow Queries는 **95% 이상 감소**,
        
        ![image](https://github.com/user-attachments/assets/6aeb3a6f-bafb-4630-9f8d-4aa14ff2ee8a)


        
    
    - HikariCP 커넥션 점유 시간은 **최대 436ms에서 235ms로 약 46% 감소**하여 시스템 안정성 크게 향상
        
        ![image](https://github.com/user-attachments/assets/4ce2654c-1fc1-4b81-a847-216ebe59d92f)

        

### 프로젝트 회고

**아쉬운 점**

- **실시간 혈당 데이터 동기화의 한계**: 현재 Heady는 사용자가 직접 혈당 수치를 입력하는 방식에 의존하고 있어, 연속 혈당 측정기(CGM)와 같은 외부 기기와의 연동을 통한 실시간 데이터 수집 및 분석 기능이 부족합니다. 이는 사용자 편의성을 저해하고, 더 정확한 혈당 변화 추이를 파악하는 데 한계로 작용합니다.
- **음식 데이터베이스의 확장성 및 정확성**: 식품영양정보 Open API를 활용했으나, 모든 음식에 대한 상세한 영양 정보나 다양한 조리법에 따른 데이터 반영은 미흡했습니다. 사용자 입력에만 의존하기보다, 이미지 인식 기반의 식단 분석이나 더 광범위한 음식 데이터베이스를 구축했다면 영양소 추적의 정확성과 편리성을 더욱 높일 수 있었을 것입니다.

**배운 점**

- **성능 최적화의 심층적 이해와 문제 해결 역량 강화**: 로그인 API의 비밀번호 해싱 병목 현상과 음식 검색 API의 Slow Query 문제를 해결하는 과정에서 **성능 튜닝의 깊은 지식과 다각적인 접근 방식**을 체득했습니다. 특히 BCrypt cost 값 조정, 서버 스케일업, MySQL Full-Text Index 및 N-gram Parser 도입 등 다양한 시도를 통해 **실질적인 성능 개선(최대 27배 단축)**을 이뤄내며, **문제를 근본적으로 분석하고 최적의 해결책을 찾아 적용하는 역량**을 크게 향상시켰습니다.
- **견고한 아키텍처 설계 및 인프라 구축 경험**: **Prometheus와 Grafana를 활용한 모니터링 시스템 구축**을 통해 문제 원인을 시각적으로 파악하고, AWS EC2, RDS, Docker, GitHub Actions 등을 활용하여 **안정적인 서비스 인프라를 구축하고 CI/CD 파이프라인을 자동화**하는 경험을 쌓았습니다. 특히 EC2 퍼블릭 IP 변경 문제 해결 및 [**월 $1.55의 네트워크 리소스 비용을 $0으로 절감**한 경험](https://blog.naver.com/smileman___/223888966345)은 효율적인 클라우드 자원 관리의 중요성을 일깨워주었습니다.
