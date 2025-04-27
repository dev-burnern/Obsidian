## **1. 기본 SELECT 및 정렬**

**문제**: 사원 테이블(Y_EMP)에서 모든 사원의 이름(emp_name), 직급(position), 입사일(hiredate)을 입사일 순으로 오름차순 정렬하여 출력하시오.

```sql
SELECT emp_name, position, hiredate FROM y_emp ORDER BY hiredate;
```
**🔍 설명**:
- `SELECT`: 출력할 열 지정.
- `ORDER BY`: 결과를 `hiredate` 기준으로 오름차순 정렬 (기본값이 ASC이므로 생략 가능).
## **2. COUNT와 DISTINCT**

**문제**: 부서 테이블(Y_DEPT)에서 고유한 지역 번호(loc_id)의 개수를 구하시오.
```sql
SELECT COUNT(DISTINCT loc_id) AS unique_locations FROM y_dept;
```
**🔍 설명**:
- `COUNT(DISTINCT)`: 중복 제거 후 행 수 계산.
- `AS`: 결과 열에 별칭 부여.

## **3. INNER JOIN**
**문제**: 사원 테이블(Y_EMP)과 부서 테이블(Y_DEPT)을 조인하여 사원 이름(emp_name)과 소속 부서 이름(dept_name)을 출력하시오.
```sql
SELECT e.emp_name, d.dept_name FROM y_emp e JOIN y_dept d ON e.dept_id = d.dept_id;
```
**🔍 설명**:
- `JOIN ... ON`: 두 테이블을 `dept_id`로 결합.
- 테이블 별칭(`e`, `d`) 사용으로 코드 간결화.

## **4. LIKE 연산자**
**문제**: 이름이 '김'으로 시작하는 사원의 사번(emp_id)과 이름(emp_name)을 검색하시오.
```sql
SELECT emp_id, emp_name FROM y_emp WHERE emp_name LIKE '김%';
```
**🔍 설명**:
- `LIKE '김%'`: '김'으로 시작하는 모든 문자열 검색.
- `%`: 0개 이상의 문자 대체.
## **5. NULL 처리 (NVL)**
**문제**: 커미션(comm)이 NULL인 사원의 경우 0으로 표시하고, 사원 이름과 커미션을 출력하시오.
```sql
SELECT emp_name, NVL(comm, 0) AS commission FROM y_emp;
```
**🔍 설명**:
- `NVL(comm, 0)`: `comm`이 NULL이면 0 반환.
- Oracle 전용 함수. 표준 SQL에서는 `COALESCE(comm, 0)` 사용.

## **6. 서브쿼리 (WHERE 절)**
**문제**: 급여가 평균 급여보다 많은 사원의 이름과 급여를 출력하시오.
```sql
SELECT emp_name, salary FROM y_emp WHERE salary > (SELECT AVG(salary) FROM y_emp);
```
**🔍 설명**:
- 서브쿼리: 평균 급여 계산 후 메인 쿼리에서 비교.
- 단일 행 서브쿼리이므로 `>` 사용 가능.

## **7. GROUP BY + HAVING**
**문제**: 부서별 평균 급여가 500 이상인 부서의 ID와 평균 급여를 출력하시오.
``SELECT dept_id, AVG(salary) AS avg_salary FROM y_emp GROUP BY dept_id HAVING AVG(salary) > 500;

**🔍 설명**:

- `GROUP BY`: 부서별 그룹화.
    
- `HAVING`: 그룹화된 결과 필터링 (WHERE는 개별 행 필터링).
    

## **8. 날짜 함수 (TO_CHAR)**

**문제**: 2020년에 입사한 사원의 이름과 입사일을 'YYYY-MM-DD' 형식으로 출력하시오.

sql

`SELECT emp_name, TO_CHAR(hiredate, 'YYYY-MM-DD') AS hire_date FROM y_emp WHERE TO_CHAR(hiredate, 'YYYY') = '2020';`

**🔍 설명**:

- `TO_CHAR`: 날짜를 문자열로 변환.
    
- 형식 지정을 통해 연도 추출.
    

## **9. UNION**

**문제**: 영업부(dept_id=400)와 경영지원부(dept_id=100) 소속 사원의 이름을 중복 없이 출력하시오.

sql

`SELECT emp_name FROM y_emp WHERE dept_id = 100 UNION SELECT emp_name FROM y_emp WHERE dept_id = 400;`

**🔍 설명**:

- `UNION`: 두 쿼리 결과 합치며 중복 제거.
    
- `UNION ALL`은 중복 허용.
    

## **10. ROW_NUMBER()**

**문제**: 급여 상위 5명의 사원 이름과 급여를 출력하시오.

sql

