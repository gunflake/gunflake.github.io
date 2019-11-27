# SQL 활용

## 목차

### 조인(Join)
1. EQUI 조인(등가 조인)
    - 조인은 여러 개의 릴레이션을 사용해서 새로운 릴레이션을 만드는 과정이다.
    - 조인의 가장 기본은 교집합을 만드는 것이다.
    - 두 개의 테이블 간에 일칳는 것을 조인한다.

2. INNER JOIN
    - EQUI 조인과 마찬가지로 ISO 표준 SQL로 INNER JOIN이 있다. INNER JOIN은 ON구를 사용해서 테이블을 연결한다.
    - 해시함수는 테이블을 해시 메모리에 적재한 후에 해시함수로써 연결하는 방법이다.
    - 해시조인은 EQUI 조인만 사용 가능한 방법이다.

3. INTERSECT 연산
    - INTERSECT 연산은 두 개의 테이블에서 교집합을 조회한다.
    - 두 개 테이블에서 공통된 값을 조회한다.

4. OUTTER JOIN
    - 두 개의 테이블 간에 교집합을 조회하고 한쪽 테이블에만 있는 데이터도 포함시켜서 조회한다.

5. CROSS JOIN
    - 조인 조건 구 없이 2개의 테이블을 하나로 조인한다.
    - 조인구가 없기 때문에 카테시안 곱이 발생한다.
    - 개수 = A테이블 행 * B 테이블 행 

6. UNION
    - 두 개의 테이블을 하나로 만드는 연산이다.
    - 중복된 데이터를 제거한다.

7. UNION ALL
    - 중복된 데이터를 포함한다.

8. MINUS
    - MINUS 연산은 두 개의 테이블에서 차집합을 조회한다. 즉, 먼저 쓴 SELECT문에는 있고 뒤에 쓰는 SELECT문에는 없는 집합을 조회하는 것이다.
    - EXCEPT 연산과 동일하다.

### 계층형 조회
- Oracle 데이터베이스에서 지원하는 것으로 계층형으로 데이터를 조회할 수 있다.
- 트리 형태의 구조를 탐색하면서 조회하는 것이다.
- Connect by 는 트리 형태의 구조로 질의를 수행하는 것으로 START WITH구는 시작 조건을 의미하고 CONNECT BY PRIOR는 조인 조건이다. Root 노드로부터 하위 노드의 질의를 실행한다.
- 계층형 조회에서 최대 계층의 수를 궇기 위한 문제, MAX(LEVEL)을 사용하여 최대 계층 수를 구한다.
- LPAD 함수 : 계층형 조회 결과를 명확히 보기 위해 사용

### 서브쿼리
1. Main Query와 Subquery
    - SELECT문 내에 다시 SELECT문을 사용하는 SQL문이다.
    - FROM구에 SELECT문을 사용하는 인라인 뷰와 SELECT문에 Subquery를 사용하는 스칼라 서브쿼리 등이 있다.
    - WHERE구에 SELECT문을 사용하면 서브쿼리라고한다.

    ```SQL
    SELECT *
    FROM EMP
    WHERE DEPTNO =
        (SELECT DEPTNO FROM DEPT
            WHERE DEPTNO = 10)
    # Where 구에 있는 SELECT문은 서브쿼리이고 괄호 내에 SELECT문을 사용한다.
    # Subquery 밖에 있는 SELECT문은 메인쿼리이다.
    ```

    ```SQL
    SELECT *
    FROM (SELECT ROWNUM NUM,ENAME FROM EMP) a
    WHERE NUM < 5
    # From 구에 있는 SELECT문을 인라인 뷰라고 한다.
    # FROM 구에 SELECT문을 사용해서 가상의 테이블을 만들 수 있다.
    ```

