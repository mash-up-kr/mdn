# Toolbar Pattern

- 툴바는 버튼, 메뉴버튼 또는 체크박스와 같은 **컨트롤 집합(a set of controls)을 그룹화하기 위한 컨테이너**이다.
- 포커스 관리(focus management)를 구현하여 도구 모음 단위에 대한 이동은 화살표 키를 이용하게끔 하고, 도구 모음 내에서의 이동은 Tab을 이용하게 할 수 있다.

## Role, Property, State, and Tabindex Attributes

### Toolbar

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<div>` | toolbar |  | div 요소가 툴바임을 표현한다. |
|  |  | aria-label=”Text Formatting” | 툴바의 레이블을 제공한다. |
|  |  | aria-controls=”ID” | textarea의 ID를 제공하여 해당 툴바가 textarea를 제어함을 표현한다. |

### Toggle Buttons (Bold, Italic, Underline)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<button>` |  | aria-pressed=”true / false” | 버튼이 토글 버튼이며, 해당 버튼이 적용 혹은 미적용 상태임을 알려준다. |
| `<span>` |  | aria-hidden=”true” | 아이콘 문자가 포함되지 않도록 설정해준다. |

### Radio Group (Text Alignment)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<div>` | radiogroup |  | div 요소가 라디오 버튼의 그룹으로 인식되게끔 설정한다. |
|  |  | aria-label=”Text Alignment” | 라디오 버튼 그룹의 레이블을 설정한다. |
| `<button>` | radio |  | 버튼을 라디오 버튼으로 식별하게끔 해준다. |
|  |  | aria-checked=”true” | “false” | 라디오 버튼이 선택되거나 선택되지 않은 경우에 설정해준다. |
| `<span>` |  | aria-hidden=”true” | 아이콘 문자가 포함되지 않도록 설정해준다. |

### Button (Cut, Copy, Paste)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<button>` |  | aria-disabled=”true” | 텍스트가 선택되지 않아 해당 버튼이 활성화되지 않았음을 알리기 위해 사용한다. |
|  |  | aria-disabled=”false” | 버튼이 제공하는 기능을 사용할 수 있게 되었을 때 설정해준다. |

### Menu Button (Font Family)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<button>` |  | aria-label=”Font:FONT_FAMILY_NAME” | 다른 글꼴을 선택할때마다 갱신해주어야 한다. |
|  |  | aria-haspopup=”true” | 해당 버튼이 팝업 메뉴를 가지고 있음을 알리기 위해 사용한다. |
|  |  | aria-controls=”ID” | 버튼이 메뉴를 열거나 닫을 수 있다는 것을 표현한다. |
|  |  | aria-expanded=”true” | 메뉴가 열리면 설정되며, 메뉴가 닫히면 속성을 제거해주어야 한다. |

### Menu (Font Family)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<ul>` | menu |  | ul 요소가 menu임을 표현해준다. |
|  |  | aria-label=”Font Family” | 해당 메뉴가 Font Family라는 레이블을 갖도록 해준다. |
| `<li>` | menuitemradio |  | li 요소의 텍스트 콘텐츠는 menuitemradio의 레이블을 제공한다. |
|  |  | aria-checked=”true” | “false” | 특정 menuitemradio 요소가 선택되었음을 표현하기 위해 선택된 요소는 true로 설정하고, 그 외의 요소는 false로 설정해준다. |
|  |  | tabindex=”-1” | menuitemradio 요소에 포커스를 설정할 수 있도록 해준다. |

