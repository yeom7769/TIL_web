# HTML

> Hyper Text Markup Language
>
> 웹 컨텐츠의 의미와 구조를 정의할 때 사용하는 언어



## 0. HTML 기초

1. Hyper : 정보가 다중으로 연결되어 있는 상태
2. Hyper Text : 하이퍼링크를 통해 user가 한 문서에서 다른 문서로 접근할 수 있는 텍스트
3. Markup Language : 특정 텍스트에 역할을 부여하는 마킹을 하는 것



## 1. HTML 구조

1. DOM : 문서의 구조화된 표현 제공(문서 구조, 스타일, 내용 변경 가능)

   - 웹 페이지의 객체 지향 표현
   - DOM TREE : 부모 관계, 형제 관계

   

2. 요소(Element) : 태그와 내용으로 구성

   - 태그(element, 요소) : 컨텐츠의 성격과 의미 정의
     - 태그 별 사용 속성은 다름
     - 내용 없는 태그 : br, hr, img, input, link, meta
     - 요소는 중첩될 수 있음
   - 시멘틱 태그 : 의미론적 요소를 담은 태그 ex) section, article ...

   2.1 블럭 레벨 요소(block level element)

   ​		- 블럭 전부 차지 ex) paragraphs(p), lists, nav, footer

   2.2 인라인 요소(inline element)

   ​		- 새로운 줄 만들지 않음 ex) em, strong



3. 속성(attribute) : 태그의 부가적인 정보
   - 요소의 시작 태그에 위치. `이름`과 `값`의 쌍 이룸
   - 태그와 상관없이 사용 가능한 속성 있음
   - ex) class, href, title ...



## 2. 태그

> 시맨틱 태그 : 콘텐츠의 의미를 명확히 설명하는 태그
>
> 🙋‍♀️ 왜 사용? 가독성 up, 접근성 up

🌠 closing tag 없는 것도 있음 ex) img ...

- `head` : 해당 문서의 정보를 담고 있음(제목, 문자 인코딩)
- 외부 로딩 파일 지정 가능 ex)link
  
- `body` : 브라우저 화면에 실질적 정보

- 그룹 컨텐츠 : p, hr, ol, ul, pre, blockquote, div(div는 시맨틱 아님)

- 텍스트 관련 요소 : a, b, i, span, img, em, strong(span은 시맨틱 아님)

- 테이블 관련 요소 : tr, td, th, thead, tbody, tfoot, caption, colspan

- form 태그

  - 입력 데이터를 함께 서버에 요청해주는 태그
  - action : 요청하는 서버의 주소
  - input : 다양한 타입을 가지는 입력 데이터 필드 설정할 수 있음
    - text, checkbox, radio, range, date
    - name(데이터 담을 이름, 변수명), placeholder, required
    - label tag : 서식의 입력의 이름표
  
