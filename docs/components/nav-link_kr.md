---
title: NavLink
---

# `<NavLink>`

`<NavLink>`는 "활성", "보류 중" 또는 "전환 중"인지 여부를 아는 특별한 종류의 `<Link>`입니다. 이는 다음과 같은 몇 가지 시나리오에서 유용합니다.

- 현재 선택된 항목을 표시하려는 이동 경로 또는 탭 집합과 같은 탐색 메뉴를 구축할 때
- 스크린 리더와 같은 보조 기술에 유용한 컨텍스트를 제공합니다.
- [보기 전환][view-transitions]을 더욱 세밀하게 제어할 수 있도록 "전환" 값을 제공합니다.

```tsx
import { NavLink } from "react-router-dom";

<NavLink
  to="/messages"
  className={({ isActive, isPending }) =>
    isPending ? "pending" : isActive ? "active" : ""
  }
>
  Messages
</NavLink>;
```

## 기본 `활성` 클래스

기본적으로 `active` 클래스는 활성화되면 `<NavLink>` 구성 요소에 추가되므로 CSS를 사용하여 스타일을 지정할 수 있습니다.

```tsx
<nav id="sidebar">
  <NavLink to="/messages" />
</nav>
```

```css
#sidebar a.active {
  color: red;
}
```

## `className`

`className` prop은 일반 className처럼 작동하지만, 링크의 활성 및 보류 상태에 따라 적용되는 className을 사용자 정의하는 함수를 전달할 수도 있습니다.

```tsx
<NavLink
  to="/messages"
  className={({ isActive, isPending, isTransitioning }) =>
    [
      isPending ? "pending" : "",
      isActive ? "active" : "",
      isTransitioning ? "transitioning" : "",
    ].join(" ")
  }
>
  Messages
</NavLink>
```

## `style`

`style` 소품은 일반 스타일 prop처럼 작동하지만, 링크의 활성 및 보류 상태에 따라 적용되는 스타일을 맞춤설정하는 함수를 전달할 수도 있습니다.

```tsx
<NavLink
  to="/messages"
  style={({ isActive, isPending, isTransitioning }) => {
    return {
      fontWeight: isActive ? "bold" : "",
      color: isPending ? "red" : "black",
      viewTransitionName: isTransitioning ? "slide" : "",
    };
  }}
>
  Messages
</NavLink>
```

## `children`

렌더링 prop을 자식으로 전달하여 활성 및 보류 상태를 기반으로 `<NavLink>`의 콘텐츠를 맞춤설정할 수 있습니다. 이는 내부 요소의 스타일을 변경하는 데 유용합니다.

```tsx
<NavLink to="/tasks">
  {({ isActive, isPending, isTransitioning }) => (
    <span className={isActive ? "active" : ""}>Tasks</span>
  )}
</NavLink>
```

## `end`

'end' prop은 NavLink의 'to' 경로의 '끝'에만 일치하도록 'active' 및 'pending' 상태에 대한 일치 논리를 변경합니다. URL이 'to'보다 길면 더 이상 활성 상태로 간주되지 않습니다.

| Link                          | URL          | isActive |
| ----------------------------- | ------------ | -------- |
| `<NavLink to="/tasks" />`     | `/tasks`     | true     |
| `<NavLink to="/tasks" />`     | `/tasks/123` | true     |
| `<NavLink to="/tasks" end />` | `/tasks`     | true     |
| `<NavLink to="/tasks" end />` | `/tasks/123` | false    |

**루트 경로 링크에 대한 참고 사항**

`<NavLink to="/">`는 _모든_ URL이 `/`와 일치하기 때문에 예외적인 경우입니다. 기본적으로 모든 단일 경로와 일치하는 것을 피하기 위해 'end' 소품을 효과적으로 무시하고 루트 경로에 있을 때만 일치합니다.

## `caseSensitive`

'caseSensitive' 소품을 추가하면 일치 논리가 대소문자를 구분하도록 변경됩니다.

| Link                                         | URL           | isActive |
| -------------------------------------------- | ------------- | -------- |
| `<NavLink to="/SpOnGe-bOB" />`               | `/sponge-bob` | true     |
| `<NavLink to="/SpOnGe-bOB" caseSensitive />` | `/sponge-bob` | false    |

## `aria-current`

`NavLink`가 활성화되면 기본 앵커 태그에 `<a aria-current="page">`가 자동으로 적용됩니다. MDN의 [aria-current][aria-current]를 참조하세요.

## `reloadDocument`

`reloadDocument` 속성을 사용하면 클라이언트측 라우팅을 건너뛰고 브라우저가 전환을 정상적으로 처리하도록 할 수 있습니다(마치 `<a href>`인 것처럼).

## `unstable_viewTransition`

`unstable_viewTransition` prop은 `document.startViewTransition()`에 최종 상태 업데이트를 래핑하여 이 탐색에 대한 [보기 전환][view-transitions]을 활성화합니다. 기본적으로 전환 중에 보기 전환을 맞춤설정하는 데 사용할 수 있는 `<a>` 요소에 `transitioning` 클래스가 추가됩니다.

```css
a.transitioning p {
  view-transition-name: "image-title";
}

a.transitioning img {
  view-transition-name: "image-expand";
}
```

```jsx
<NavLink to={to} unstable_viewTransition>
  <p>Image Number {idx}</p>
  <img src={src} alt={`Img ${idx}`} />
</NavLink>
```

또한 `className`/`style` 소품 또는 `children`에 전달된 렌더링 소품을 사용하여 `isTransitioning` 값을 기반으로 추가 맞춤설정할 수도 있습니다.

```jsx
<NavLink to={to} unstable_viewTransition>
  {({ isTransitioning }) => (
    <>
      <p
        style={{
          viewTransitionName: isTransitioning
            ? "image-title"
            : "",
        }}
      >
        Image Number {idx}
      </p>
      <img
        src={src}
        alt={`Img ${idx}`}
        style={{
          viewTransitionName: isTransitioning
            ? "image-expand"
            : "",
        }}
      />
    </>
  )}
</NavLink>
```

<docs-warning>
이 API는 불안정한 것으로 표시되어 있으며 주요 릴리스 없이는 주요 변경 사항이 적용될 수 있습니다.
</docs-warning>

[aria-current]: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-current
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
