![](https://woowahan-cdn.woowahan.com/static/image/share_kor.jpg)

## 목차
1. Vacuum이란?
2. MVCC란?
3. MySQL의 MVCC
4. PostgreSQL의 MVCC
5. Vacuum이 필요한 이유 - Dead Tuple 정리
6. Vacuum이 필요한 이유 - Transaction ID Wraparound 방지
7. AutoVacuum은 언제 호출될까?
8. Vacuum 관련 파라미터는 어떻게 튜닝할까?
9. Vacuum이 실패하고 있다면?
10. 마치며

## 1. Vacuum이란?
- postgresql의 진공청소기 역할을 하는 동작.
- 개발자분들에게 친숙한 GC (Garbage Collector)의 역할을 하는 동작이라고 생각하면 된다.
- vacuum을 DB단에서 자동으로 수행하는 동작을 AutoVacuum이라고 한다.
- 바큠은 아래 4가지 작업을 수행.
    - 임계치 이상으로 발생한 dead tuple 정리 FSM(free space map) 반환.
    - transaction id wraparound 방지
    - 통계정보 갱신
    - visibility map을 갱신하여 index scan 성능 향상
- 위 4가지 동작 중 가장 중요한 것은 2번 transation id wraparound 방지 동작이다.

## 2. MVCC란?
- multi version concurrency control
- 동시에 여러 트랜잭션이 수행되는 환경에서 각 트랜잭션에게 쿼리 수행 시점의 데이터를 제공하여 읽기 일관성을 보장하고 Read/Write 간의 충돌 및 lock을 방지하여 동시성을 높일 수 있는 기능
- 트랜잭션이 시작된 시점의 transaction id와 같거나 작은 transaction id를 가지는 데이터를 읽는 것.

## 3. MySQL의 MVCC
![](https://techblog.woowahan.com/wp-content/uploads/2022/11/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA-2022-11-07-%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE-9.59.33-600x434.png)
- 트랜잭션에서 변경된 데이터가 UNDO 영역에 저장되고, 그 뒤의 변경된 내용들은 앞선 내용들을 포인터로 가리키는 형태

## 4. postgresql의 mvcc
![](https://techblog.woowahan.com/wp-content/uploads/2022/11/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA-2022-11-24-%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB-3.37.28-600x180.png)
- postgresql은 데이터 페이지 내에 변경되기 이전 tuple과 변경된 신규 tuple을 같은 page에 저장하고 tuple별로 생성된 시점과 변경된 시점을 기록 및 비교하는 방식으로 mvcc를 제공

## 5. Vacuum이 필요한 이유 - Dead Tuple 정리
- sql update가 완료되면 기존 원본 데이터를 저장한 tuple은 어디에도 참조되지 않는 tuple이 되는데 이를 쓸모없는, 죽은 데이터라는 의미로 dead tuple이라고 합니다.
- 문제는 이 dead tuple이 자동으로 정리되거나 FSM으로 반환되지 않기 때문에 쓸데없이 공간만 차지할 뿐만 아니라, 쓸데없이 공간만 차지하는 Dead Tuple 때문에 DB 때문에 더 많은 page를 읽게 되고 이는 쿼리 성능에도 약영향을 끼친다.
![](https://techblog.woowahan.com/wp-content/uploads/2022/11/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA-2022-11-24-%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB-12.37.14-600x455.png)
- vacuum과 vacuum full 차이
    -  그림처럼 일반 vacuum, auto vacuum을 수행했을 때는 OS 디스크의 공간 반환까지는 처리되지 못하고 FSM에만 반환되어 재사용할 수 있게끔만 처리된다.
    - OS 디스크의 공간 반환까지 처리하려면 vacuum full이라는 작업을 수행해야 한다.
    - 이 작업은 운영 중에는 할 수 없는 작업

## 7. AutoVacuum은 언제 호출될까?
- vacuum 동작을 DB단에서 임계치에 따라 자동으로 수행하는 것이 AutoVacuum이라고 하며, AutoVacuum은 기본적으로 아래 두 상황에서 수행
    - dead tuple의 개수의 누적치가 임계치에 도달했을 때
    - table이나 tuple의 age가 누적되어 임계치에 도달했을 때
    
## 8. Vacuum 관련 파라미터는 어떻게 튜닝할까?
- auto vacuum을 설정하는데 있어 가장 중요한 것은 적절한 빈도로 수행되도록 하는 것.
- 보통 vacuum 동작은 비용이나 부하가 많이 필요한 작업이여서 점검일정 또는 한가한 새벽시간대에 돌린다.

## 9. Vacuum이 실패하고 있다면?
- auto vacuum은 흔히 아래 3가지 상황에서 실패한다.
    - long transaction이 수행되는 경우
    - 미사용의 버려진 replicaion slot이 있을때
    - hot_standby_feedback = on
    
## 10. 마치며
PostgreSQL은 MVCC의 구현 방법상의 특징으로 vacuum이라는 동작이 필수이고,

이 vacuum 동작을 DB단에서 여러 파라미터로 설정된 임계치를 통해 자동으로 수행하는 것이 AutoVacuum입니다.

AutoVacuum(vacuum)의 주요 목적으로는 아래 네가지가 있으며

임계치 이상으로 발생한 Dead Tuple을 정리하여 FSM (Free Space Map)으로 반환
Transaction ID Wraparound 방지
통계 정보 갱신
visibility map을 갱신하여 index scan 성능 향상
이번 글에서는 Dead Tuple 정리, Transaction ID Wraparound 방지에 대해 알아보았습니다.

성능과 디스크의 효율적 사용을 위해 Dead Tuple 정리도 중요하지만

DB의 지속적인 운영을 위해서는 vacuum을 통한 Transaction ID Wraparound 방지 작업이 중요하다는 것과

AutoVacuum이 호출되는 경우에 대해 테스트를 해보면서 어떤 경우에 AutoVacuum이 호출되는지, 이 때 어떤 변화가 생기는지에 대해서도 확인해보았습니다.

Dead Tuple

vacuum threshold = autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * number of Tuples
Transaction ID (age)

테이블 age > autovacuum_freeze_max_age vacuum_freeze_table_age  <  테이블 age  < autovacuum_freeze_max_age  
상황별로 AutoVacuum이 잘 수행될 수 있도록 어떤 파라미터를 수정하면 좋을지에 대해서 살펴보면서 아래 파라미터에 대해 말씀드렸습니다

AutoVacuum이 너무 드물게 돌고있다
autovacuum_vacuum_scale_factor / threshold
autovacuum_vacuum_insert_scale_factor / threshold
AutoVacuum이 너무 느리다
autovacuum_vacuum_cost_delay
autovacuum_vacuum_cost_limit
autovacuum_naptime
autovacuum_max_workers
autovacuum_work_mem
max_parallel_maintenance_workers
마지막으로는 AutoVacuum이 실패해서 Dead Tuple이 정리되지 않을 수 있는 아래 3가지 케이스에 대해서 살펴보았습니다.

Long transaction이 수행되는 경우
버려진 replication slot이 있을때
hot_standby_feedback = ON

## 참고자료 출처
[https://techblog.woowahan.com/9478/](https://techblog.woowahan.com/9478/)