`SELECT emp_name, salary FROM (   SELECT emp_name, salary, ROW_NUMBER() OVER (ORDER BY salary DESC) AS rn  FROM y_emp ) WHERE rn <= 5;`

**🔍 설명**:

- `ROW_NUMBER()`: 순위 할당.
    
- 서브쿼리로 순위 필터링.
    

## **11. Self-Join**

**문제**: 각 사원의 이름과 해당 관리자 이름을 출력하시오.

sql

`SELECT e.emp_name, m.emp_name AS manager_name FROM y_emp e LEFT JOIN y_emp m ON e.mgr_id = m.emp_id;`

**🔍 설명**:

- 자기 참조 조인: 동일 테이블을 별칭으로 구분.
    
- `LEFT JOIN`: 관리자가 없는 사원도 포함.
    

## **12. EXISTS**

**문제**: 최소 한 명의 부하직원이 있는 관리자의 사번을 출력하시오.

sql

`SELECT DISTINCT mgr_id FROM y_emp m WHERE EXISTS (   SELECT 1 FROM y_emp e WHERE e.mgr_id = m.emp_id );`

**🔍 설명**:

- `EXISTS`: 서브쿼리 결과 존재 여부 확인.
    
- `DISTINCT`: 중복 관리자 제거.
    

## **13. BETWEEN**

**문제**: 급여가 400에서 800 사이인 사원의 이름과 급여를 출력하시오.

sql

`SELECT emp_name, salary FROM y_emp WHERE salary BETWEEN 400 AND 800;`

**🔍 설명**:

- `BETWEEN`: 범위 조건 (양쪽 포함).
    
- `AND`로 범위 경계 지정.
    

## **14. 문자열 연결 (||)**

**문제**: 사원의 이름과 직급을 '이름(직급)' 형식으로 출력하시오.

sql

`SELECT emp_name || '(' || position || ')' AS emp_info FROM y_emp;`

**🔍 설명**:

- `||`: 문자열 연결 연산자.
    
- 괄호와 함께 리터럴 문자열 사용.
    

## **15. CTE (WITH)**

**문제**: 부서별 최대 급여를 받는 사원의 이름과 급여를 출력하시오.

sql

`WITH dept_max AS (   SELECT dept_id, MAX(salary) AS max_salary  FROM y_emp  GROUP BY dept_id ) SELECT e.emp_name, e.salary FROM y_emp e JOIN dept_max d ON e.dept_id = d.dept_id AND e.salary = d.max_salary;`

**🔍 설명**:

- `WITH`: CTE를 사용하여 임시 결과 집합 생성.
    
- 메인 쿼리에서 CTE와 조인.
    

## **16. Pivot (CASE + SUM)**

**문제**: 부서별 남녀 사원 수를 가로로 출력하시오.

sql

`SELECT dept_id,        SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS male,       SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS female FROM y_emp GROUP BY dept_id;`

**🔍 설명**:

- 조건부 집계: `CASE`로 성별 구분 후 `SUM`으로 카운트.
    
- Pivot 테이블 생성.
    

## **17. NULLIF**

**문제**: 커미션(comm)이 0인 경우 NULL로 표시하고 사원 이름과 커미션을 출력하시오.

sql

`SELECT emp_name, NULLIF(comm, 0) AS commission FROM y_emp;`

**🔍 설명**:

- `NULLIF(a, b)`: a가 b와 같으면 NULL 반환.
    
- 0을 NULL로 변환하여 의미 없는 값 처리.
    

## **18. ROLLUP**

**문제**: 부서별, 직급별 급여 합계와 소계/총계를 함께 출력하시오.

sql

`SELECT dept_id, position, SUM(salary) FROM y_emp GROUP BY ROLLUP(dept_id, position);`

**🔍 설명**:

- `ROLLUP`: 계층적 소계 및 총계 생성.
    
- 결과에 `dept_id`별 소계, 전체 총계 포함.
    

## **19. 날짜 차이 계산**

**문제**: 각 사원의 근속 연수를 계산하여 이름과 함께 출력하시오.

sql

`SELECT emp_name,         TRUNC(MONTHS_BETWEEN(SYSDATE, hiredate)/12) AS years FROM y_emp;`

**🔍 설명**:

- `MONTHS_BETWEEN`: 두 날짜 간의 월 차이 계산.
    
- 12로 나누어 연수 변환 후 `TRUNC`로 정수 처리.
    

## **20. CUBE**

**문제**: 부서와 성별 조합별 사원 수를 출력하시오 (모든 조합 포함).

sql

`SELECT dept_id, gender, COUNT(*) FROM y_emp GROUP BY CUBE(dept_id, gender);`

**🔍 설명**:

- `CUBE`: 모든 열 조합에 대한 소계 생성.
    
- 결과에 단일 열 그룹별 소계 및 전체 총계 포함.