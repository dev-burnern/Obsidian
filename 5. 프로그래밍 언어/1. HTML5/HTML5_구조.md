# HTML5 전체 구조 개요

HTML5는 웹 문서의 표준 마크업 언어로, 문서의 구조와 의미를 명확하게 표현할 수 있도록 다양한 태그와 시맨틱 구조를 제공합니다.

## 주요 구조
- 문서 선언: `<!DOCTYPE html>`
- 루트 요소: `<html lang="ko"> ... </html>`
- 메타데이터: `<head> ... </head>`
- 본문: `<body> ... </body>`

## 시맨틱 구조 예시
- `<header>`: 머리말(로고, 네비게이션 등)
- `<nav>`: 내비게이션(메뉴)
- `<main>`: 주요 콘텐츠
- `<section>`: 구획(논리적 구분)
- `<article>`: 독립적 글/기사
- `<aside>`: 보조 정보
- `<footer>`: 바닥글(저작권 등)

## 기본 예시
```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8">
    <title>HTML5 구조 예시</title>
  </head>
  <body>
    <header>머리말</header>
    <nav>메뉴</nav>
    <main>
      <section>
        <article>본문 내용</article>
      </section>
      <aside>보조 정보</aside>
    </main>
    <footer>바닥글</footer>
  </body>
</html>
```

---

> 이 파일은 HTML5 전체 구조의 개념과 시맨틱 태그의 역할을 옵시디언 마크다운 양식에 맞춰 정리한 것입니다.
