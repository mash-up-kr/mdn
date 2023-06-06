# Images

## 이미지의 종류와 접근성 요약

- `정보 이미지`: 개념과 정보를 그래픽으로 나타내는 이미지, 일반적으로 그림, 사진 및 삽화

  정보 이미지는 이미지에 대하여 대체 텍스트를 사용하여

  - 정보를 보완
  - 정보에 레이블을 지정
  - 간결한 정보를 전달
  - 인상이나 감정을 전달
  - 파일 형식을 전달

  다음과 같은 것들을 보완하여 접근성을 높인다

- `장식 이미지`: 이미지의 목적이 페이지의 이해하는데 중요한 정보가 아닌 시각적 장식인 경우에 추가

  장식 이미지는 단순히 페이지의 모양을 개선하는 것으로 사용 되어야 하기에 사물을 식별하거나 다른 정보를 전달하는 것이라면 정보 이미지로 사용되어야 한다

  또 **AT가 무시해야 하는 이미지의 img 요소에 null 대체 텍스트 및 제목 속성 없음 사용해야 한다**

  제목 속성이 사용되지 않고 대체 텍스트가 null로 설정된 경우 장식 이미지를 안전하게 무시할 수 있음을 나타낸다

  **(*참고:* "null" `alt`속성이 있는 것은 속성이 없는 것과 다르다)**

  ```jsx
  <img {...} alt="" /> !== <img {...} />
  ```

- `기능 이미지`: 링크 또는 버튼으로 사용 되는 이미지

  기능적 이미지는 정보를 전달하기보다는 작업을 시작하는 데 사용한다. 이 경우에는 추가 정보를 제공함으로써 접근성을 높일 수 있다. (단, 독립적이지 않은 경우 대체 텍스트에는 null을 입력해준다)

  - \*\*\*\*단독으로 링크된 로고로 사용된 이미지
  - 링크 텍스트 내의 이미지 (독립적이지 않은 경우)
  - 기능이 있는 독립형 아이콘 이미지
  - 버튼에 사용된 이미지\*\*\*\*

  인상 깊었던 부분

  ```jsx
  <form action='end-point' method='HTTP'>
    <input type='image' name='submit' src='button.gif' alt='Submit' />
  </form>
  ```

- `복잡한 이미지`(그래프 및 다이어그램 등): 데이터 또는 자세한 정보를 전달하기 위해 이미지에서 제공되는 데이터 또는 정보와 동등한 완전한 텍스트를 텍스트 대안으로 제공

  두 가지 대체 텍스트가 필요하다.
  첫 번째는 이미지를 식별하기 위한 간단한 설명이다. 긴 설명의 위치를 나타내기도 한다
  두 번째는 이미지가 전달하는 필수 정보를 텍스트로 표현한 긴 설명이다

  이때 긴 설명은 다양한 방법으로 접근할 수 있다

  **1. 별도의 웹 페이지 또는 자세한 설명이 포함된 웹 페이지의 링크를 이미지 옆에 제공**

  > 이 접근 방식은 모든 웹 브라우저와 보조 기술에서 사용할 수 있다. 그러나 링크와 이미지가 의미론적으로 연관되지 않는다. 때문에 HTML5 `<figure>`및 `<figcaption>`요소를 통해 의미론적으로 이미지와 링크를 그룹화 할 수 있다. 3번 접근법 참고

  **2. alt 속성에 긴설명 위치 표기**

  > 이미지와 동일한 웹 페이지에 긴 설명이 제공되는 경우 alt 속성을 이용하여 위치를 설명할 수 있다. 해당 접근시 위치 정보는 사용자가 콘텐츠를 찾는 데 도움이 되도록 명확하고 정확해야 한다

  **3. 이미지와 인접한 긴 설명을 구조적으로 연결**
  HTML5 `figure`요소는 이미지와 자세한 설명을 모두 포함하는 데 사용할 수 있습니다. 긴 설명은 `<figcaption>`요소에 래핑된다. 요소의 의미를 전달하지 않는 웹 브라우저의 `role="group"`포함 요소를 사용하여 이미지와 명시적으로 연결된다

  ```jsx
  // 별도의 웹 페이지 제공시

  <p>
    {/* img에 대한 짧은 설명 이후*/}
    <img
      src="chart.png"
      alt="Bar chart showing monthly and total visitors for the first quarter 2014 for sites 1 to 3">
    <br>
    {/* img에 대한 긴 설명 위치 표기*/}
    <a href="2014-first-qtr.html">Example.com Site visitors Jan to March 2014 text description of the bar chart</a>
  </p>

  // HTML5
  <figure role="group">
      <img
          src="chart.png"
          alt="Bar chart showing monthly and total visitors for the first quarter 2014 for sites 1 to 3">
      <figcaption>
          <a href="2014-first-qtr.html">Example.com Site visitors Jan to March 2014 text description of the bar chart</a>
      </figcaption>
  </figure>
  ```

  **4. longdesc 속성을 이용하여 설명**
  속성에 긴 설명이 있는 다른 웹 페이지를 참조하기 위한 URI가 포함된 경우 `longdesc` 도 적용하는 것도 좋다. (접근법 1에도 사용 가능) 
  또한 `longdesc`는 식별자가 포함되어 있으면 동일한 웹 페이지에서 긴 설명을 제공하는 요소를 나타낸다 (접근법 2에도 사용 가능)

  **5.** `aria-describedby="description"` **사용**

  ```jsx
  <img {...} aria-describedby="description">
  {...}
  <p id="description">
  {...}
  </p>
  ```

