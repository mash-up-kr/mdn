# Basic Considerations for Web Accessibility

## Associate a label with every form control

**`모든 form에는 label을 활용하자!`**

- Label에 "for" 속성을 활용하여 input의 ID 속성과 연결시키기
- Label을 가려야 할 경우도 있긴 하지만 평상적으로 label 엘리먼트가 독자들이 해당 input에 대한 이해를 위해 사용해야 함

```jsx
<label for="username">Username</label>
<input id="username" type="text" name="username">
```

## Include alternative text for images

`이미지에는 대체 택스트를 추가하자!`

- 정보성, 기능성 이미지에는 무조건 대체 택스트 추가하기
- 장식용 이미지 같은 경우에는 비어있는 대체 텍스트 활용 → `alt=” “`

```jsx
<img src="..." alt="profile-image" />
```

## Identify page language and language changes

`페이지의 언어와 언어 변화를 명시하자!`

- html 태그에 lang 속성을 꼭 명시하기 → `<html lang=”en”>`
- 엘리먼트가 다른 페이지에 있는 엘리먼트와 언어가 다르다면 lang 속성으로 명시하기

## Use mark-up to convey meaning and structure

`마크업으로 구조와 의미를 전달하자!`

- heading, lists, table 등을 적절한 HTML 마크업으로 사용하기
- nav, aside와 같은 추가적인 엘리먼트도 제공하기에 적절하게 잘 사용하기
- WAI-ARIA role이 추가적인 의미나 기능을 설명 할 수 있다 → `role=”search”`

```jsx
// 적절한 마크업 활용한 예시
<section>
  <article>
    <h2>Superbear saves the day</h2>
    <time datetime="2015-08-07">7 Aug 2015</time>
    <p>The city's favorite bear yet again proves his mettle by rescuing a young cat from a tree. Witnesses say that Superbear's efforts were not appreciated by the feline, who inflicted some minor scratch wounds on his rescuer.</p>
    <aside>
      <h3>Related Articles</h3>
      <ul>
        <li><a href="#">Bear receives key to city</a></li>
        <li><a href="#">Superbear stands for mayor</a></li>
      </ul>
    </aside>
  </article>
</section>

// WAI-ARIA 속성을 활용한 마크업
<form action="#" method="post">
  <div role="search">
    <label for="search">Search for</label>
    <input type="search" id="search" aria-describedby="search-help">
    <div id="search-help">Search records by customer id or name</div>
    <button type="submit">Go</button>
  </div>
</form>
```

## Help users avoid and correct mistakes

`명확한 지침, 에러 메시지, 알림, 등을 제공하여 유저의 실수를 방지하자!`

- 에러가 발생 했을 때 유저가 어디가 문제인지, 문제에 대한 구체적인 설명과 수정을 제안을 하기
- 유저 입력의 형식을 넓게 허용하기

```jsx
// 유저 입력에 대해 forgiving한 형식으로 제공하기
<label for="phone">Phone</label>
<input id="phone" name="phone" type="tel"
       pattern="^(\(?0[1-9]{1}\)?)?[0-9 -]*$"
       aria-describedby="phone-desc">
<p id="phone-desc">For example, (02) 1234 1234</p>
```

## Reflect the reading order in the code order

`엘리먼트의 순서가 코드의 논리적 순서와 일치하도록 하자!`

- 코드를 읽는 순서와 실제 반영이 되는 엘리먼트들이 보여지는 순서와 일치하도록 하기

```jsx
// 헤딩이 먼저 들어온 경우 X
<img src="images/trainer.png" alt="...">
<h3>Space trainers</h3>
<p>Space...</p>
<a href="...">Add to cart</a>

// 헤딩이 섹션의 시작을 나타내는 예시 O
<h3>Space trainers</h3>
<img src="images/trainer.png" alt="...">
<p>Space...</p>
<a href="...">Add to cart</a>
```

## Write code that adapts to the user’s technology

`반응형 디자인으로 여러 디바이스를 지원하는 코드를 작성하자!`

- 확대/축소, 여러 viewport 사이즈에도 적응하는 코드를 작성하기
- 폰트 사이즈가 최소 200% 이상 늘어났을때 가로 스크롤이나 내용이 clipping 되는 것을 방지하기
- 핵심 기능과 내용이 기술과 상관없이 정확하게 보여 질 수 있도록 점진적인 향상으로 접근하기

```jsx
/* 너비가 좁은 뷰포트에서는 네비게이션을 전체 너비로 변경 */
@media screen and (min-width: 25em) {
  #nav {
    float: none;
    width: auto;
  }
  #main {
    margin-left: 0;
  }
}
/* 너비가 긴 뷰포트에서는 네비게이션을 왼쪽에다가 float 시키기 */
@media screen and (min-width: 43em) {
  #nav {
    float: left;
    width: 24%;
  }
  #main {
    margin-left: 27%;
  }
}
```

## ****Provide meaning for non-standard interactive elements****

`WAI-ARIA를 활용하여 커스텀으로 만든 위젯이나 버튼의 기능에 설명을 부여하자!`

- `role=”navigation”` 이나 `aria-expanded=”true”` 와 같은 속성을 활용하여 설명 부여하기
- 위젯의 기능에 따라서 추가 코드를 활용하여 기능에 대한 설명을 넣어주기

```jsx
// 메뉴의 기능을 aria 속성들로 설명 부여
<nav aria-label="Main Navigation" role="navigation">
  <ul>
    <li><a href="...">Home</a></li>
    <li><a href="...">Shop</a></li>
    <li class="has-submenu">
      <a aria-expanded="false" aria-haspopup="true" href="...">SpaceBears</a>
      <ul>
          <li><a href="...">SpaceBear 6</a></li>
          <li><a href="...">SpaceBear 6 Plus</a></li>
      </ul>
    </li>
    <li><a href="...">MarsCars</a></li>
    <li><a href="...">Contact</a></li>
  </ul>
</nav>
```

## ****Ensure that all interactive elements are keyboard accessible****

`반응형 엘리먼트들에 대해서는 키보드 조작이 가능하게 하자!`

- 메뉴, 마우스오버로 나타나는 정보, 아코디언, 미디어 플레이어 등 유저의 반응으로 조작 가능한 엘리먼트들은 키보드로 기능을 접근 할 수 있게 하기

```jsx
var buttonExample = document.getElementById('example-button');

buttonExample.addEventListener('keydown', function(e) {
  // Toggle the menu when RETURN is pressed
  if(e.keyCode && e.keyCode == 13) {
    toggleMenu(document.getElementById('example-button-menu'));
  }
});

buttonExample.addEventListener('click', function(e) {
// Toggle the menu on mouse click
  toggleMenu(document.getElementById('example-button-menu'));
});
```

## ****Avoid CAPTCHA where possible****

`CAPTCHA는 최대한 거르자!`

- CAPTCHA는 기본적으로 유저들에게 많은 문제가 되기에 사용을 최대한이면 하지 않도록 하기
- 사람이 입력 했다는 것을 확인하는 방법은 CAPTCHA 외에도 많기 때문에 사람들이 사용하기에 편한 방법으로 사용하기
- CAPTCHA를 사용해야 한다면 장애가 있는 사람들도 사용 할 수 있도록 유의하면서 만들기:
    - CAPTCHA를 해결하기 위한 방법을 2가지 이상으로 제공
    - CPATCHA를 우회 할 수 있는 방법도 별개로 마련
    - 권한이 있는 유저에게는 CAPTCHA를 필수화 시키지 않는 것