# Tabs Pattern

- 탭은 한 번에 하나의 콘텐츠 패널을 표시하는 탭 패널(Tab Panel)이라고 하는 계층화된 콘텐츠 섹션 집합(a set of layered sections of content)이다.
- 탭 요소 목록은 현재 표시된 패널의 한쪽 가장자리(일반적으로는 위쪽 가장자리)를 따라 정렬된다.
- 탭 인터페이스가 초기화되면 하나의 탭 패널이 표시되고, 활성 상태임을 나타내도록 해당 탭의 스타일이 지정되게 된다.
- 크게 두 가지 방법으로 구현이 가능하며, (1) 탭에 포커스가 가는 순간 해당 탭을 활성화 시키는 방법(`Automatic Activation`)과 (2) 탭의 포커스와 별도로 추가적인 입력을 통해 특정 탭을 활성화 시키는 방법(`Manual Activation`)이 존재한다.

## Keyboard Interaction

- `Tab`
    - 탭 자체에 포커스가 이동하게 된 경우, 현재 활성화 된 탭 요소에 포커스가 되어야 한다.
- `Left Arrow` / `Right Arrow`
    - 각각 이전 탭과 다음 탭으로 이동하며, 요구사항에 따라 바로 탭을 활성화시킬 것인지 아니면 사용자의 입력(`Space` 또는 `Enter`)을 전달받아 활성화할지 구현할 수 있다.
    - 각각 첫번째 탭 그리고 마지막 탭에서 입력한 경우 마지막 탭 그리고 첫번째 탭으로 이동시킬 수 있어야 한다.

- `Space`(`Enter`)
    - 현재 포커스가 되어 있는 요소가 위와 같이 바로 활성화되는 형태로 구현되어 있지 않다면 Space 또는 Enter가 입력되었을 때 활성화시켜 주어야 한다.
- `Home` / `End` (Optional)
    - 입력 시 각각 첫번째 탭과 마지막 탭으로 이동시킬 수 있어야 한다.

## Automatic Activation vs. Manual Activation

> 패널에 노출하기 위한 데이터가 모두 준비되어 있는 경우에는 이러한 자동 활성화 구현을 패널에 노출하기 위한 데이터를 비동기적으로 불러와야 하는 경우에는 수동 활성화 구현을 고려하는 것이 권장된다.
> 

### WAI-ARIA Roles, States, and Properties

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<div>` | tablist |  | 요소가 탭들의 컨테이너 역할을 수행함을나타낸다. |
|  |  | aria-labelledby="ID_REFERENCE" or aria-label="LABEL" | 탭들의 목적을 설명하는 레이블을 제공한다. |
| `<button>` | tab |  | - 요소가 탭을 컨트롤하는 역할을 수행함을 의미한다.<br/>- 포커스되면, 자동으로 활성화되며 연관된 탭 패널을 보여준다.<br/>- 탭 패널과 연관된 label을 제공한다. |
|  |  | `aria-selected=”true”` | 해당 탭 패널이 표시되고 있음을 나타내며 탭이 포커스를 받을 때 설정해 주어야 한다. |
|  |  | `aria-selected=”false”` | 해당 탭이 선택되지 않은 상태로 탭 패널이 노출되지 않는다는 것을 의미한다. 현재 선택된 탭을 제외한 모든 탭에 대해 설정해 주어야 한다. |
|  |  | `tabindex="-1"` | 현재 활성화 된 탭을 제외하고는 명시적으로 -1로 설정하여 포커스가 이동되지 않도록 방지시켜준다. |
|  |  | `aria-controls=”ID_REFERENCE”` | 탭과 관련되어 있는 role=”tabpanel” 요소를 참조한다. |
| `<div>` | tabpanel |  | 해당 요소가 탭 패널의 컨테이너 역할을 수행함을 표현하며, 연결되어 있는 탭 컨트롤이 활성화 상태가 아닌 경우에는 숨겨진다. |
|  |  | `aria-labelledby=”ID_REFERENCE”` or `aria-label="LABEL"` | 탭 패널에 접근 가능한 이름을 제공하며, 패널을 제어하는 탭 요소를 나타낸다. |
|  |  | `tabindex="0"` | 탭 패널에도 포커스가 이동될 수 있도록 선택된 탭 패널에 대해서는 명시적으로 설정해준다. |

## Implementation

> 탭 인터페이스를 구현할 때는 크게 두 가지 접근 방식이 존재하는데, 각각 **합성 기반(Composition-based)**과 **구성 기반(Configuration-based)** 구현 방법이다.
> 

### 구성 기반 구현(Configuration-based)

```tsx
import type { ReactNode } from "react";
import { useState } from "react";
 
const tabs: Tab[] = [
  { label: "tab 1", key: "tab1", content: <p>content for tab 1</p>, contentId },
  { label: "tab 2", key: "tab2", content: <p>content for tab 2</p> },
  { label: "tab 3", key: "tab3", content: <p>content for tab 3</p> }
];

interface Tab {
  key: string;
  label: string;
  panel: {
    id: string;
    content: ReactNode;
  }
}

interface TabsProps {
  tabs: Tab[];
  defaultSelectedTab: Tab;
}