2. 단일 행 Subquery와 다중 행 Subquery
    - 서브쿼리는 반환하는 행 수가 한개인 것과 여러 개인 것에 따라서 단일 행 서브쿼리와 멀티 행 서브쿼리로 분류된다.
    - 단일 행 서브쿼리는 단 하나의 행만 반환하는 서브쿼리로 비교 연산자를 사용한다.
    - 다중 행 서브쿼리는 여러 개의 행을 반환하는 것으로 IN, ANY, ALL, EXISTS를 사용해야 한다.

3. 다중 행 서브쿼리
    1. IN
        - 반환되는 여러 개의 행 중에서 하나만 참이 되어도 참이 되는 연산이다.

        ```SQL
        SELECT ENAME, DNAME, SAL
        FROM EMP, DEPT
        WHERE EMP.DEPTNO = DEPT.DEPTNO
        AND EMP.EMPNO
            IN (SELECT EMPNO FROM EMP
                WHERE SAL > 2000)
        # EMP테이블에서 SAL이 2000원 이상인 사원번호를 반환하고, 반환된 사원번호와 메인쿼리에 있는 사원번호와 비교해서 같은 것을 조회한다.
        ``` 

    2. ALL
        - 메인쿼리와 서브쿼리의 결과가 모두 동일하면 참이 된다.

        ```SQL
        SELECT *
        FROM EMP
        WHERE DEPTNO <= ALL (20, 30)
        # DEPTNO가 20, 30보다 작거나 같으면 조회된다.
        ```

    3. EXISTS
        - 어떤 데이터 존재 여부를 확인한다.
        - 참과 거짓이 반환된다.

        ```SQL
        SELECT ENAME, DNAME, SAL
        FROM   EMP, DEPT
        WHERE  EMP.DEPTNO = DEPT.DEPTNO
        AND    EXISTS (SELECT 1 FROM EMP WHERE SAL > 2000)
        # 급여가 2000원 초과인 사원이 있으면 TRUE가 되서 모든 전체결과가 나온다.
        ```

    4. Scala Subquery
        - 반드시 한 행과 한 컬럼만 반환하는 서브쿼리이다.
        - 만약 여러 행이 반환되면 오류가 발생한다.

        ```SQL
        SELECT ENAME, SAL, (SELECT AVG(SAL) FROM EMP) AS "평균급여"
        FROM   EMP
        WHERE  EMPNO = 1000
        # subquery에서 한행만 조회 되어야한다.
        ```

    5. 연관(Correlated) Subquery
        - Subquery 내에서 Main Query 내의 칼럼을 사용하는 것을 의미한다.

        ```SQL
        SELECT *
        FROM EMP a
        WHERE a.DEPTNO =
            (SELECT DEPTNO FROM DEPT b
            WHERE b.DEPTNO = a.DEPTNO)
        ```

### 그룹 함수(Group Function)
1. ROLLUP
    - GROUP BY의 칼럼에 대해서 Subtotal을 만들어 준다.
    - GROUP BY구에 칼럼이 두 개 이상 오면 순서에 따라 달라진다.

    ```SQL
    SELECT DECODE(DEPTNO, NULL, '전체합계', DEPTNO), SUM(SAL)
    FROM   EMP
    GROUP BY ROLLUP(DEPTNO)
    # ROLLUP을 사용하면 부서별 합계 및 전체합계가 계산된다.
    # NULL이면 전체합계 문자를 출력하기 위해 DECODE 함수 사용
    ```
    
2. GROUPING 함수
    - ROLLUP, CUBE, GROUPING SETS에서 생성되는 합계 값을 구분하기 위해서 만들어진 함수이다.
    - 예를 들어 소계, 합계 등이 계산되면 GROUPING 함수는 1을 반환하고 그렇지 않으면 0을 반환해서 합계 값을 식별할 수 있다.

    ```SQL
    SELECT DEPTNO, GROUPING(DEPTNO), JOB, GROUPING(JOB), SUM(SAL)
    FROM   EMP
    GROUP BY ROLLUP(DEPTNO, JOB)
    ```

    - 결과

        | DEPTNO | GROUPING(DEPTNO) | JOB      | GROUPING(JOB) | SUM(SAL) |
        | ------ | ---------------- | ---      | ------------- | -------- |
        |  10    |    0             | CLERK    | 0             | 1300     |
        |  10    |    0             | SALESMAN | 0             | 2450     |
        |  10    |    0             |          | 1             | 3750     |
        |  20    |    0             | CLERK    | 0             | 1900     |
        |  20    |    0             | SALESMAN | 0             | 6000     |
        |  20    |    0             |          | 1             | 7900     |
        |        |    1             |          | 1             | 11650    |

