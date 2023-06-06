# 가상 스크롤 (Virtual Scroll) ?

Virtual Scroll, Virtual 이라고 하면 여러분은 어떤 것이 생각나시나요 ?
<br>

<img src="https://blog.kakaocdn.net/dn/XaGlE/btsbVdm31rS/7svCCNi3JdqkziTDjvnGD0/img.png" />

<br>
실제로는 존재하지 않지만, 존재하는 것처럼 느껴지게 하는 기술을 Virtual이라고 표현할 수 있을것 같습니다
<br>

그렇다면 Virtual Scroll. 즉, 가상 스크롤은 어떤 기술 일까요 ?
<br>

<img src="https://blog.kakaocdn.net/dn/dfaW0G/btsb2VlQbqL/kWoq1U3484f48ff4FwaeFK/img.png" />

<br>

가상 스크롤은 그림에서 보시는 것처럼 viewport에 해당하는 데이터만 가져와서 화면에 표시하는 기술입니다
<br>
viewport 이외의 요소들은 스크롤 영역만 차지하고 내부 구현은 진행하지 않아 가상 스크롤이라는 이름을 가지고 있습니다
<br>
즉, 가상 스크롤은 돔의 노드 수를 줄임으로써 렌더링 리소스를 줄이는 최적화 기술입니다
<br>
물론 필요하다면 스크롤 컨테이너에서 viewport외의 인비저블 아이템을 추가로 렌더링하여 보다 자연스러운 스크롤을 핸들링 할 수 있습니다

## 그래서 우리는 가상 스크롤을 통해 어떤 이득을 얻을 수 있을까요 ?

앞서 말했듯이 가상 스크롤은 노드의 수를 줄임으로써 최적화한다고 말씀드렸습니다
<br>
<img src="https://blog.kakaocdn.net/dn/YaI2g/btsb5lYWMAc/RHlHXGCPWSUoM60wQKtQN1/img.png" /><br>
자바스크립트 성능 최적화의 저자 니콜라스 자카스의 말을 인용하면 **_DOM은 태생부터 느립니다_**
<br>
DOM 조작은 브라우저의 성능에 직접적인 영향을 미치는 작업이라는 것은 우리 모두가 아는 사실입니다. 때문에 우리는 효울적인 DOM 조작을 통해 브라우저 성능을 개선할 수 있습니다
<br>
<img src="https://blog.kakaocdn.net/dn/begALh/btsbSgZJlHJ/x9PMJspDan4HF1I2QR7vE1/img.png" /><br>
우리가 애용하는 React에서도 공식문서에서 리액트에서 대규모 데이터 렌더링의 최적화 방식에서는 가상화를 얘기하고 있습니다
<br>
가상화. 즉, 많은 노드를 렌더링할 때는 windowing기법을 사용하라고 얘기하고 있습니다
<br>
(여기서 말하는 윈도잉 기법이란 필요한 데이터만 렌더링 하여 렌더링시 리소스를 줄이는 기법)
<br>
윈도잉 기법은 가상 스크롤과 굉장히 맥락이 비슷합니다. 가상 스크롤 역시 스크롤 위치에 따라 해당하는 데이터만 렌더링하는 기법입니다
<br>
즉, 가상 스크롤 역시 윈도잉 기법중 하나이며 우리는 가상스크롤을 통해 노드의 수를 줄여서, 보다 자연스럽고 빠른 스크롤 렌더링을 구현할 수 있습니다

## 그렇다면 무조건 가상 스크롤이 스크롤에 최적화 일까요 ?

<img src="https://blog.kakaocdn.net/dn/o4pd3/btsbTu32vTj/LGa2JhktUTiB230NERrLnK/img.png" /><br>
당연히 아닙니다. 그림을 보면 알 수 있듯이 대규모 데이터를 다룰 때는 가상 스크롤을 구현하는 것이 성능 측면에서 유리할 수 있습니다.
<br>
그러나 단순한 데이터 처리가 필요한 경우에는 Intersection Observer와 같은 브라우저 API를 활용하여 스크롤 로직을 구현하는 것이 더 적합할 수 있습니다
<br>
즉, 가상 스크롤은 대규모 데이터를 효율적으로 렌더링할 때 사용하는 기술이라고 정리할 수 있습니다.
<br>
특히 일반적으로 브라우저보다 성능이 떨어지는 모바일 웹 환경에서 보다 유용한 것으로 알려져 있습니다
<br>
<img src="https://blog.kakaocdn.net/dn/ogCYT/btsb5lED06n/IMQECXoDVJXkAJx1dMpgXK/img.png" />
<br>
위 사진은 실제로 레퍼런스를 찾아보면서 가상 스크롤을 적용하기 전과 후를 크롬 데브 툴로 확인했을 때의 결과입니다. 얼핏보면 큰 차이가 없어 보이지만, 위에 있는 overview로 보게 되면 퍼포먼스에서 분명한 차이를 보이고 있습니다