const Tabs = ({ tabs, defaultSelectedTab, ariaLabel }: TabsProps) => {
  const [selectedTab, setSelectedTab] = useState(defaultSelectedTab);

  return (
    <div role="tablist" aria-label={ariaLabel}>
      {tabs.map((tab) => {
        return (
          <button
            key={tab.key}
            type="button"
            role="tab"
            onClick={() => setSelectedTab(tab)}
            aria-selected={tab.key === selectedTab.key}
            tabIndex={tab.key !== selectedTab.key ? -1 : 0}
            aria-controls={tab.panel.id}
          >
            {tab.label}
          </button>
        )
      })}
      <div role="tabpanel" tabIndex="0" id={selectedTab.panel.id}>
        {selectedTab.panel.content}
      </div>
    </div>
  );
};
```

- 구성 기반으로 접근하면 배열을 전달하여 렌더링하므로, 배열 인덱스를 사용해 활성화 상태나 형제 탭 목록을 제어하거나 추적하기 쉽다는 장점이 존재한다.
    - 하지만 데이터의 구조와 HTML 구조 간에 강한 결합이 발생하여 유연성이 떨어지고, Tab[]의 인터페이스를 변경하지 않는 한 HTML 구조를 쉽게 변경하기가 어렵다.

### 합성 기반 구현 (Composition-based)

```tsx
const App = () => {
  return (
    <Tabs defaultSelectedTab="tab2">
      <TabList aria-label="tab list">
        <Tab tab="tab1">tab 1</Tab>
        <Tab tab="tab2">tab 2</Tab>
        <Tab tab="tab3">tab 3</Tab>
      </TabList>
      <TabPanel tab="tab1">content for tab 1</TabPanel>
      <TabPanel tab="tab2">content for tab 2</TabPanel>
      <TabPanel tab="tab3">content for tab 3</TabPanel>
    </Tabs>
  );
};
```

```tsx
import {
  createContext,
  useMemo,
  useCallback,
  useRef,
  useState,
} from "react";
import type {
  ReactNode,
  PropsWithChildren,
  KeyboardEvent,
} from "react";
import { uuid } from "uuid";

interface TabsContextShape {
  selectedTab: string | null;
  setSelectedTab: (tab: string) => void;
  tabsPrefix: string;
}

const TabsContext = createContext<TabsContextShape>({
  tabsPrefix: "",
  selectedTab: null,
  setSelectedTab: (tab: string) => {
    throw new Error(
      "Should be used with TabsContext.Provider"
    );
  },
});

interface BaseTabsProps {
  defaultSelectedTab: string;
}

const Tabs = ({
  children,
  defaultSelectedTab,
}: PropsWithChildren<BaseTabsProps>) => {
  const tabsPrefix = useMemo(() => uuid(), []);
  const [selectedTab, setSelectedTab] = useState(
    defaultSelectedTab
  );

  const value = useMemo(
    () => ({
      selectedTab,
      setSelectedTab,
      tabPrefix,
    }),
    [selectedTab, setSelectedTab]
  );

  return (
    <TabsContext.Provider value={value}>
      {children}
    </TabsContext.Provider>
  );
};

interface BaseTabListProps {
  label: string;
}

const TabList = ({
  children,
  label,
}: PropsWithChildren<BaseTabListProps>) => {
  const listRef = useRef<HTMLDivElement>(null);

  const handleKeyDown = useCallback(
    (event: KeyboardEvent) => {
      if (!listRef.current) return;

      const $tabs = [
        ...listRef.current.querySelectorAll(
          '[role="tab"]:not([disabled])'
        ),
      ];

      const index = $tabs.indexOf(document.activeElement as HTMLElement);
      if (index < 0) return;

      switch (event.key) {
        case "ArrowUp":
        case "ArrowLeft": {
          const next = (index - 1 + $tabs.length) % $tabs.length;
          $tabs[next]?.focus();
          break;
        }
   
        case "ArrowDown":
        case "ArrowRight": {
          const next = (index + 1 + $tabs.length) % $tabs.length;
          $tabs[next]?.focus();
          break;
        }
      }
    },
    []
  );

  return (
    <div
      ref={listRef}
      role="tablist"
      aria-label={label}
      onKeyDown={handleKeyDown}
    >
      {children}
    </div>
  );
};

interface BaseTabProps {
  tab: string;
}

const Tab = ({
  children,
  tab,
}: PropsWithChildren<BaseTabProps>) => {
  const { selectedTab, setSelectedTab, tabsPrefix } =
    useContext(TabsContext);

  return (
    <button
      type="button"
      role="tab"
      aria-selected={selectedTab === tab}
      aria-controls={`tab-${tabsPrefix}-tabpanel-${tab}`}
      onClick={() => setSelectedTab(tab)}
      tabIndex={selectedTab === tab ? 0 : -1}
    >
      {children}
    </button>
  );
};

interface BaseTabPanelProps {
  tab: string;
}

const TabPanel = ({
  children,
  tab,
}: PropsWithChildren<BaseTabPanelProps>) => {
  const { selectedTab, tabsPrefix } =
    useContext(TabsContext);

  if (selectedTab !== tab) return null;

  return (
    <div
      role="tabpanel"
      tabIndex="0"
      id={`tab-${tabsPrefix}-tabpanel-${tab}`}
    >
      {children}
    </div>
  );
};
```

## 참고 자료

[How to create an accessible Tabs component in React?](https://amanexplains.com/how-to-create-an-accessible-tabs-component-in-react/)

[Keyboard Accessible Tabs with React](https://dev.to/eevajonnapanula/keyboard-accessible-tabs-with-react-5ch4)

[](https://github.com/reactjs/react-tabs)

[Build an accessible Tab component in React](https://jser.dev/react/2022/07/25/build-an-accessible-tab-component-in-react)