3. GROUPING SETS 함수
     - GROUP BY에 나오는 컬럼의 순서와 관계없이 다양한 소계를 만들 수 있다.
     - GROUP BY에 나오는 컬럼의 순서와 관계없이 개별적으로 모두 처리한다.

     ```SQL
     SELECT DEPTNO, JOB, SUM(SAL)
     FROM   EMP
     GROUP BY GROUPING SETS(DEPTNO, JOB)
     ```

    - 결과

        | DEPTNO | JOB | SUM(SAL) |
        | ------ | --- | -------- |
        |        | CLERK | 4150 |
        |        | SALESMAN | 5600 |
        |        | MANAGER | 3000 |
        | 10     |        | 1500 |
        | 20     |        | 2400 |
    

4. CUBE 함수
    - CUBE함수에 제시한 칼럼에 대해서 결합 가능한 모든 집계를 계산한다.
    - 다차원 집계를 제공하여 다양하게 데이터를 분석할 수 있게 한다.
    - 부서와 직업을 CUBE로 사용하면 부서별 합계, 직업별 합계, 부서별 직업별 합계, 전체 합계가 조회된다. (즉, 조합할 수 있는 모든 경우의 수가 조합된다.)

### 윈도우 함수
1. 윈도우 함수
    - 행과 행 간의 관계를 정의하기 위해서 제공되는 함수이다.
    - 순위, 합계, 평균, 행 위치 등을 조작할 수 있다.

- 상세 함수들 생략...  

### 테이블 파티션
1. 기능
    - 대용량의 테이블을 여러 개의 데이터 파일에 분리해서 저장한다.
    - 테이블의 데이터가 물리적으로 분리된 데이터 파일에 저장되면 입력, 수정, 삭제, 조회 성능이 향상된다.
    - 파티션을 각각의 파티션 별로 독립적으로 관리할 수 있다. 
    - 파티션 별로 백업하고 복구가 가능하면 파티션 전용 인덱스 생성도 가능하다.
    - Oracle 데이터베이스의 논리적 관리 단위인 테이블 스페이스 간에 이동이 가능하다.
    - 데이터를 조회할 때 데이터의 범위를 줄여서 성능을 향상시킨다.

2. Range Partition
    - 테이블의 컬럼 중에서 값의 범위를 기준으로 여러 개의 파티션으로 데이터를 나누어 저장한다.
    - 예를 들어, SAL 값 2000~4000은 Datafile1.dbf 파일에 저장하고, SAL 값 5000~7000은 Datafile2.dbf 파일에 저장한다.

3. List Partition
    - 특정 값을 기준으로 분할하는 방법이다.
    - 예를 들어, DEPTNO가 10번인 값은 Datafile1.dbf 파일에 저장하고, DEPTNO가 20번인 값은 Datafile2.dbf 파일에 저장한다.

4. Hash Partition
    - 데이터베이스 관리 시스템이 내부적으로 해시함수를 사용해서 데이터를 분할한다.
    - 데이터베이스 관리 시스템이 알아서 분할하고 관리하는 것이다.

5. Partition Index
    - 파티션 키를 사용해서 인덱스를 만드는 Prefixed index와 해당 파티션만 사용하는 Local Index 등으로 나누어진다.
    - Oracle 데이터베이스는 Global Non-Prefixed를 지원하지 않는다.
    
