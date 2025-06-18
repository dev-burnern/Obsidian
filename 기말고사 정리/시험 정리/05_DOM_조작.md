# DOM 조작

## DOM이란?
- DOM(Document Object Model)은 HTML 문서를 트리 구조로 표현한 것으로, 자바스크립트로 각 요소를 제어할 수 있게 해줍니다.

## 요소 선택 방법
- getElementById: id 속성으로 요소 선택
- querySelector: CSS 선택자 방식으로 요소 선택
- getElementsByName: name 속성으로 여러 요소 선택
```javascript
const title = document.getElementById('mainTitle');
const button = document.querySelector('.btn');
const inputs = document.getElementsByName('userInput');
```

## 속성 및 내용 조작
- innerHTML: 요소의 HTML 내용 변경
- value: 입력 요소의 값 변경
- style: CSS 스타일 변경
```javascript
title.innerHTML = '제목 변경';
button.style.backgroundColor = 'yellow';
```

## 요소 생성 및 삭제
- createElement: 새로운 요소 생성
- appendChild: 부모 요소에 자식 요소 추가
- removeChild: 부모 요소에서 자식 요소 삭제
```javascript
const newDiv = document.createElement('div');
document.body.appendChild(newDiv);
document.body.removeChild(newDiv);
```

- DOM 조작 시 명확한 변수명과 일관된 코드 스타일을 유지합니다.
- 불필요한 전역 변수 사용을 피하고, 필요한 경우에만 주석을 작성합니다.

> 본 문서는 AI(GitHub Copilot)로 자동 생성되었으니 반드시 사람의 검토와 사실 확인이 필요합니다.
