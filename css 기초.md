# css

> 스타일, 레이아웃 등을 표시하는 방법을 지정하는 언어
>
> user에게 문서(HTML)가 어떻게 표시 되는지를 지정하는 언어



## 1. CSS 구문

> 선택자와 함께 열린다.
>
> Style 지정 html 요소 선택

1. 적용방법

   - 인라인 방식 : 관리 힘듦, 테스트용
   - 내부 참조 :` <style>` 태그 사용. 모든 html에 적용 할 수 없음
   - 외부 참조 : `<link>` 태그에 css 파일 경로를 명시해서 사용하는 방식



2. ✨선택자(Selector)

   - 클래스(class) 선택자

     - .class명 : 해당 클래가 적용된 문서의 모든 항목을 선택

     

   - 아이디(id) 선택자

     - #문자로 시작하는 아이디 : 문서 당 한번만 사용. 요소에는 단일 id값만 적용
     - 동일한 아이디를 여러 번 사용 가능 but 그렇게 하면 안됨

   

   - 기초 선택자 : 전체 선택(*), 요소(element) 선택자

   

   - 결합자(combinators)

     - 자손 선택자 : ` ` 띄워쓰기로 구분

       `article p { color: red; }`

     - 자식 선택자 : `>` 로 구분, 바로 아래의 요소

       `article > p { color: red; }`

     - 형제 선택자 : `~` 로 구분, 같은 계층에 있는 요소

       `p ~ section~ { color: red; }`

     - 인접형제 선택자 : `+` 로 구분, 바로 아래에 붙어있는 형제 요소(붙어있으면 모두 선택)

       `section + p { color: red; }`

       🔥 적용 순위

       1. `!important`
       2. inline
       3. id 
       4. class
       5. 요소 선택
       6. 코드 순서



3. 상속

   - 상속되는 것 :  텍스트 관련 요소, opacity, visibality

   - 상속되지 않는 것 : box model 관련 요소, position 관련





## 2. CSS 단위

- (상대) 크기 단위
  - px(고정 단위), %(기준 되는 사이즈에서의 배율)
  - em(상속받는 사이즈에서의 배율)/rem(root size 16px 의 배율) 
  - viewport
  - vh, vw(화면 사이즈를 기준으로 함)
  



- 색상 표현 단위

  - 색상 키워드, RGB 색상, HSL 색상(색상, 채도, 명도) ==> a는 alpha(투명도)

  

## 3. **`box model`**

- margin / border / padding / content
- box-sizing
  - content-box :기본값(잘 사용 안함. width 너비는 content 영역을 기준으로 하기 때문)
  - border-box : 테두리까지의 너비를 말함
- 마진상쇄 : top과 bottom margin이 가장 큰 마진으로 결합
  - 수직간의 형제 요소에서 주로 발생. 큰 사이즈의 마진을 조정해준다. padding 이용으로 해결



## 4. display

- block :가로폭 전체를 차지
  - div, ul, ol, p, hr, form
  - 수평 정렬 margine auto 사용
- inline
  - 컨텐트의 너비 만큼 가로 폭을 차지
  - width, height, margin-top, margin-bottom 지정 x
    - line-height로 위아래 간격 조정
- `inline-block`
- none : 화면에서 완전히 없애 버림.
  - visibility : hidden(화면 보여기주만 않을뿐 자리 차지)



## 5. css position

- `static` : 디폴트 값(기준 위치). 부모 요소 내에서 배치될 때는 부모 요소의 위치를 기준으로 배치
- `relative` : static 위치(``본인위치``)를 기준으로 이동(상대위치)
- `absolute` : static 아닌 가장 가까이 있는 부모/조상 요소 기준으로 이동(절대 위치). body까지 올라감.  
  - 기준점(``relative`) 잡아줘야함
- `fixed` :  부모 요소와 관계 없이 `브라우저를 기준`으로 이동(고정위치)
- `sticky` : 스크롤 내려도 따라옴