### Spin Button (Font Size)

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<div>` | spinbutton |  | 해당 요소를 spinbutton으로 인식하게끔 해준다. |
|  |  | aria-label=”Font size in points” | spinbutton에 대한 레이블을 정의해준다 |
|  |  | aria-valuenow=”NUMBER_POINTS” | spinbutton의 현재 값을 표현한다. |
|  |  | aria-valuetext=”NUMBER_POINTS Points” | NUMBER_POINTS는 자바스크립트를 이용해 갱신하며 현재 값에 포인트라는 단어를 붙여서 읽어주게 한다. |
|  |  | aria-valuemin=”8” | 허용되는 최소값(지원되는 최소 글꼴 크기) |
|  |  | aria-valuemax=”40” | 허용되는 최대값(지원되는 최대 글꼴 크기) |

## on realkit

<aside>
📌 참고할만한 사항들

---

- `Composite`이라는 별도로 정의한 자료구조를 이용해 컴포넌트 간의 상태 공유를 추상화하여 취급함.
    - radix-ui, react-stately 등에서도 사용하는 방식
    - `props getter` 패턴을 이용해 유연한 합성을 가능하게 함.
- `as prop`을 통해 Polymorphic한 컴포넌트로 사용할 수 있게 자유도를 제공함.
</aside>

### Toolbar

```tsx
export const useToolbar = createHook<ToolbarOptions, ToolbarHTMLProps>({
  name: "Toolbar",
  compose: useComposite,
  keys: TOOLBAR_KEYS,

  useProps(options, htmlProps) {
    return {
      role: "toolbar",
      "aria-orientation": options.orientation,
      ...htmlProps,
    };
  },
});

export const Toolbar = createComponent({
  as: "div",
  useHook: useToolbar,
  useCreateElement: (type, props, children) => {
    useWarning(
      !props["aria-label"] && !props["aria-labelledby"],
      "You should provide either `aria-label` or `aria-labelledby` props.",
      "See https://reakit.io/docs/toolbar"
    );
    return useCreateElement(type, props, children);
  },
});

export const useToolbarItem = createHook<
  ToolbarItemOptions,
  ToolbarItemHTMLProps
>({
  name: "ToolbarItem",
  compose: useCompositeItem,
  keys: TOOLBAR_ITEM_KEYS,
});

export const ToolbarItem = createComponent({
  as: "button",
  memo: true,
  useHook: useToolbarItem,
});
```

- `TOOLBAR_STATE_KEYS`
    ```tsx
    const TOOLBAR_STATE_KEYS = [
      "baseId",
      "unstable_idCountRef",
      "unstable_virtual",
      "rtl",
      "orientation",
      "items",
      "groups",
      "currentId",
      "loop",
      "wrap",
      "shift",
      "unstable_moves",
      "unstable_hasActiveWidget",
      "unstable_includesBaseElement",
      "setBaseId",
      "registerItem",
      "unregisterItem",
      "registerGroup",
      "unregisterGroup",
      "move",
      "next",
      "previous",
      "up",
      "down",
      "first",
      "last",
      "sort",
      "unstable_setVirtual",
      "setRTL",
      "setOrientation",
      "setCurrentId",
      "setLoop",
      "setWrap",
      "setShift",
      "reset",
      "unstable_setIncludesBaseElement",
      "unstable_setHasActiveWidget",
    ] as const;
    ```
    

### Example

```tsx
const ToolbarWithMenu = () => {
  const toolbar = useToolbarState();
  
  return (
    <Toolbar {...toolbar} aria-label="Fruits">
      <ToolbarItem {...toolbar} as={Button}>
        Apples
      </ToolbarItem>
      <ToolbarItem {...toolbar} as={Button}>
        Oranges
      </ToolbarItem>
      <ToolbarItem {...toolbar} as={MoreItems} />
    </Toolbar>
  );
};

const MoreItems = forwardRef<HTMLButtonElement, ToolbarProps>(
  (props, ref) => {
    const menu = useMenuState({ placement: "bottom-end" });

    return (
      <>
        <MenuButton {...menu} {...props} ref={ref} aria-label="Other fruits">
          Other Fruits
        </MenuButton>
        <Menu {...menu} aria-label="Other fruits">
          <MenuItem {...menu}>Pears</MenuItem>
          <MenuItem {...menu}>Kiwis</MenuItem>
          <MenuItem {...menu}>Lemons</MenuItem>
        </Menu>
      </>
    );
  }
};
```

## 참고 자료

[ariakit/packages/ariakit-react-core/src/toolbar at main · ariakit/ariakit](https://github.com/ariakit/ariakit/tree/main/packages/ariakit-react-core/src/toolbar)

[ariakit/packages/reakit/src/Toolbar at reakit · ariakit/ariakit](https://github.com/ariakit/ariakit/tree/reakit/packages/reakit/src/Toolbar)