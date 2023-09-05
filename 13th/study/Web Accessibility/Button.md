# Button Pattern

- 버튼은 `form submit` / `dialog open` / `canceling an action` / `perform a delete operation` 등과 같은 **액션 또는 이벤트를 트리거할 수 있게 해주는 위젯**
- 일반적인 버튼 위젯 외에도 WAI-ARIA에서는 2가지 다른 타입의 버튼을 지원한다.
    - **Toggle Button**
        - `OFF`(not pressed) / `ON`(pressed)라는 두 가지 상태를 갖는 버튼
        - 특정 버튼이 토글 버튼임을 보조 기술에 알리려면 `aria-pressed` 속성을 지정하면 된다.
        - `aria-pressed`를 사용할 때 주의해야 할 점은 상태가 변경될 때 토글 버튼의 레이블이 변경되지 않도록 해주어야 하는 것이다.
        - 오디오 플레이어에서 Mute 버튼이 있다고 했을 때 해당 버튼의 `aria-pressed=true`인 경우 레이블 역시 Mute로 유지되어야 스크린 리더 입장에서 “Mute toggle button pressed”의 형태로 읽어주기 때문이다.
        - 만약 디자인적으로 레이블이 Mute → Unmute로 변경되어야 한다면 `aria-pressed` 속성을 지정할 필요는 없다.
    - **Menu Button**
        - Menu Button Pattern 참고
        - `aria-haspopup` 속성이 `menu` 또는 `true`로 설정된 버튼의 경우 스크린 리더에 의해 메뉴 버튼으로 읽히게 된다.

<aside>
📌 버튼이 클릭되었을 때 수행되는 액션은 링크(Link)와는 분명히 다르므로 위젯의 모양과 역할이 제공하는 기능과 일치하도록 하는 것이 매우 중요하다. 하지만 그럼에도 불구하고 때로 시각적으로는 링크의 형태를 갖지만 버튼의 동작을 수행해야 하는 경우가 존재한다. 그러한 경우에는 `role=”button”`을 부여하면 보조 기술 사용자 입장에서 해당 요소의 기능을 이해하는 데 도움이 된다.

</aside>

## Keyboard Interaction

> 버튼에 포커스가 위치했을 때 수행되어야 하는 동작
> 
- `Space` / `Enter` → 버튼에 부여되어 있는 액션을 실행(활성화)한다.
- 이때 해당 버튼이 수행하는 액션의 타입에 따라 포커스가 설정되어야 하는 조건이 달라질 수 있다.
    - 버튼이 Dialog를 여는 동작을 수행한다면 해당 Dialog 내부로 포커스가 이동해야 한다.
    - 특정 버튼을 클릭하여 Dialog를 닫는 경우 기본적으로 포커스는 Dialog를 열도록 하였던 버튼으로 이동시켜주어야 한다.

## WAI-ARIA Roles, States, and Properties

- `<button>`은 기본적으로 `role=”button”`이 부여되어 있다.
- 버튼 내의 텍스트 콘텐츠가 접근 가능한 레이블로 간주되지만, `aria-labelledby` 또는 `aria-label`을 이용해 부여하는 것도 가능하다.
- 버튼의 기능에 대한 설명이 존재하는 경우 `aria-describedby`를 이용해 연결할 수 있다.
- 버튼이 수행해야 하는 액션이 사용 불가능한 경우에는 `aria-disabled`를 `true`로 설정해주어야 한다.
- 버튼이 토글 버튼인 경우 `aria-pressed`를 이용해 ON(true) / OFF(false) 상태를 명시해줄 수 있다.

## Example

| Element | Role | Attribute | Usage |
| --- | --- | --- | --- |
| `<div>`, `<a>` | button |  | 해당 요소들이 button 위젯으로 식별되도록 해주며 이때 접근 가능한 이름은 요소 내의 텍스트 컨텐츠로 정의된다. |
|  |  | tabindex="0" | `<div>` 요소는 기본적으로 탭 시퀀스에 포함되지 않으므로 명시적으로 tabindex를 정의해준다. `<a>` 요소의 경우에도 href 속성이 정의되어 있지 않으므로 명시적으로 설정해주어야 한다. |
| `<a>` |  | `aria-pressed="false / true"` | 해당 버튼이 토글 버튼임을 나타내며 각각 ON / OFF 상태에 맞춰 변화해야 한다. |

```tsx
const PrintActionButton = () => {
  const activateActionButton = () => {
    window.print();
  };

  const handleKeyDown: KeyboardEventHandler<HTMLDivElement> = (event) => {
    // The action button is activated by space on the keyup event, but the
    // default action for space is already triggered on keydown. It needs to be
    // prevented to stop scrolling the page before activating the button.
    if (event.key === 'Space') {
      event.preventDefault();
    }
    // If enter is pressed, activate the button
    else if (event.key === 'Enter') {
      event.preventDefault();
      activateActionButton();
    }
  };

  const handleKeyUp: KeyboardEventHandler<HTMLDivElement> = (event) => {
    // Activates the action button with the space key.
    if (event.key === 'Space') {
      event.preventDefault();
      activateActionButton();
    }
  };

  return (
    <div
      tabIndex={0}
      role="button"
      id="action"
      onClick={activateActionButton}
      onKeyDown={handleKeyDown}
      onKeyUp={handleKeyUp}
    >
      Print Page
    </div>
  );
};

const [ICON_MUTE_URL, ICON_SOUND_URL] = ['#icon-mute', '#icon-sound'];

const MuteToggleButton = () => {
  const [pressed, setPressed] = useState(false);

  const toggleButtonState = () => {
    setPressed((prevPressed) => !prevPressed);
  };

  const handleClick = () => {
    toggleButtonState();
  };

  const handleKeyDown: KeyboardEventHandler<HTMLAnchorElement> = (event) => {
    if (event.key === 'Space') {
      event.preventDefault();
    } else if (event.key === 'Enter') {
      event.preventDefault();
      toggleButtonState();
    }
  };

  const handleKeyUp: KeyboardEventHandler<HTMLAnchorElement> = (event) => {
    if (event.key === 'Space') {
      event.preventDefault();
      toggleButtonState();
    }
  };

  return (
    <a
      tabIndex={0}
      role="button"
      id="toggle"
      aria-pressed={pressed}
      onClick={handleClick}
      onKeyDown={handleKeyDown}
      onKeyUp={handleKeyUp}
    >
      Mute
      <svg
        aria-hidden="true"
        focusable="false"
      >
        <use xlinkHref={pressed ? ICON_SOUND_URL : ICON_MUTE_URL} />
      </svg>
    </a>
  );
};
```