# SQL 데이터베이스 시험 대비 정리

## 📚 목차

1. [[#7장 서브쿼리]] (#7장-서브쿼리)
2. [[#8장 기타쿼리]] (#8장-기타쿼리)
3. [[#9장 DML과 트랜잭션]](#9장-dml과-트랜잭션)
4. [[#10장 테이블 관리]](#10장-테이블-관리)
5. [[#11장 제약조건 관리]](#11장-제약조건-관리)
6. [[#12장 인덱스]](#12장-인덱스)
7. [[#13장 뷰와 시퀀스]](#13장-뷰와-시퀀스)

---

## 7장 서브쿼리

### 📖 서브쿼리 개념

#### 정의
- **서브쿼리(내부 쿼리)**: SQL 문 내부에 포함되는 괄호 안의 쿼리문
- **메인쿼리(외부 쿼리)**: 결과를 반환할 때 사용할 최종 쿼리문
- 조건 값을 알 수 없는 SQL 문을 작성하는데 매우 유용

#### 서브쿼리가 사용되는 곳
- WHERE 절
- HAVING 절
- FROM 절
- ORDER BY 절

#### 기본 구문
```sql
SELECT select_list
FROM table
WHERE 표현식 연산자 (SELECT select_list
                    FROM table);
```

### 📊 서브쿼리 종류

#### 1. 단일 행 서브쿼리
- **특징**: 서브쿼리에서 하나의 행만 반환
- **연산자**: `=`, `>`, `>=`, `<`, `<=`, `<>`

```sql
-- 예제: 오상우 사원보다 급여가 많은 사원
SELECT emp_name, position, salary
FROM y_emp
WHERE salary > (SELECT salary 
                FROM y_emp
                WHERE emp_name = '오상우');
```

#### 2. 다중 행 서브쿼리
- **특징**: 서브쿼리에서 여러 행을 반환
- **연산자**: `IN`, `ANY`, `ALL`, `EXISTS`

| 연산자 | 의미 |
|--------|------|
| IN | 목록의 임의의 값과 동등 비교 |
| ANY | 값을 서브쿼리 결과 중 하나와라도 비교 |
| ALL | 값을 서브쿼리 결과 모든 값과 비교 |
| EXISTS | 서브쿼리에서 결과가 하나라도 존재하면 참 |

```sql
-- 예제: 200번 이하 부서의 최고급여와 동일한 급여를 받는 사원
SELECT emp_name, salary, dept_id
FROM y_emp
WHERE salary IN (SELECT MAX(salary)
                 FROM y_emp
                 WHERE dept_id <= 200
                 GROUP BY dept_id);
```

#### 3. 다중 열 서브쿼리
- **쌍 비교 방식(Pairwise)**: 열들을 괄호로 묶어서 동시에 비교
- **비쌍 비교 방식(Unpairwise)**: 각 열을 따로 비교 후 AND 연산

```sql
-- 쌍 비교 예제
SELECT emp_id, emp_name, position, dept_id
FROM y_emp
WHERE (position, dept_id) IN (SELECT position, dept_id 
                              FROM y_emp
                              WHERE emp_name LIKE '차%')
AND emp_name NOT LIKE '차%';
```

#### 4. 상호관련 서브쿼리 (Correlated Subquery)
- **특징**: 메인쿼리와 서브쿼리가 서로 연관되어 실행
- **성능**: 메인쿼리의 모든 행에 대해 실행되므로 성능 주의

```sql
-- 소속부서 평균급여보다 많이 받는 사원
SELECT emp_name, salary, dept_id
FROM y_emp outer
WHERE salary > (SELECT AVG(salary) 
                FROM y_emp
                WHERE dept_id = outer.dept_id);
```

### 🔍 기타 서브쿼리 활용

#### 인라인 뷰 (FROM 절 서브쿼리)
```sql
SELECT a.emp_name, a.salary, a.dept_id, b.salavg
FROM y_emp a
JOIN (SELECT dept_id, AVG(salary) salavg 
      FROM y_emp
      GROUP BY dept_id) b
ON (a.dept_id = b.dept_id);
```

#### Top-N 분석
```sql
SELECT ROWNUM as RANK, emp_id, emp_name, salary
FROM (SELECT emp_id, emp_name, salary 
      FROM y_emp
      WHERE salary IS NOT NULL
      ORDER BY salary DESC)
WHERE ROWNUM <= 5;
```

#### WITH 절 활용
```sql
WITH dept_total_sal AS (
  SELECT d.dept_name, SUM(e.salary) AS dept_total
  FROM y_emp e JOIN y_dept d
  ON (e.dept_id = d.dept_id)
  GROUP BY d.dept_name
)
SELECT * FROM dept_total_sal
WHERE dept_total > (SELECT AVG(dept_total) 
                    FROM dept_total_sal);
```

---

## 8장 기타쿼리

### 🔗 집합 연산자 (SET Operators)

#### 기본 구문
```sql
SELECT statement 1
[UNION | UNION ALL | INTERSECT | MINUS]
SELECT statement 2
[ORDER BY [column_name | 별칭 | column_위치값]];
```

#### 연산자 종류

| 연산자 | 설명 | 중복 처리 |
|--------|------|-----------|
| UNION | 합집합 | 중복 제거 |
| UNION ALL | 합집합 | 중복 포함 |
| INTERSECT | 교집합 | 중복 제거 |
| MINUS | 차집합 | 중복 제거 |

```sql
-- UNION 예제
SELECT emp_id FROM y_emp
UNION
SELECT emp_id FROM y_award;

-- INTERSECT 예제 (수상시기와 현재 부서가 동일한 사원)
SELECT emp_id, emp_name, dept_id
FROM y_emp
INTERSECT
SELECT emp_id, awardee, dept_id
FROM y_award;
```

### 📈 계층 쿼리 (Hierarchical Query)

#### 기본 구문
```sql
SELECT [LEVEL], column, 표현식...
FROM table_name
[WHERE 조건(s)]
[START WITH 조건(s)]
[CONNECT BY PRIOR 조건(s)];
```

#### 주요 절 설명
- **START WITH**: 계층 쿼리의 시작점 지정 (루트)
- **CONNECT BY PRIOR**: 부모-자식 관계 및 검색 방향 결정
  - `PRIOR 부모 = 자식`: Top-Down 검색
  - `부모 = PRIOR 자식`: Bottom-Up 검색

```sql
-- Top-Down 조직도 검색
SELECT emp_id, emp_name, position, mgr_id
FROM y_emp
START WITH emp_id = 1001
CONNECT BY PRIOR emp_id = mgr_id;

-- 들여쓰기가 있는 계층형 결과
SELECT LPAD(emp_id, LENGTH(emp_id)+(LEVEL*2)-2,'-') AS CHART
FROM y_emp
START WITH emp_id = 1001
CONNECT BY PRIOR emp_id = mgr_id;
```

#### 계층 쿼리 관련 함수
- **LEVEL**: 계층에서의 레벨 표시 (의사열)
- **SYS_CONNECT_BY_PATH**: 현재 행까지의 경로 표시

```sql
-- 경로 표시 예제
SELECT emp_id, emp_name, 
       SYS_CONNECT_BY_PATH(emp_id,'/') path
FROM y_emp
START WITH emp_id = 1001
CONNECT BY PRIOR emp_id = mgr_id;
```

---

## 9장 DML과 트랜잭션

### 📝 DML (Data Manipulation Language)

#### DML 종류
- **INSERT**: 테이블에 새 행 추가
- **UPDATE**: 테이블의 기존 행 수정
- **DELETE**: 테이블에서 기존 행 삭제
- **MERGE**: INSERT와 UPDATE를 동시에 처리

### ➕ INSERT 명령

#### 단일행 INSERT
```sql
INSERT INTO table [(column, column...)]
VALUES (value [, value...]);

-- 예제
INSERT INTO y_dept(dept_id, dept_name, mgr_id, loc_id)
VALUES (600, '생산관리', 1038, 6);
```

#### 다중행 INSERT (서브쿼리 사용)
```sql
INSERT INTO table [column (, column)]
subquery;

-- 예제
INSERT INTO emp300
SELECT emp_id, emp_name, salary, dept_id
FROM y_emp
WHERE dept_id = 300;
```

#### NULL 값 처리
- **암시적**: INSERT INTO 절의 열 목록에서 해당 열을 생략
- **명시적**: VALUES 절에서 NULL을 명시적으로 지정

### 🔄 UPDATE 명령

```sql
UPDATE table
SET column = value [, column = value, ...]
[WHERE 조건];

-- 예제
UPDATE emp300
SET dept_id = 400
WHERE emp_id = 1037;

-- 서브쿼리 사용
UPDATE emp300
SET salary = (SELECT salary FROM y_emp WHERE emp_id = 1038)
WHERE emp_id = 1087;
```

### ❌ DELETE 명령

```sql
DELETE [FROM] table
[WHERE 조건];

-- 예제
DELETE FROM emp300
WHERE emp_id = 1019;

-- 서브쿼리 사용
DELETE FROM y_emp
WHERE dept_id = (SELECT dept_id
                 FROM y_dept
                 WHERE dept_name LIKE '%회계%');
```

### 🔀 MERGE 명령

```sql
MERGE INTO table_name table_별칭
USING (table|view|sub_query) 별칭
ON (join_조건)
WHEN MATCHED THEN
    UPDATE SET col1 = col_val1, col2 = col2_val
    [DELETE WHERE (조건)]
WHEN NOT MATCHED THEN
    INSERT (column_name,...) VALUES (column_value,...);
```

### 🔄 트랜잭션 관리

#### 트랜잭션의 특징
- **원자성(Atomicity)**: 모든 연산이 완전히 수행되거나 전혀 수행되지 않음
- **일관성(Consistency)**: 트랜잭션 실행 전후 데이터베이스가 일관된 상태 유지
- **고립성(Isolation)**: 동시 실행되는 트랜잭션들이 서로 영향을 주지 않음
- **지속성(Durability)**: 트랜잭션이 성공적으로 완료되면 결과가 영구 저장

#### TCL (Transaction Control Language)

| 명령어 | 기능 |
|--------|------|
| COMMIT | 현재 트랜잭션의 모든 변경사항을 영구 저장 |
| ROLLBACK | 현재 트랜잭션의 모든 변경사항을 취소 |
| SAVEPOINT | 트랜잭션 내 특정 지점에 이름 지정 |

```sql
-- SAVEPOINT 사용 예제
UPDATE emp300 SET salary = 600 WHERE emp_id = 1037;
SAVEPOINT s1;
UPDATE emp300 SET salary = 670 WHERE emp_id = 1081;
SAVEPOINT s2;
DELETE FROM emp300 WHERE emp_id = 1019;

ROLLBACK TO s2; -- s2 지점으로 부분 롤백
ROLLBACK;       -- 전체 롤백
```

#### 트랜잭션 시작과 종료
- **시작**: 첫 번째 DML 문 실행 시
- **종료**:
  - COMMIT 또는 ROLLBACK 실행
  - DDL 문 또는 DCL 문 실행 (자동 COMMIT)
  - 세션 종료

---

## 10장 테이블 관리

### 🗃️ 데이터베이스 객체

#### 기본 객체 종류
- **테이블(Table)**: 데이터를 저장하는 기본 객체
- **뷰(View)**: 하나 이상 테이블 데이터의 부분 집합
- **시퀀스(Sequence)**: 일련 번호 생성기
- **인덱스(Index)**: 쿼리 성능 향상을 위한 객체
- **동의어(Synonym)**: 객체에 대한 다른 이름

### 📊 데이터 딕셔너리

#### 데이터 딕셔너리 뷰 접두어

| 접두어 | 범위 |
|--------|------|
| USER_ | 현재 사용자가 소유하는 객체 정보 |
| ALL_ | 현재 사용자가 액세스할 수 있는 모든 객체 정보 |
| DBA_ | 데이터베이스의 모든 객체 정보 (DBA 권한 필요) |
| V$ | 데이터베이스 서버 성능, 메모리, LOCK 등 동적 성능 정보 |

### 🏗️ 테이블 생성

#### 직접 정의 방식
```sql
CREATE TABLE [schema.]table_이름
(column1 datatype [DEFAULT 표현식][제약조건],
 column2 datatype [DEFAULT 표현식][제약조건],
 ...);

-- 예제
CREATE TABLE customers
(cust_id NUMBER(6),
 cust_name VARCHAR2(40),
 phone_no VARCHAR2(15),
 email VARCHAR2(30),
 birth_date DATE,
 join_date DATE DEFAULT SYSDATE,
 cust_level NUMBER(1) DEFAULT 9);
```

#### 기존 테이블 복사 방식
```sql
CREATE TABLE table
[(column, column...)] AS subquery;

-- 예제
CREATE TABLE dept200
AS SELECT emp_id, emp_name, salary*12 ANNSAL, hiredate
   FROM y_emp
   WHERE dept_id = 200;
```

### 🔧 데이터 타입

#### 숫자 데이터
- **NUMBER[(p, s)]**: p는 전체 자릿수, s는 소수점 이하 자릿수

#### 문자 데이터
- **CHAR[(size)]**: 고정길이 문자 데이터 (최대 2000바이트)
- **VARCHAR2[(size)]**: 가변길이 문자 데이터 (최대 4000바이트)

#### 날짜 데이터
- **DATE**: 초 단위까지의 날짜와 시간 (7바이트)
- **TIMESTAMP**: 더 정밀한 시간 정보 포함
- **TIMESTAMP WITH TIME ZONE**: 시간대 정보 포함
- **TIMESTAMP WITH LOCAL TIME ZONE**: 사용자 지역 시간대에 맞게 변환

#### INTERVAL 데이터 타입
- **INTERVAL YEAR TO MONTH**: 연도와 월 기간 저장
- **INTERVAL DAY TO SECOND**: 일, 시, 분, 초 기간 저장

```sql
-- INTERVAL 사용 예제
INSERT INTO time_test2
VALUES(1, INTERVAL '10-6' YEAR TO MONTH, 
       INTERVAL '7 12:00:00' DAY TO SECOND);
```

### 🔨 테이블 수정 (ALTER TABLE)

#### 새 열 추가
```sql
ALTER TABLE table
ADD (column datatype [DEFAULT 표현식]);

-- 예제
ALTER TABLE dept200
ADD (position VARCHAR2(10));
```

#### 기존 열 수정
```sql
ALTER TABLE table
MODIFY (column datatype [DEFAULT 표현식]);

-- 예제
ALTER TABLE dept200 
MODIFY hiredate TIMESTAMP;
```

#### 열 삭제
```sql
ALTER TABLE table
DROP COLUMN column_name;

-- SET UNUSED 활용 (성능 향상)
ALTER TABLE dept200 SET UNUSED (annsal, position);
ALTER TABLE dept200 DROP UNUSED COLUMNS;
```

#### 테이블 READ ONLY 설정
```sql
ALTER TABLE table_name [READ ONLY | READ WRITE];
```

### 🗑️ 테이블 삭제

#### DROP TABLE
```sql
DROP TABLE table_name [PURGE];

-- 예제
DROP TABLE customers;          -- 휴지통으로 이동
DROP TABLE customers PURGE;    -- 영구 삭제

-- 휴지통에서 복원
FLASHBACK TABLE customers TO BEFORE DROP;
```

#### TRUNCATE TABLE
```sql
TRUNCATE TABLE table_name;
```

**TRUNCATE vs DELETE vs DROP 비교**

| 명령어 | 데이터 | 구조 | 조건절 | 롤백 | 성능 |
|--------|--------|------|--------|------|------|
| DELETE | 부분/전체 삭제 | 유지 | 가능 | 가능 | 느림 |
| TRUNCATE | 전체 삭제 | 유지 | 불가능 | 불가능 | 빠름 |
| DROP | 전체 삭제 | 삭제 | 불가능 | 불가능 | 빠름 |

---

## 11장 제약조건 관리

### 🔒 제약조건 개념

제약조건(Constraints)은 테이블에 잘못된 데이터가 입력되는 것을 방지하기 위해 테이블의 데이터에 적용되는 규칙입니다.

### 📋 제약조건 종류

#### 1. NOT NULL 제약조건
- 해당 열에 NULL 값 생성 방지
- 열 레벨에서만 정의 가능

#### 2. UNIQUE 제약조건 (고유 키)
- 열 또는 조합 열의 값이 고유하게 유지
- NULL 값은 허용 (NULL은 고유하지 않음)
- UNIQUE 인덱스 자동 생성

#### 3. PRIMARY KEY 제약조건 (기본 키)
- 테이블의 각 행을 고유하게 식별
- NOT NULL + UNIQUE 속성
- 테이블당 하나만 생성 가능
- UNIQUE 인덱스 자동 생성

#### 4. FOREIGN KEY 제약조건 (외래 키)
- 다른 테이블의 기본 키 또는 고유 키 참조
- 참조 무결성 보장
- 부모 테이블과 자식 테이블 관계 설정

#### 5. CHECK 제약조건
- 각 행이 만족해야 하는 조건 정의
- WHERE 절과 동일한 구조 사용

### 🛠️ 제약조건 정의

#### 테이블 생성 시 정의
```sql
CREATE TABLE customers
(cust_id NUMBER(6) CONSTRAINT cust_custid_pk PRIMARY KEY,
 cust_name VARCHAR2(40) CONSTRAINT cust_name_nn NOT NULL,
 phone_no VARCHAR2(15) NOT NULL,
 email VARCHAR2(30),
 birth_date DATE,
 join_date DATE DEFAULT SYSDATE,
 cust_level NUMBER(1) DEFAULT 9,
 CONSTRAINT cust_level_fk FOREIGN KEY (cust_level)
 REFERENCES cust_grade(cust_level));
```

#### 기존 테이블에 제약조건 추가
```sql
ALTER TABLE table_name
ADD [CONSTRAINT constraint_name] type (column);

-- 예제
ALTER TABLE y_emp
ADD CONSTRAINT emp_empid_pk PRIMARY KEY(emp_id);

ALTER TABLE y_emp
ADD CONSTRAINT emp_deptid_fk FOREIGN KEY(dept_id)
REFERENCES y_dept(dept_id) ON DELETE SET NULL;
```

### 🔧 FOREIGN KEY 옵션

| 옵션 | 설명 |
|------|------|
| ON DELETE CASCADE | 부모 행 삭제 시 자식 행도 삭제 |
| ON DELETE SET NULL | 부모 행 삭제 시 외래 키 값을 NULL로 변경 |

```sql
-- SELF REFERENCE FOREIGN KEY
ALTER TABLE y_emp
ADD CONSTRAINT emp_mgrid_fk FOREIGN KEY(mgr_id)
REFERENCES y_emp(emp_id);
```

### 🎛️ 제약조건 관리

#### 제약조건 활성화/비활성화
```sql
-- 비활성화
ALTER TABLE table
DISABLE CONSTRAINT constraint_name [CASCADE];

-- 활성화
ALTER TABLE table
ENABLE CONSTRAINT constraint_name;
```

#### 제약조건 삭제
```sql
ALTER TABLE table_name
DROP PRIMARY KEY | UNIQUE (column) | CONSTRAINT constraint_name [CASCADE];
```

### 🔍 제약조건 정보 조회

#### 주요 데이터 딕셔너리
- **USER_CONSTRAINTS**: 제약조건 정보
- **USER_CONS_COLUMNS**: 제약조건과 연관된 열 정보

```sql
-- 제약조건 조회
SELECT constraint_name, constraint_type, status
FROM user_constraints
WHERE table_name = 'Y_EMP';

-- 제약조건과 열 정보 조회
SELECT c.constraint_name, cc.column_name, c.constraint_type
FROM user_constraints c JOIN user_cons_columns cc
ON (c.constraint_name = cc.constraint_name)
WHERE c.table_name = 'Y_EMP';
```

---

## 12장 인덱스

### 🚀 인덱스 개념

#### 정의
인덱스는 데이터베이스 시스템의 성능 최적화를 위해 테이블과 별도로 저장되는 데이터 구조로, 검색 속도를 향상시키는 스키마 객체입니다.

#### 인덱스의 원리
- 책의 목차와 같은 개념
- 포인터를 사용하여 테이블의 임의 행을 빠르게 검색
- 테이블과 독립적으로 존재

### 🎯 인덱스 사용 방식

#### 1. Index Scan
- 인덱스를 사용하여 데이터 검색
- 빠른 검색 속도

#### 2. Full Table Scan
- 인덱스가 없을 때 전체 테이블 검색
- 느린 검색 속도

### 🏗️ 인덱스 생성

#### 자동 생성
- PRIMARY KEY 제약조건 시 고유 인덱스 자동 생성
- UNIQUE 제약조건 시 고유 인덱스 자동 생성

```sql
-- 제약조건으로 인한 자동 인덱스 생성 확인
SELECT table_name, index_name, uniqueness 
FROM user_indexes
WHERE table_name IN ('Y_EMP','Y_DEPT');
```

#### 수동 생성
```sql
CREATE [UNIQUE] INDEX index_name
ON table_name (column_name [, column_name]...);

-- 예제
CREATE INDEX emp_deptid_idx ON y_emp(dept_id);

-- 결합 인덱스
CREATE INDEX emp_empid_ename_idx ON y_emp(emp_id, emp_name);
```

### 📊 인덱스 유형

#### 1. 고유 인덱스 (UNIQUE INDEX)
- 중복 값 허용하지 않음

#### 2. 비고유 인덱스 (NON-UNIQUE INDEX)
- 중복 값 허용

#### 3. 결합 인덱스 (COMPOSITE INDEX)
- 여러 열을 조합하여 생성

#### 4. 함수 기반 인덱스 (FUNCTION BASED INDEX)
- 함수나 표현식을 인덱스로 생성

```sql
-- 함수 기반 인덱스 생성
CREATE INDEX emp_sal_fidx ON y_emp(salary*12*0.9);

-- 사용 예제
SELECT emp_id, emp_name, position 
FROM y_emp
WHERE salary*12*0.9 > 10000;
```

### 🗑️ 인덱스 삭제

#### 수동 삭제
```sql
DROP INDEX index_name;
```

#### 자동 삭제
- 제약조건 삭제 시
- 테이블 삭제 시
- 인덱스된 열 삭제 시

### 📈 인덱스 사용 권장사항

#### 인덱스가 권장되는 경우
- 열에 광범위한 값이 포함된 경우
- 열에 NULL 값이 많이 포함된 경우
- WHERE 절이나 조인 조건에서 자주 사용되는 경우
- 큰 테이블에서 검색되는 행이 2%-4% 미만인 경우

#### 인덱스가 권장되지 않는 경우
- 테이블이 작은 경우
- 열이 쿼리 조건으로 자주 사용되지 않는 경우
- 대부분의 쿼리가 테이블 행의 2%-4% 이상을 검색하는 경우
- 테이블이 자주 갱신되는 경우

### 🔍 인덱스 정보 조회

```sql
-- 인덱스 정보 조회
SELECT ic.index_name, ic.column_name, uniqueness
FROM user_indexes ix, user_ind_columns ic
WHERE ic.index_name = ix.index_name 
AND ic.table_name = 'Y_EMP';
```

---

## 13장 뷰와 시퀀스

### 👁️ 뷰 (View)

#### 뷰의 개념
- 논리적으로 하나 이상의 테이블 데이터의 부분 집합
- 자체적으로 데이터를 포함하지 않음
- 데이터 딕셔너리에 SELECT 문으로 저장
- 보안성과 편의성 제공

#### 뷰의 종류

| 구분 | 단순 뷰 | 복합 뷰 |
|------|---------|---------|
| 테이블 수 | 단일 테이블 | 여러 테이블 |
| 함수/그룹 연산 | 포함하지 않음 | 포함 |
| 조인 | 포함하지 않음 | 포함 |
| DML 작업 | 대부분 가능 | 제한적 |

### 🏗️ 뷰 생성

#### 기본 구문
```sql
CREATE [OR REPLACE] [FORCE|NOFORCE] VIEW view_name
AS subquery
[WITH CHECK OPTION [CONSTRAINT constraint_name]]
[WITH READ ONLY [CONSTRAINT constraint_name]];
```

#### 뷰 생성 예제
```sql
-- 단순 뷰 생성
CREATE OR REPLACE VIEW dept300_vu
AS SELECT emp_id, emp_name, position, mgr_id, dept_id
   FROM y_emp
   WHERE dept_id = 300;

-- 별칭을 포함한 뷰
CREATE OR REPLACE VIEW sal_vu200
AS SELECT emp_id id, emp_name name, salary*12 ann_sal
   FROM y_emp
   WHERE dept_id = 200;

-- 복합 뷰 생성 (조인 포함)
CREATE VIEW dname_tot_vu(name, avgsal, sumsal)
AS SELECT d.dept_name, AVG(e.salary), SUM(e.salary)
   FROM y_emp e JOIN y_dept d
   ON (e.dept_id = d.dept_id)
   GROUP BY d.dept_name;
```

### 🔒 뷰 제약 옵션

#### WITH CHECK OPTION
- INSERT/UPDATE 시 뷰의 WHERE 절 조건 검사

```sql
CREATE OR REPLACE VIEW dept300_vu
AS SELECT emp_id, emp_name, position, mgr_id, dept_id
   FROM y_emp
   WHERE dept_id = 300
WITH CHECK OPTION;
```

#### WITH READ ONLY
- DML 작업 금지

```sql
CREATE OR REPLACE VIEW dept300_vu
AS SELECT emp_id, emp_name, position, mgr_id, dept_id
   FROM y_emp
   WHERE dept_id = 300
WITH READ ONLY;
```

### 🔧 뷰를 통한 DML 제한사항

#### DELETE 불가능한 경우
- 그룹 함수, GROUP BY 절, DISTINCT, ROWNUM 포함

#### UPDATE 불가능한 경우
- 그룹 함수, GROUP BY 절, DISTINCT, ROWNUM, 표현식 열 포함

#### INSERT 불가능한 경우
- 그룹 함수, GROUP BY 절, DISTINCT, ROWNUM, 표현식 열 포함
- 기본 테이블의 NOT NULL 열이 뷰에 포함되지 않은 경우

### 🗑️ 뷰 삭제

```sql
DROP VIEW view_name;
```

### 🔢 시퀀스 (Sequence)

#### 시퀀스 개념
- 고유한 정수를 생성하는 데이터베이스 객체
- 기본키 값 생성에 주로 사용
- 테이블과 별도로 저장
- 여러 테이블에서 동일한 시퀀스 사용 가능

#### 시퀀스 생성
```sql
CREATE SEQUENCE sequence_name
[INCREMENT BY n]
[START WITH n]
[{MAXVALUE n | NOMAXVALUE}]
[{MINVALUE n | NOMINVALUE}]
[{CYCLE | NOCYCLE}]
[{CACHE n | NOCACHE}];

-- 예제
CREATE SEQUENCE dept_deptid_seq
INCREMENT BY 100
START WITH 1000
MAXVALUE 9900
NOCACHE
NOCYCLE;
```

#### 시퀀스 사용
```sql
-- NEXTVAL: 다음 시퀀스 값 생성
INSERT INTO y_dept(dept_id, dept_name)
VALUES(dept_deptid_seq.NEXTVAL, 'AS');

-- CURRVAL: 현재 시퀀스 값 참조
SELECT dept_deptid_seq.CURRVAL FROM dual;
```

#### 시퀀스 사용 가능한 곳
- 서브쿼리에 속하지 않은 SELECT 문의 SELECT 목록
- INSERT 문의 VALUES 절
- UPDATE 문의 SET 절

#### 시퀀스 사용 불가능한 곳
- 뷰를 정의하는 SELECT 절
- DISTINCT 키워드가 있는 SELECT 문
- GROUP BY, HAVING, ORDER BY 절이 있는 SELECT 문
- 서브쿼리
- DEFAULT 값

### 🔧 시퀀스 관리

#### 시퀀스 수정
```sql
ALTER SEQUENCE sequence_name
[INCREMENT BY n]
[{MAXVALUE n | NOMAXVALUE}]
[{MINVALUE n | NOMINVALUE}]
[{CYCLE | NOCYCLE}]
[{CACHE n | NOCACHE}];
```

#### 시퀀스 삭제
```sql
DROP SEQUENCE sequence_name;
```

#### 시퀀스 정보 조회
```sql
SELECT sequence_name, increment_by, last_number
FROM user_sequences
WHERE sequence_name = 'DEPT_DEPTID_SEQ';
```

---

## 📝 핵심 정리

### 🎯 시험 출제 포인트

1. **서브쿼리**
   - 단일행 vs 다중행 연산자 구분
   - 상호관련 서브쿼리와 EXISTS 사용
   - 인라인 뷰와 Top-N 분석

2. **집합 연산자**
   - UNION vs UNION ALL 차이점
   - INTERSECT와 MINUS 활용

3. **DML과 트랜잭션**
   - MERGE 문 활용
   - SAVEPOINT를 이용한 부분 롤백
   - 트랜잭션 특성 (ACID)

4. **제약조건**
   - 각 제약조건의 특징과 차이점
   - FOREIGN KEY 옵션 (CASCADE, SET NULL)
   - 제약조건 활성화/비활성화

5. **인덱스**
   - 인덱스 생성 시기와 종류
   - 함수 기반 인덱스 활용
   - 인덱스 사용 권장/비권장 상황

6. **뷰와 시퀀스**
   - 뷰를 통한 DML 제한사항
   - 시퀀스 NEXTVAL/CURRVAL 사용법
   - WITH CHECK OPTION vs WITH READ ONLY

### 💡 학습 팁

1. **실습 위주 학습**: 각 개념을 직접 SQL로 작성해보기
2. **오류 상황 이해**: 각 명령어에서 발생할 수 있는 오류와 해결방법 숙지
3. **성능 고려사항**: 인덱스, 서브쿼리 등의 성능 영향 이해
4. **데이터 딕셔너리 활용**: USER_로 시작하는 뷰들을 통한 메타데이터 조회 연습