- `이미지 그룹`: 여러 이미지가 단일 정보를 전달하는 경우 하나의 이미지에 대한 대체 텍스트는 전체 그룹에 대한 정보를 전달해야 한다

  이미지 그룹이 함께 사용되어 하나의 정보를 나타낼때 전체 이미지를 설명하는 **하나의 대체 텍스트**가 필요하다

  또 마찬가지로 HMTL5의 `<figure>`, `<figcaption>`을 사용할 수 있는데, `<figure>`를 중복으로 사용하여 전체 이미지 컬렉션에 대한 캡션을 제공할 수 있다. 이 경우에 요소가 개별 그룹의 레이블로 사용되는지 `aria-labelledby`를 통해 확인할 수 있다

  ```jsx
  <figure role="group" aria-labelledby="fig1">
    <figcaption id="fig1">
  		{...}
    </figcaption>

    <figure role="group" aria-labelledby="fig11">
      <img {...} />
      <figcaption id="fig11">{...}</figcaption>
    </figure>
  </figure>
  ```

- `이미지 맵`: 클릭 가능한 여러 영역이 포함된 이미지의 대체 텍스트는 링크 집합에 대한 전반적인 컨텍스트를 제공해야 한다.
  또 개별적으로 클릭할 수 있는 각 영역에는 링크의 목적이나 대상을 설명하는 대체 텍스트가 있어야 한다
  클라이언트측에서 이미지 맵은 `<area>`사용자 상호 작용을 허용하는 요소이다. 선택 가능한 영역으로 분할된 이미지이며, 일반적으로 선택 가능한 영역은 다른 페이지에 대한 링크이다.

      `<img>`이미지 맵의 경우 요소 자체의 정보 맥락을 전달하기 위해 각 요소 `<area>`(링크 대상 또는 링크를 따라가는 경우 시작되는 작업을 전달하기 위해) 모두에 대체 텍스트가 필요하다

      ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e7f58d7d-2310-4107-88ec-f0006291f73c/Untitled.png)

      ```jsx
      <img src="orgchart.png"
           alt="Board of directors and related staff: "
           usemap="#Map">

      <map name="Map" id="Map">
        <area
          shape="rect"
          coords="176,14,323,58"
          href="…"
          alt="Davy Jones: Chairman"
        >

        <area
          shape="rect"
          coords="81,75,226,114"
          href="…"
          alt="Carole Brewster: Company Secretary"
        >
        <area
          shape="rect"
          coords="6,138,155,182"
          href="…"
          alt="Harry H Brown: Marketing Director (reports to chairman)"
        >
        <area
          shape="rect"
          coords="175,138,323,182"
          href="…"
          alt="Paula Holbein: Sales Director (reports to chairman)"
        >
        <area
          shape="rect"
          coords="345,136,496,186"
          href="…"
          alt="Hugh Howard: Finance Director (reports to chairman)"
        >
      </map>
      ```

## 이미지에 대하여 접근성을 준수할 때 얻을 수 있는 효과

- 이미지와 그래픽은 많은 사람들, 특히 인지 및 학습 장애가 있는 사람들이 콘텐츠를 더 즐겁고 이해하기 쉽게 만든다
- 시각 장애가 있는 사람이 콘텐츠에서 방향을 잡기 위해 사용하는 단서 역할을 한다
- 스크린 리더를 사용하는 사람들을 위해 대체 텍스트를 소리내어 읽거나 점자로 렌더링할 수 있다
- 음성 입력 소프트웨어를 사용하는 사람이 단일 음성 명령으로 버튼 또는 연결된 이미지에 초점을 맞출 수 있다
- 모바일 웹 사용자가 특정 환경에서 이미지를 불러오지 않을 수 있다
- 검색 엔진에서 이미지를 인덱싱 할 수 있다

# on React

- 이미지 그룹

  ```jsx
  import {FC, ReactNode} from 'react';

  type ImageType = {
    id: string,
    src: string,
    desc: string,
  };

  interface ImageGroupProps {
    group: {
      id: string,
      node: ReactNode,
    };
    imgs: ImageType[];
  }

  const ImageGroup: FC<ImageGroupProps> = ({group, imgs}) => {
    return (
      <figure role='group' aria-labelledby={group.id}>
        <figcaption id={group.id}>{group.node}</figcaption>

        {imgs.map((img) => {
          return (
            <figure role='group' aria-labelledby={img.id}>
              <figcaption id={img.id}>
                <img src={img.src} alt={img.desc} />
              </figcaption>
            </figure>
          );
        })}
      </figure>
    );
  };

  export default ImageGroup;
  ```

<br>

- 뭐가 더 좋아 보이나요 ?

  ```jsx
  import {FC} from 'react';

  interface ImageButtonProps {
    img: string;
    role: string;
    onClick: () => void;
  }

  const ImageButton: FC<ImageButtonProps> = ({img, role, onClick}) => {
    return <input type='image' src={img} alt={role} onClick={onClick} />;
  };

  // const ImageButton: FC<ImageButtonProps> = ({img, role, onClick}) => {
  //   return (
  //     <button onClick={onClick}>
  //       <img src={img} alt={role} />
  //     </button>
  //   );
  // };

  export default ImageButton;
  ```
