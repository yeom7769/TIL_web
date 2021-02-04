# css layout

> 웹페이지에 포함되는 요소들을 취합. 어느 위치에 놓일 것인지 제어하는 기술
>
> ex) display, position, float, flexbox, grid



## 1. float

> 요소들의 구조 정리

1. none :기본값
2. left :요소를 왼쪽으로 띄움
3. right : 요소를 오른쪽으로 띄움

- float한 요소가 나머지에 영향을 줌 ==> 겹쳐짐 등

  - 강제로 막는 방법을 찾아야함

  - ````html
    .clearfix::after {
    	content: "";
    	display: block;
    	clear: both;
    }
    # after, before = 의사요소(가상요소)

    # hover 마우스 댔을 때만 적용
    aside:hover {
    	transform: scale(1.5);
    	# 크기를 1.5배 키움
    	transition: transform is ease-in-out;
    	# 서서히 커짐
    }
    ````
    
  - 현재는 잘 사용 x. but 간단한 nav같은 경우 사용



## 2. 📌flexbox : flexible box layout

1. 요소 : flex contatiner, flex item
2. 축

- 초기 설정. 클래스로 flex 설정 해줘야함.

```html
.flex-container {
	display: flex;
	/*display: inline-flex*/
}
```

1️⃣ 배치(위치) 방향 설정

**`flex-direction`**  :  row▶, row-reverse◀, column🔽, column-reverse🔼

: 메인축만 바뀐다. flexbox는 단방향 레이아웃이기 때문

- flex-start, flex-end, center, space-between, space-around, space-evenly



2️⃣ 메인축 방향 정렬

**`justify-content`**  :   justify(메인축 정렬),  align(교차축 정렬)



3️⃣ 교차축 방향 정렬

**`align-items, align-self, align-content`**  :  content(여러줄) ,  items(한줄), self(개별 요소)

🔥 여기서 중요한 점은 items, content의 판별 기준은 교차축 기준으로 판별



4️⃣ 기타

**`flex-wrap`** : 컨테이너 크기를 넘어가면 아래로 정렬

**`flex-flow`** : direction 과 wrap shortand

**`flex-grow`** : 크기 키우기 ==> 남은 부분에서 상대적 크기 할당

**`order`** :  아이템의 순서 지정. 지정 없으면 0으로 설정됨.





## 3. Bootstrap grid system

> 12 column ==> 약수가 많아서 많이 채택

```html
<div class="container">
    <div class="row">
      <div class="col-2">1</div>
      <div class="col-8">2</div>
      <div class="col-2">3</div>
    </div>
  </div>
/* col-n은 12열 중에 각자 차지하는 열 */
```

- row

  - columns의 wrapper
  - 좌우 padding= gutter

- col

  - columns 너비는 부모 요소를 기준으로 유동적으로 크기가 조정

- offset

  - 지정한 만큼 column 공간 무시(기재한 아이템 앞에 구현됨)

    ```html
    class="col-12 col-md-10 offset-md-2 col-lg-11 offset-md-1"
    ```

- Grid breakpoints

  - 특정 px 지점을 정해 크기 변화

