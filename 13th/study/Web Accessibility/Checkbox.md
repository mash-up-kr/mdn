# Checkbox 접근성

체크박스는 선택됨, 안 선택됨, `부분 선택됨` 세 가지 상태를 가질 수 있다.

<img width="315" alt="Untitled" src="https://github.com/wonjin-dev/TIL/assets/82315118/1df3f0b2-54f6-49cc-8a85-f71577090c16" />

- 체크박스에 포커스가 있을 때 Space키를 누르면 확인란의 상태가 변경되어야 한다.
- 그룹 체크박스를 통해 모든 체크박스에 대한 상태를 나타내고, 제어할 수 있다.
  - 그룹의 부분적으로 선택된 것이 있을때 선택되었다고 표기되어야 한다.
  - 그룹의 각 옵션은 개별적으로 제어할 수 있어야 한다.
- _일부 구현에서는 부분적으로 체크되었을 때, 어떤 옵션이 체크되었는지 기억하고, 전체 확인란을 통해 기억된 상태를 제공할 수 있다._

## WAI-ARIA

- role=checkbox
- aria-checked
  - 부분 선택시 값은 `mixed`
- aria-labelledby
  > 체크박스에 연결된 레이블의 ID를 지정하여, 체크박스를 설명하는 레이블 요소와 연결하여 접근성을 개선할 수 있다.
- aria-label
  > 체크박스에 대한 액세스 가능한 레이블을 직접 제공. 텍스트 값을 설정하여 체크박스를 설명할 수 있다.
- aria-describedby
  > 체크박스나 체크박스 그룹과 관련된 추가 설명적인 텍스트가 있는 경우, 해당 텍스트를 설명하는 요소의 ID를 지정.

## 체크박스 꾸미기

체크박스의 스타일을 변경할 수는 없다.<br>
반드시 수정해야 한다면 체크 되었을 때 `input[type=checkbox]:checked`에 백그라운드 이미지를 주어 수정할 수 있다.<br>

<br>

여기서 백그라운드 이미지를 사용하지 않는다면 `input[type=checkbox] + label` 을 이용해 꾸밀 수 있다.<br>
이렇게 스타일링 하면 input 자체를 보이지 않게 하고, label에 스타일을 주어서 꾸미는 로직이 작성된다.<br>

즉, 체크박스를 보이지 않게 하기 위해서

```jsx
input[type=checkbox] {
display:none
}.
```

이런식으로 스타일링하게 되는데, 이렇게 되면 웹 접근성이 떨어질 수 있다. <br>
마찬가지로 opacity나 hidden을 이용해 숨기는 것도 접근성에 좋지 못하다.<br>
(포커스를 받은 객체가 포커스를 받았다는 표시. 즉, 점선이나 크롬브라우저에서 글로우 효과가 같이 안 보이게 되기 때문)
<br>
때문에 label에 스타일링하여 체크박스를 꾸미는 경우에는 둘 다 노출시키고 보이지 않게 해야한다.<br>
(z-index를 이용하라는 뜻)

1. 부모 요소에 relative 속성을 주고, input과 label 모두 absolute를 주어 완벽하게 겹치게 한다.
2. input[type=checkbox] 에 z-index를 label보다 높게 준다.

이렇게 되면 라벨의 요소는 input에 가려지게 되는데, 이때<br> 3. label에 input[type=checkbox]:checked 보다 z-index를 높게 할당한다

input[type=checkbox]와 input[type=checkbox]:checked는 같은 객체인데,
선택 여부에 따라 z-index로 인해 라벨보다 앞에 놓이기도 하고 뒤에 놓이기도 한다.
<br><br>
즉, 스타일이 먹힌 label이 보이기도 하고, 스타일이 없는 인풋이 보이게 되어 접근성도 지키면서, 유저 입장에서는 커스텀된 체크박스를 만나게 된다.
