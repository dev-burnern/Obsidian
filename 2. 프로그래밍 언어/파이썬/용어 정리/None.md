1. **값 없음 표시**:

- Python에서는 `None`이 값이 없거나 초기화되지 않은 상태를 나타냅니다.

```
def no_return():
    pass

print(no_return())  # 출력: None
```

2. **데이터 타입**:

- `None`은 Python에서 **`NoneType`** 클래스의 유일한 인스턴스입니다.
    
- 이는 Python에서 싱글톤 객체로, 모든 `None` 값은 동일한 객체를 참조합니다.
`print(type(None))  # 출력: <class 'NoneType'>`
**비교 방식**:

- `None`은 항상 **identity 연산자(`is`)**를 사용하여 비교합니다.
    
    python

| **특징**    | **Python (`None`)** | **다른 언어 (`Null`)**    |
| --------- | ------------------- | --------------------- |
| **타입**    | 객체 (`NoneType`)     | 참조형 변수의 비어 있음 표시      |
| **비교 방식** | is None             | == null               |
| **사용 환경** | Python              | C, Java, SQL 등 다양한 언어 |


