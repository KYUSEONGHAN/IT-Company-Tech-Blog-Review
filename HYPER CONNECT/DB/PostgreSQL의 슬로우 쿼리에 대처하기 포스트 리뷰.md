![](https://lh3.googleusercontent.com/jRSyFkr70V71AR_wmx-yc_Wy-hnBn01Ao9QU2Iomz_3-lrXfm1RfsJnSTv8R81YAh93JKp4DiHsu1QkDeE8phASSslGjOPJJ3M3N)

## 목차
1. 개발 배경
2. 어떤 쿼리가 문제인지 확인하기
3. APM에서 문제가 되는 쿼리 찾기
4. 쿼리의 실행 계획을 알려주는 Explain
5. PostgreSQL에서 지원하는 Join 알고리즘
6. 인덱스는 최소 개수로 유지
7. 카디널리티가 높은 컬럼에 인덱스 추가
8. 테이블의 일부만 주로 검색하는 경우, 부분 인덱스를 활용
9. 성능 향상
10. Lessons Learned

## 1. 개발 배경
- 모니터링 시스템에서 데이터베이스의 CPU 사용량이 지속해서 높다는 경고를 보냄 (90% 이상)

## 2. 어떤 쿼리가 문제인지 확인하기
- 예전에는 DB 부하를 줄이기 위해 WAS 개발자가 작성한 SQL 쿼리들을 DBA들이 전부 검수
- 하지만 요즘은 ORM 기술이 발전하고 DBA같은 전문인력을 해당 작업에 투입하는건 비효율적.
- ORM이 만드는 모든 SQL 쿼리를 최적화할 필요는 없다.

## 3. APM에서 문제가 되는 쿼리 찾기
- APM (SaaS 중 하나)
- AWS에는 RDS를 모니터링해주는 Performance Insight라는 기능이 있다.
- 슬로우 쿼리를 찾을 때 매우 요긴하게 사용할 수 있다
- SQL 쿼리가 RDS 인스턴스에 어느 정도의 영향을 미치는지 보기에 좋다.
![](https://hyperconnect.github.io/assets/2020-08-31-improve-slow-query/aws-performance-insight.png)
- 어플리케이션 레벨에서 가시성을 더 쉽게 파악하기 위해, Datadog라는 APM 모니터링 솔루션을 사용하고 있다.
- 이를 활용하면 query 별 TPS와 지연시간 등을 볼 수 있다.
- 이를 통해, 어떤 SQL 쿼리가 얼마나 많이 사용되고 있으며, 얼마나 느린지를 파악하는데 도움이 된다.
![](https://hyperconnect.github.io/assets/2020-08-31-improve-slow-query/datadog.png)
- 1, 2번째 쿼리는 p99 레이턴시가 가장 높지만, 요청수가 높지 않아 최적화 대상이 아니다.
- 3, 4, 5번째 쿼리는 p99 레이턴시가 5~6초이며, p50 레이턴시도 역시 1초대이다. 요청 수도 많기 때문에 최적화가 필요하다가 판단할 수 있다.

## 4. 쿼리의 실행 계획을 알려주는 Explain
- 문제가 되는 쿼리를 확인했으니 다음으로 확인할 것은 실행 계획을 확인하는 것이다.
- 실행 계획이란 데이터베이스 옵티마이저가 어떻게 쿼리를 수행할 것인지를 세우는 계획이다.
- 이를 확인하려면 select 앞에 explain을 붙이면 된다.

## 5. PostgreSQL에서 지원하는 Join 알고리즘
- PosgreSQL은 다음과 같은 세 가지의 Join 알고리즘을 지원.
    - Nested Join
    - Hash Join
    - Merge Join

## 6. 인덱스는 최소 개수로 유지
- 데이터 풀 스캔을 하는 쿼리를 최적화할 때, 일반적인 해결책은 인덱스를 거는 것이다.
- 인덱스의 개수는 최소한으로 유지하는 것이 좋다.

## 7. 카디널리티가 높은 컬럼에 인덱스 추가
- 중복도가 낮으면 카디널리티가 높고, 중복도가 높으면 카디널리티가 낮다고 한다.
- 인덱스를 통한 select 성능 향상은 조회 대상 데이터의 양을 줄이는 것이 목적이므로, 일반적으로 카디널리티가 높은 컬럼에 인덱스를 걸기를 권장하고 있다.

## 8. 테이블의 일부만 주로 검색하는 경우, 부분 인덱스를 활용
- 테이블이 아주 크고, 대부분의 쿼리가 일부분의 열만 조회한다면 인덱스를 일부분만 만들어 주는 것이 좋다.

## 9. 성능 향상
- RDS CPU 지표
![](https://hyperconnect.github.io/assets/2020-08-31-improve-slow-query/rds-cpu.png)

## 10. Lessons Learned
- 데이터베이스의 인덱스를 실험할 때는 프로덕션 데이터베이스를 복제해서 실험하는 것이 가장 좋다.
- PosgreSQL에서는 테이블의 일부에만 인덱스를 거는 부분 인덱스 기능이 있다.
    - 큰 테이블에서 일부 데이터만 주로 쿼리하는 경우 이를 잘 활용하면 성능 향상이 된다.
- PostgreSQL에서는 표현식에도 인덱스를 걸 수 있다.
- order by 조건에 인덱스를 태우려면, 복합 인덱스를 걸 때 order by 절의 정렬 조건을 맨 앞에 붙이고, where절의 컬럼들은 그 뒤에 붙여야 옵티마이저가 인덱스르 사용한다.
    - 이 때, 복합 인덱스의 순서도 맞아야 하고 asc, desc 여부도 맞아야 한다. 하나라도 틀리면 인덱스를 타지 않는다.
- 인덱스를 추가해도 옵티마이저가 인덱스를 사용하지 않는게 더 빠르다고 판단되면, 인덱스를 타지 않고 full table scan을 진행
- postgresql의 explain을 사용할 때, postgres explain visualizer 2 라이브러리를 사용하면 좋다.

## 참고자료 출처
[https://hyperconnect.github.io/2020/08/31/improve-slow-query.html](https://hyperconnect.github.io/2020/08/31/improve-slow-query.html)