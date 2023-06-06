# Feed Pattern

`A feed is a section of a page that automatically loads new sections of content as the user scrolls`

## What is a Feed Pattern in Web Accessibility?

- 여기서 정의된 피드는 article 엘리먼트로 구성이 된 동적인 리스트
- 피드는 주로 무한 스크롤로 나오는 경우가 흔한 케이스
- 접근성 측면에서의 특이점은 접근성을 준수한 **피드**는 유저 스크롤 시 데이터 로딩을 하는 부분도 대응을 함
  - 기본적으로 보조기술은 피드 컨텐츠를 **읽기 모드**로 접근
  - 그래서 일반적인 보조기술 관점에서 새로운 데이터를 로드 하는 행위는 버튼을 클릭하여 하는 방법이 흔한 보조기술을 연동한 UX
  - **하지만,** 피드 같은 경우는 웹과 보조기술 간의 상호 운용성을 체결
  - 스크롤 인터렉션에 따라 보조기술이 피드 컨텐츠를 읽거나 새로운 컨텐츠를 로드하는 부분도 읽기 모드에서 가능하게끔 함
- 또한, 피드 패턴은 보조기술 사용자에게도 내용을 **훓어 보는 것**을 가능하게 함

## Example

- 아래 코드는 `role=”feed”`를 활용하여 무한 스크롤과 같은 스크롤 액션이나 다른 방법으로 동적으로 컨텐츠를 들고오는 경우에 웹 접근성에 맞춘 예시
- 추가적으로 아래에는 포함되어 있지 않지만 피드 같은 경우에는 키보드 인터렉션이 추가 되는 것을 공식 문서에서는 추천함
  - `Page Down` - 다음 아티클로 포커스 이동
  - `Page Up` - 이전 아티클로 포커스 이동
  - `Control + End` - 피드 다음으로 가장 첫번째 포커스가 가능한 엘리먼트로 이동
  - `Control + Home` - 피드 가장 직전으로 포커스가 가능한 엘리먼트로 이동

### Feed Component

```tsx
  import { useEffect } from "react"

  interface FeedItem {
    id: number
    name: string
    desc: string
    author: string
  }

  const Feed = () => {
    const [feedList, setFeedList] = useState<FeedItem[]>([])
    return (
      // 피드 아티클을 담고 있는 엘리먼트는 role="feed" 속성을 가져야 한다.
      // 새로운 피드 아티클을 들고 오는 경우 aria-busy를 동적으로 true로 변경해야 한다
      <div role="feed" aria-busy="false">
        {feedList.map((item, i) => (
          <FeedItem item={item} index={i} feedTotalLength={feedList.length} />
        )}
      </div>
    )
  }
```

### FeedItem Component

- 피드 아이템은 `role="article"` 속성을 가져야 한다 -> 예시에서는 `article` 엘리먼트 사용
- `aria-posinset` 속성은 피드 아이템의 순서를 나타낸다.
- `aria-setsize` 속성은 피드 아이템의 총 개수를 나타낸다. -> 총 사이즈를 알 수 없는 경우 "-1"로 설정
  - 피드의 총 개수 혹은 로드가 된 피드의 총 개수 (유저에게 더 중요한 개수로 설정)
- `aria-labelledby` 속성은 피드 아이템의 제목을 나타낸다.
- `aria-describedby` 속성은 피드 아이템의 설명과 작성자와 같은 설명을 나타낸다.
- `tabindex`는 article 엘리먼트가 포커스가 가능하게 하고 Tab에 반응하도록 설정

```tsx
interface FeedItemProps {
  item: FeedItem;
  index: number;
  feedTotalLength: number;
}

const FeedItem = ({ item, index, feedTotalLength }: FeedItemProps) => {
  return (
    <article
      aria-posinset={index + 1}
      aria-setsize={feedTotalLength}
      aria-labelledby={`feed-name-${item.id}`}
      aria-describedby={`feed-description-${item.id} feed-author-${item.id}`}
      tabindex="0"
    >
      <div id={`feed-name-${item.id}`}>{item.name}</div>
      <div>
        <div id={`feed-description-${item.id}`}>{item.desc}</div>
        <div id={`feed-author-${item.id}`}>{item.author}</div>
      </div>
    </article>
  );
};
```