<br>
레퍼런스를 찾으면서 가상 스크롤이 상황에 따라 유의미한 작업이라는 것은 명확하게 인지할 수 있었습니다
그래서 직접 가상 스크롤 이용해보고자 토이 프로젝트를 생성해보았습니다
<br>
가상 스크롤을 직접 구현할 수 있었지만, 이번 포스팅에서는 가상 스크롤 라이브러리를 만드는 것이 아닌, 가상 스크롤의 소개와 개념 설명에 중점을 가지고 있으므로 react-window라는 라이브러리를 사용하였습니다
<br>
(react-window는 react 가상 스크롤의 가장 대중적인 라이브러리인 react-virtualized의 경량화 버전입니다)
<br>
<img src="https://blog.kakaocdn.net/dn/bcUWVs/btsbShxwsoI/dylVKfqC2En0RXlvAXOouk/img.png" />
<br>
React virtualized를 비롯한 가상 스크롤 관련 라이브러리들에서 요구하는 것도 그렇고 실제로 직접 구현한다고 할 때 가상 스크롤에서 무조건적으로 필요한 부분은 크게 두 가지를 얘기할 수 있습니다
<br>
스크롤 컨테이너의 크기와 그리고 그 안에 들어가는 내부 컨텐츠의 크기입니다.
(예시처럼 종 스크롤일 경우 크기는 height를 나타냅니다)
<br>
<img src="https://blog.kakaocdn.net/dn/kYzQN/btsbSh5k4xj/9N2lZFe2Zn0sipZM8QAeM1/img.png" />
<br>
위의 코드에서 눈여겨볼 부분은 ‘VariableSizeList’ 즉, 컨텐츠 아이템의 높이가 규칙적이지 않은 경우를 고려해야 한다는 것입니다
이는 스크롤 컨테이너의 높이를 알아야 하는 것과 연관되는데, 라이브러리들은 가변적인 내부 컨텐츠 크기에 대하여 계산해주는 부모 컴포넌트들을 제공합니다
<br>
만약 컨텐츠의 크기가 상수로서 고정되어 있다면 FixedSizeList를 통해 보다 쉽게 구현할 수 있습니다
또한 onScroll과 initialScrollOffset등 스크롤 관련하여 자주 사용되는 로직에 대해 유용한 메서드를 제공합니다
<br>
이렇게 구현된 가상 스크롤입니다
<br>
(엘리멘트에서 보다 가시적으로 보실 수 있게 일부러 이미지를 렌더링 하지 않았습니다)
<br>
<img src="https://blog.kakaocdn.net/dn/bq34dk/btsbSgeiVF2/vmg7nNkZXB0y7d2u5FwOfk/img.gif" />
<br>
보시는 것처럼 스크롤 방향에 맞추어 올바른 데이터를 렌더링하며, 일정한 노드 수를 유지 하는 것을 확인할 수 있습니다
<br>
<img src="https://blog.kakaocdn.net/dn/biIHqB/btsbSNbHZAh/frrYq4kRcJWUXa0q8zMWZ1/img.png" />
<br>
9개씩 렌더링 되던 것이 의도적으로 브라우저 크기를 줄이자 5개만 렌더링 되는 것을 확인할 수 있습니다
<br>
가상 스크롤의 핵심인 view port에 맞추어 렌더링 하는것에 대하여 일부러 브라우저 창의 크기를 줄이고 확인했을때 정상적으로 컨텐츠의 수가 줄어들어 렌더링 되는 것을 확인할 수 있었습니다
<br>
<img src="https://blog.kakaocdn.net/dn/bbMp5F/btsb5kMu7Nd/IHT8epoQWHsY7JKF6CYki1/img.gif" />
<br>
보시는 것처럼 컨텐츠 아이템이 일정한 노드 수 만을 가지며 자연스러운 스크롤을 핸들링 할 수 있습니다<br>
(참고로 우측 영상은 좌측 상단에서 노드 수를 확인할 수 있습니다)

### More Detail

추가로 이번 글에서 다루지는 않았지만, react-window와 자주 같이 쓰이는 라이브러인 react-window-infinite-loader
혹은 다른 로직을 이용한 추가 fetch 에 따른 가상 스크롤 구현이나, 윈도잉 기법을 사용할 때 적용할 캐싱 전략 등 ...
<br>
여러가지 최적화를 연결하여 추가로 생각해볼 수 있을 것 같습니다
<br>
물론 가상 스크롤을 사용하더라도 많은 데이터를 초기에 모두 불러오는 경우는 적기에 지연 로딩을 사용해야 하며, 이는 사용자의 스크롤의 속도에 따라 화면의 반짝임이 있다는 것을 의미합니다.
<br>
따라서 일반적인 무한스크롤과 마찬가지로 쓰로틀링 , 디바운싱, 로딩뷰 등 후처리가 동반되어야 할 것입니다.
