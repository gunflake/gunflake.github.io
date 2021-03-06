# SQL 기본

## 목차
- [관계형 데이터베이스](#관계형-데이터베이스)
- [DDL](#DDL)
- [DML](#DML)
- [DCL](#DCL)
- [TCL](#TCL)

### 관계형 데이터베이스
1. 특징
    - 릴레이션과 릴레이션의 조인 연산을 통해서 합집합, 교집합, 차집합 등을 만들 수 있다.

2. 데이터베이스 종류

    | 종류 | 특징 |
    | --- | --- |
    | 계층형 데이터베이스 | - 트리 형태의 자료구조에 데이터를 저장하고관리한다. <br> - 1대N 관계를 표현한다. |
    | 네트워크형 데이터베이스 | - 오너와 멤버 형태로 데이터를 저장한다. <br> - 1대N과 함께 M대N 표현도 가능하다. |
    | 관계형 데이터베이스 | - 릴레이션에 데이터를 저장하고 관리한다. <br> - 릴레이션을 사용해서 집합 연산과 관계 연산을 할 수 있다. |

3. 관계형 데이터베이스 집합 연산과 관계 연산
    - 집합 연산

        | 집합 연산 | 설명 |
        | ---- | --- |
        | 합집합 | - 두 개의 릴레이션을 하나로 합치는 것이다. <br> - 중복된 행(튜플)은 한 번만 조회된다. | 
        | 차집합 | 본래 릴레이션에는 존재하고 다른 릴레이션에는 존재하지 않는 것을 조회한다. |
        | 교집합 | 두 개의 릴레이션 간에 공통된 것을 조회한다. |
        | 곱집합 | 각 릴레이션에 존재하는 모든 데이터를 조합하여 연산한다. |

    
    - 관계 연산

        | 관계 연산 | 설명 |
        | ---- | --- |
        | 선택 연산 | 릴레이션에서 조건에 맞는 행(튜플)만을 조회한다. |
        | 투영 연산 | 릴레이션에서 조건에 맞는 속성만을 조회한다. |
        | 결합 연산 | 여러 릴레이션의 공통된 속성을 사용해서 새로운 릴레이션을 만들어 낸다. |
        | 나누기 연산 | 기준 릴레이션에서 나누는 릴레이션이 가지고 있는 속성과 동일한 값을 가지는 행(튜플)을 추출하고 나누는 릴레이션의 속성을 삭제한 후 중복된 행을 제거하는 연산이다. |


### DDL
1. 테이블 생성
    
    ```sql
    Create Table DEPT
    (
        detpno varchar2(4) primary key,
        deptname varchar2(20)
    );
    ```

    ```sql
    Create Table EMP
    (
        empno      number(10) primary key,
        ename      varchar2(20),
        sal        number(6),
        createdate date default sysdate,
        constraint deptfk foreign key (deptno)
                          references dept(deptno)
                          ON DELETE CASCADE,
        //constraint emppk primary key(empno, ename)
    );
    ```
    
    - EMP는 테이블 명이고, 영문자로 시작한다.
    - empno, ename, sal 은 테이블의 컬럼명이다.
    - number는 숫자형, varchar2는 가변길이 문자열 데이터 타입이다.
    - empno를 기본키로 지정한다.
    - `constraint`를 사용해서 **기본키**를 지정할 수 있다. 기본키가 두 개일 경우 위와 같이 (empno, ename)로 표현할 수 있다.
    - Oracle 데이터베이스에서 `sysdate`는 오늘의 날짜를 조회환다. default 옵션을 사용해서 오늘 날짜를 기본값으로 입력할 수 있다.
    - constraint를 사용해서 **외래키**를 지정할 수 있다. 참조할 테이블의 컬럼(DEPT_deptno)와 외래키로 선택할 컬럼(EMP_deptno)을 위와 같이 지정한다.
    - `CASCADE` 옵션을 사용해서 참조되는 데이터를 자동으로 삭제할 수 있는 기능이다.

2. 테이블 변경

    1. 컬럼 추가
        
        ```sql
        ALTER TABLE EMP
        ADD (age number(2) default 1);
        ```

        - EMP테이블에 age 컬럼을 추가한다.

    2. 컬럼 변경

        ```sql
        ALTER TABLE EMP
        MODIFY(ename varchar2(40) not null);
        ```

        - ename 컬럼을 varchar2(40) 타입으로 변경하고 NOT NULL 조건을 설정한다.
        - 컬럼의 데이터 타입을 변경할 때, 기존 데이터가 있는 경우 에러가 발생한다. 예를 들어 숫자 타입이고 숫자 데이터가 저장되어 있는데 문자형 데이터 타입으로 변경하면 에러가 발생한다.

    3. 컬럼 삭제

        ```sql
        ALTER TABLE EMP
        DROP COULUMN age;
        ```

        - EMP 테이블에서 age 칼럼을 삭제한다.

    4. 컬럼명 변경

        ```sql
        ALTER TABLE EMP
        RENAME COLUMN ename to new_ename;
        ```

        - EMP 테이블에 ename 칼럼명을 new_ename으로 변경한다.

3. 테이블 삭제

    ```sql
    DROP TABLE EMP;
    DROP TABLE EMP CASCADE CONSTRAINT;
    ```

    - DROP TABLE 테이블명을 사용해서 테이블을 삭제할 수 있다.
    - `CASCADE CONSTRAINT` 옵션을 사용해서 참조된 제약사항까지 모두 삭제할 수 있다.

4. 뷰(View) 생성과 삭제

    - View란 테이블로부터 유도된 가상의 테이블이다.
    - 실제 데이터를 가지고 있지 않고 테이블을 참조해서 원하는 컬럼만을 조회할 수 있게 한다.
    - 뷰는 데이터 딕셔너리(Data Dictionary)에 SQL문 형태로 저장하고 실행 시에 참조된다.
    - **특징**

        ```
        - 참조한 테이블이 변경되면 뷰도 변경된다.
        - 뷰의 검색은 참조한 테이블과 동일하게 할 수 있지만, 뷰에 대한 입력, 수정, 삭제에는 제약이 발생한다.
        - 특정 컬럼만 조회시켜서 보안성을 향상 시킨다.
        - 한 번 생상된 뷰는 변경할 수 없고 변경을 원하면 삭제 후 재생성해야한다.
        - ALTER문을 사용해서 뷰를 변경할 수 없다.
        ```

    - SQL 구문

        ```sql
        CREATE VIEW T_EMP AS
         SELECT * FROM EMP;
        ```

        - EMP 테이블을 조회해서 그 결과로 T_EMP라는 뷰를 생성한다.
        - 뷰의 생성은 `CREATE VIEW`를 사용하고 참조해야 하는 테이블을 지정해야 한다.
        - 뷰를 생성할 때 `SELECT`문을 사용하여 참조되는 테이블을 지정한다.
        - 뷰의 조회는 SELECT문을 사용해서 일반 테이블처럼 조회한다.
        - 뷰의 삭제는 `Drop view`를 사용한다. 뷰를 삭제했다고 해서 참조했던 테이블이 삭제되진 않는다.

    - **장점과 단점**

        | 장단점 | 설명 |
        | --- | --- |
        | 장점 | - 특정 칼럼만 조회할 수 있기 때문에 보안 기능이 있다. <br> - 데이터 관리가 간단하다. <br> - SELECT문이 간단해진다. <br> - 하나의 테이블에 여러 개의 뷰를 생성할 수 있다.
        | 단점 | - 뷰는 독자적인 인덱스를 만들 수 없다. <br> - 삽입, 수정, 삭제 연산이 제약된다. <br> - 데이터 구조를 변경할 수는 없다. |

### DML

1. INSERT 문
    
    - SELECT 문으로 입력
        
        ```sql
        INSERT INTO DEPT_TEST
        SELECT * FROM DEPT;
        ```

        - SELECT문을 사용하여 데이터를 조회해서 해당 테이블에 바로 삽입할 수 있다.
    
    - Nologging 사용

        ```sql
        ALTER TABLE DEPT NOLOGGING;
        ```

        - 데이터베이스에 데이터를 입력하면 로그파일(log file)에 그 정보를 기록한다.
        - Check point라는 이벤트가 발생하면 로그파일의 데이터를 데이터 파일에 저장한다.
        - Nologging 옵션은 로그파일의 기록을 최소화시켜서 입력 시에 성능을 향상시키는 방법이다.
        - Nologging 옵션은 Buffer Cache라는 메모리 영역을 생략하고 기록한다.

2. UPDATE 문

    - UPDATE문은 원하는 조건으로 검색해서 해당 데이터를 수정하는 것이다. 만약, UPDATE문에 조건문을 입력하지 않으면 모든 데이터가 수정되므로 유의해야 한다.

3. DELETE 문

    - UPDATE문과 마찬가지로 조건문을 입력하지 않으면 모든 데이터가 삭제된다.
    - DELETE문으로 데이터를 삭제한다고 해서 테이블의 용량이 초기화되지 않는다.
    - `TRUNCATE TABLE 테이블명`을 사용하면 테이블의 모든 데이터를 삭제하고, 테이블 용량을 초기화 한다.

4. SELECT 문

    - 조회 데이터 결합 

        ```sql
        SELECT ENAME || '님' FROM EMP;
        ```

        - ENAME 컬럼 뒤에 '님'이라는 문자를 결합한다. 예를 들어 `김백점님`이라고 출력된다.
    
    - Order by를 사용한 정렬
        - 정렬하는 시점은 모든 실행이 끝난 후에 개발자에게 데이터를 출력해주기 전이다.
        - 정렬을 하기 때문에 데이터베이스 메모리를 많이 사용하게 된다. 즉, 대량의 데이터를 정렬하게 되면 정렬로 인한 성능저하가 발생한다.
        - ORACLE 데이터베이스는 정렬을 위해서 메모리 내부에 할당된 SORT_AREA_SIZE를 사용한다. 만약 SORT_AREA_SIZE가 너무 작으면 성능저하가 발생한다.
        
        ```sql
        SELECT * FROM EMP
            ORDER BY ENAME, SAL DESC;
        ```

        - ENAME으로 오름차순 정렬하고 SAL로 내림차순 정렬한다.
        - 정렬을 회피하기 위해서 인덱스를 생성할 때 사용자가 원하는 형태로 오름차순 혹은 내림차순으로 생성해야 한다.

        ```sql
        SELECT /*+ INDEX_DESC(A) */ * 
        FROM EMP A;
        ```

        - 인덱스 힌트인 `/*+ INDEX_DESC(A) */`를 사용해서 EMP 테이블에 생성된 인덱스를 내림차순으로 읽게 지정할 수 있다.

    - Distinct와 Alias
        - Distinct문은 컬럼명 앞에 지정하여 중복된 데이터를 한 번만 조회하게 한다.
        - Alias(별칭)은 테이블명이나 컬럼명이 너무 길어서 간략하게 사용할 떄 사용한다.

    - `SELECT문 실행 순서`
        - FROM > WHERE > GROUP BY > HAVING > SELECT > ORDER BY 순으로 실행 된다.

5. WHERE문 사용
    - 연산자

        | 연산자 | 설명 |
        | --- | --- |
        | LIKE '비교 문자열' | 비교 문자열을 조회한다. '%'는 모든 겂을 의미한다. |
        | IN (list) | OR을 의미하며 list 값 중에 하나만 일치해도 조회된다. |
        | IS NULL | NULL 값을 조회한다. |
        | NOT BETWEEN A AND B | A와 B 사이에 해당되지 않는 값을 조회한다. |
        | IS NOT NULL | NULL 값이 아닌 것을 조회한다. |


    - Like문 사용
        - `_`는 한 개의 단일 문자를 의미한다.

            ```sql
            SELECT * FROM EMP
            WHERE ENAME LIKE 'test_';
            ```

            - 조회 결과 : test1, test2, test3 등

    - NULL값 조회
        - NULL 특징
            - 모르는 값을 의미한다.
            - 값의 부재를 의미한다.
            - NULL과 모든 비교는 알 수 없음을 반환한다.
            - NULL과 숫자 혹은 날짜를 더하면 NULL이 된다.
            - NULL 값은 비교 연산자로 비교할 수 없다. 만약 비교 연산자로 NULL을 비교하면 False가 나온다.

        - NULL 관련 함수

            | NULL 함수 | 설명 |
            | --- | ---|
            | NVL 함수 | - NULL이면 다른 값으로 바꾸는 함수이다. <br> - `NVL(MGR, 0)`은 MGR 칼럼이 NULL이면 0으로 바꾼다. |
            | NVL2함수 | - NVL함수와 DECODE를 하나로 만든 것이다. <br> - `NVL2(MGR, 1, 0)`은 MGR 컬럼이 NULL이 아니면 1을 NULL이면 0을 반환한다. |
            | NULLIF 함수 | - 두 개의 값이 같으면 NULL을, 같지 않으면 첫 번째 값을 반환한다. <br> - `NULLIF(exp1, exp2)`은 exp1과 exp2가 같으면 NULL을, 같지 않으면 exp1을 반환한다. |
            | COALESCE | `COALESCE(mgr, 1)`은 mgr이 NULL이 아니면 1을 반환한다. |

6. GROUP 연산
    1. GROUP BY 문
        - 테이블에서 소규모 행을 그릅화하여 합계, 평균, 최대 값, 최소 값 등을 구할 수 있다.
        - HAVING구에 조건문을 사용한다.
        - ORDER BY를 사용해서 정렬을 할 수 있다.

    2. HAVING 문 사용
        - GROUP BY에 조건을 사용하려면 HAVING을 사용해야 한다. 만약 WHERE절에 조건문을 사용하게 되면 GROUP BY 대상에서 제외된다.

            ```sql
            SELECT DEPTNO, SUM(SAL)
            FROM EMP
            GROUP BY DEPTNO
            HAVING SUM(SAL) > 10000;
            ```

    3. 집계함수
        - Count 함수
            - COUNT() 함수는 행 수를 계산하는 함수이다. 
            - COUNT(*)은 NULL 값을 포함한 모든 행 수를 계산한다.
            - COUNT(칼럼)으로 하면 NULL 값을 제외한 행 수를 계산한다.
            - **종류**

                | 집계함수 | 설명 |
                | --- | --- |
                | COUNT() | 행 수를 조회한다. |
                | SUM() | 합계를 계산한다. |
                | AVG() | 평균을 계산한다. |
                | MAX()와 MIN() | 최대값과 최소값을 계산한다. |
                | STDDEV() | 표준편차를 계산한다. |
                | VARIAN() | 분산을 계산한다. |

7. GROUP BY 예제

    - 부서별(DEPTNO), 관리자별(MGR) 급여 평균 계산
    
        ```sql
        SELECT DEPTNO, MGR, AVG(SAL)
        FROM EMP
        GROUP BY DEPTNO, MGR;
        ```

    - 직업(JOB)별 급여 합계 중에 급여 합계가 1000 이상인 직원

        ```sql
        SELECT JOB, SUM(SAL)
        FROM EMP
        GROUP BY JOB
        HAVING SUM(SAL) > 1000;
        ```

    - 사원번호 1000~1003번의 부서별 급여 합계

        ```sql
        SELECT DEPTNO, SUM(SAL)
        FROM EMP
        WHERE EMPNO BETWEEN 1000 AND 1003
        GROUP BY DEPTNO;
        ```

8. 명시적(Explicit) 변환과 암시적(Implicit) 형변환

    - 형변환은 두 개의 데이터의 데이터 타입이 일치하도록 변환하는 것이다.
    - 명시적 형변환은 형변환 함수를 사용해서 데이터 타입을 일치시키는 것으로 개발자가 SQL을 사용할 떄 형변환 함수를 사용해야 한다.
    - **형변환 함수**

        | 형변환 함수                      | 설명 |
        | ----------------------------- | --- |
        | TO_NUMBER(문자열)               | 문자열을 숫자로 변환한다. |
        | TO_CHAR(숫자 혹은 날짜, [FORMAT]) | 숫자 혹은 문자를 지정된 FORMAT의 문자로 변환한다. |
        | TO_DATE(문자열, FORMAT)         | 문자열을 지정된 FORMAT의 날짜형으로 변환한다. |
    
    - 암시적 형변환은 개발자가 하지 않은 경우 DBMS가 자동으로 형변환하는 것을 의미한다.

9. 내장형 함수(Build-In Function)
    > 모든 데이터베이스는 SQL에서 사용할 수 있는 내장형 함수를 가지고 있다.
    
    - 문자형 함수

        | 문자형 함수 | 설명 |
        | -------- | --- |
        | ASCII(문자) | 문자 혹은 숫자를 ASCII 코드 값으로 변환한다. |
        | CHAR(ASCII 코드 값) | ASCII 코드 값을 문자로 변환한다. |
        | SUBSTR(문자열, m, n) | 문자열에서 m번째 위치부터 n개를 자른다. |
        | CONCAT(문자열1, 문자열2) | - 문자열1번과 문자열2번을 결합한다. |
        | LOWER(문자열) | 영문자를 소문자로 변환한다. |
        | UPPER(문자열) | 영문자를 대문자로 변환한다. |
        | LENGTH or LEN (문자열) | 공백을 포함해서 문자열의 길이를 알려준다. |
        | LTRIM(문자열, 지정문자) <br> RTRIM(문자열, 지정문자) | - 왼쪽 or 오른쪽에     지정된 문자를 삭제한다. <br> - 지정된 문자를 생략하면 공백을 삭제한다. |
        | TRIM(문자열, 지정된 문자) | - 왼쪽 및 오른쪽에 지정된 문자를 삭제한다. <br> -     지정된 문자를 생략하면 공백을 삭제한다. |

    - **SYSDATE** : 오늘 날짜를 구하는 함수
    - **EXTRACT** : 해당 년/월/일 만 알고 싶을때 사용 함수

    ```SQL
    SELECT SYSDATE,
           EXTRACT(YEAR From SYSDATE) AS ExtractYear, 
           TO_CHAR(SYSDATE, 'YYYYMMDD') AS TOCHAR
           FROM DUAL;
    ```

    - 결과
        
        | SYSDATE | ExtractYear | TOCHAR |
        | ------- | ----------- | ------ |
        | 19/11/26 | 2019 | 20191126 |

    - **숫자형 함수**

        | 숫자형 함수 | 설명 |
        | -------- | --- |
        | ABS(숫자) | 절대값을 돌려준다. |
        | SIGN(숫자) | 양수, 음수, 0을 구별한다. |
        | MOD(숫자1, 숫자2) | 숫자1을 숫자2로 나누어 나머지를 계산한다. |
        | CEIL/CEILING(숫자) | 숫자보다 크거나 같은 최소의 정수를 돌려준다. |
        | FLOOR(숫자) | 숫자보다 작거나 같은 최대의 정수를 돌려준다. |
        | ROUND(숫자, m) | 소수점 m자리에서 반올림한다. |
        | TRUNC(숫자, m) | 소수점 m자리에서 절삭한다. |

10. DECODE와 CASE문

    - DECODE문으로 IF문을 구현할 수 있다. 특정 조건이 참이면 A, 거짓이면 B로 응답한다.

    ```SQL
    DECODE(EMPNO, 1000, 'TRUE', 'FALSE')
    ```

    - EMPNO = 1000 이면 TRUE, 아니면 FALSE 응답

    -CASE
        - IF~THEN ~ ELSE ~ END 구조

11. ROWNUM과 ROWID

    1. ROWNUM

        - ROWNUM은 SELECT문의 결과에 대해서 논리적인 일련번호를 부여한다.
        - ROWNUM은 조회되는 행 수를 제한할 때 많이 사용된다.
        - ROWNUM을 사용해서 한 개의 행을 가지고 올 수 있으나, 여러 개의 행을 가지고 올 때는     인라인 뷰(Inline view)를 사용해야 한다.

        ```SQL
        SELECT * FROM EMP
        WHERE ROWNUM <= 1
        ```

        | EMPNO | ENAME | JOB | SAL |
        | --- | --- | --- | --- |
        | 1000 | test1 | clerk | 800 |

        ```SQL
        SELECT *
        FROM (SELECT ROWNUM list, ENAME FROM EMP)
        WHERE list <= 5
        ```

        | LIST | ENAME |
        | --- | --- | 
        | 1 | test1 |
        | 2 | test2 |
        | 3 | test3 |
        | 4 | test4 |
        | 5 | test5 |

        - 위와 같이 5건의 행을 조회하기 위해서는 인라인 뷰를 사용하고 ROWNUM에 별칭을 사용해야  한다.

    2. ROWID

        - ORACLE 데이터베이스 내에서 데이터를 구분할 수 있는 유일한 값이다.
        - 데이터가 어떤 데이터 파일, 어떤 블록에 저장되어 있는지를 알 수 있다.
        - 구조

            | 구조 | 길이 | 설명 |
            | --- | --- | --- |
            | 오브젝트 번호 | 1~6 | 오브젝트 별로 유일한 값을 가지고 있으며, 해당 오브젝트가 속해 있는 값이다. |
            | 상대 파일번호 | 7~9 | 테이블스페이스에 속해 있는 데이터 파일에 대한 상대 파일번호이다. |
            | 블록번호 | 10~15 | 데이터 파일 내부에서 어느 블록에 데이터가 있는지 알려준다. |
            | 데이터번호 | 16~18 | 데이터 블록에 데이터가 저장되어 있는 순서를 의미한다. |

12. WITH 구문

    - 서브쿼리를 사용해서 임시 테이블이나 뷰처럼 사용할 수 잇는 구문이다.
    - 서브쿼리 블록에 별칭을 지정할 수 있다.
    - 옵티마이저는 SQL을 인라인 뷰나 임시 테이블로 판단한다.

    ```SQL
    WITH viewData AS
    (SELECT * FROM EMP
        UNION ALL
    SELECT * FROM EMP)
    # SubQuery를 사용해서 임시 테이블을 만들고 viewData라는 별명을 만든다.
    SELECT * FROM viewData WHERE EMPNO=1000
    # 임시 테이블을 이용해 데이터를 조회한다.
    ```

### DCL

1. GRANT
    - GRANT문은 데이터베이스 사용자에게 권한을 부여한다.
    - 데이터베이스 사용을 위해서는 권한이 있어서 연결, 입력, 수정, 삭제, 조회를 할 수 있다.
        
        ```SQL
        GRANT privileges ON object TO user
        # privileges는 구너한을 의미하며, object는 테이블명이다.
        # user는 Oracle 데이터베이스 사용자를 지정하면 된다.
        ```

    - Privileges(권한)

        | 권한 | 설명 |
        | --- | --- |
        | SELECT | 지정된 테이블에 대해서 SELECT 권한을 부여한다. |
        | INSERT | 지정된 테이블에 대해서 INSERT 권한을 부여한다. |
        | UPDATE | 지정된 테이블에 대해서 UPDATE 권한을 부여한다. |
        | DELETE | 지정된 테이블에 대해서 DELETE 권한을 부여한다. |
        | REFERENCES | 지정된 테이블에 참조하는 제약조건을 생성하는 권한을 부여한다. |
        | ALTER | 지정된 테이블에 대해서 수정할 수 있는 권한을 부여한다. |
        | INDEX | 지정된 테이블에 대해서 인덱스를 생성할 수 있는 권한을 부여한다. |
        | ALL | 테이블에 대한 모든 권한을 부여한다. |

        ```SQL
        GRANT SELECT, INSERT, UPDATE, DELETE
            ON EMP
            TO LIMBEST
        ```

    - WITH GRANT OPTION

        | GRANT 옵션 | 설명 |
        | --------- | --- |
        | WITH GRANT OPTION | - 특정 사용자에게 권한을 부여할 수 있는 권한을 부여한다. <br> - 권한을 A 사용자가 B에 부여하고 B가 다시 C를 부여한 후에 권한을 취소(Revoke)하면 모든 권한이 회수된다. |
        | WITH ADMIN OPTION | - 테이블에 대한 모든 권한을 부여한다. <br> - 권한을 A 사용자가 B에 부여하고 B가 다시 C를 부여한 후에 권한을 취소(Revoke)하면 B 사용자 권한만 취소된다. | 

        ```SQL
        GRANT SELECT, INSERT, UPDATE, DELETE
             ON EMP
             TO LIMBSET WITH GRANT OPTION
        #권한을 부여할 수 있는 권한을 부여한다.
        ```

    - REVOKE
        - 데이터베이스 사용자에게 부여된 권한을 회수한다.

        ```SQL
        REVOKE privileges ON object From user
        ```

### TCL

1. COMMIT
    - INSERT, UPDATE, DELETE문으로 변경한 데이터를 데이터베이스에 반영한다.
    - 변경 전 이전 데이터는 잃어버린다.
    - COMMIT이 완료되면 모든 데이터베이스 사용자는 변경된 데이터를 볼 수 있다.
    - 데이터베이스 변경으로 인한 LOCK이 UNLOCK(해제)된다.
    - COMMIT이 완료되면 다른 모든 데이터베이스 사용자는 변경된 데이터를 조작할 수 있다.
    - COMMIT을 실행하면 하나의 트랜잭션 과정을 종료한다.
    - ORACLE 데이터베이스는 암시적 트랜잭션 관리를 한다. 

2. ROLLBACK
    - ROLLBACK을 실행하면 데이터에 대한 변경 사용을 모두 취소하고 트랜잭션을 종료한다.
    - INSERT, UPDATE, DELETE문의 작업을 모두 취소한다. 
    - ROLLBACK을 실행하면 LOCK이 해제되고 다른 사용자도 데이터베이스 행을 조작할 수 있다.

3. SAVEPOINT(저장점)
    - SAVEPOINT는 트랜잭션을 작게 분할하여 관리하는 것으로 SAVEPOINT를 사용하면 지정된 위치까지만 트랜잭션을 ROLLBACK 할 수 있다.
    - SAVEPOINT의 지정은 SAVEPOINT <SAVEPOINT명>을 실행한다.
    - 저장된 SAVEPOINT까지만 데이터 변경을 취소하고 싶은 경우는 `ROLLBACK TO <SAVEPOINT명>`을 실행한다.
    - 만약, `ROLLBACK`을 실행하면 SAVEPOINT와 관계없이 변경된 모든 데이터를 취소한다.