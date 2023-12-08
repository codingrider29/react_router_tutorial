---
title: useNavigate
---

# `useNavigate`

<details>
  <summary>Type declaration</summary>

```tsx
declare function useNavigate(): NavigateFunction;

interface NavigateFunction {
  (to: To, options?: NavigateOptions): void;
  (delta: number): void;
}

interface NavigateOptions {
  replace?: boolean;
  state?: any;
  preventScrollReset?: boolean;
  relative?: RelativeRoutingType;
}

type RelativeRoutingType = "route" | "path";
```

</details>

<docs-warning>일반적으로 이 후크보다 [`loaders`][loaders] 및 [`actions`][actions]에서 [`redirect`][redirect]를 사용하는 것이 더 좋습니다.</docs-warning>

`useNavigate` 후크는 예를 들어 효과에서 프로그래밍 방식으로 탐색할 수 있는 함수를 반환합니다.

```tsx
import { useNavigate } from "react-router-dom";

function useLogoutTimer() {
  const userIsInactive = useFakeInactiveUser();
  const navigate = useNavigate();

  useEffect(() => {
    if (userIsInactive) {
      fake.logout();
      navigate("/session-timed-out");
    }
  }, [userIsInactive]);
}
```
`navigate` 함수에는 두 가지 시그니처가 있습니다:

- 선택적인 두 번째 `options` 인수([`<Link>`][link]에 전달할 수 있는 props와 유사)와 함께 `To` 값(`<Link to>`와 동일한 유형)을 전달하거나
- 히스토리 스택에 들어가고 싶은 델타를 전달하세요. 예를 들어 `navigate(-1)`은 뒤로 버튼을 누르는 것과 같습니다.

## `옵션.교체`

'replace: true'를 지정하면 탐색이 새 항목을 추가하는 대신 기록 스택의 현재 항목을 대체하게 됩니다.

## `옵션.상태`

[history state][history-state]에 저장하기 위해 선택적 상태 값을 포함할 수 있습니다.

## `options.preventScrollReset`

[`<ScrollRestoration>`][scrollrestoration] 구성 요소를 사용할 때 `options.preventScrollReset`을 통해 페이지 상단으로 스크롤을 재설정하는 것을 비활성화할 수 있습니다.

## `options.relative`

기본적으로 탐색은 경로 계층 구조(`relative: "route"`)를 기준으로 하므로 `..`는 한 `Route` 수준 위로 올라갑니다. 때로는 중첩하기에 적합하지 않은 일치하는 URL 패턴이 있는 경우 상대 _경로_ 라우팅을 사용하는 것이 좋습니다. `relative: "path"`를 사용하여 이 동작을 선택할 수 있습니다.

```jsx
// Contact and EditContact do not share additional UI layout
<Route path="/" element={<Layout />}>
  <Route path="contacts/:id" element={<Contact />} />
  <Route
    path="contacts/:id/edit"
    element={<EditContact />}
  />
</Route>;

function EditContact() {
  // Since Contact is not a parent of EditContact we need to go up one level
  // in the path, instead of one level in the Route hierarchy
  navigate("..", { relative: "path" });
}
```
## `options.unstable_viewTransition`

`unstable_viewTransition` 옵션은 `document.startViewTransition()`에 최종 상태 업데이트를 래핑하여 이 탐색에 대한 [보기 전환][view-transitions]을 활성화합니다. 이 뷰 전환에 특정 스타일을 적용해야 하는 경우 [`unstable_useViewTransitionState()`][use-view-transition-state]도 활용해야 합니다.

<docs-warning>`unstable_viewTransition`은 데이터 라우터를 사용할 때만 작동합니다. [라우터 선택][picking-a-router]을 참조하세요.</docs-warning>

<docs-warning>이 API는 불안정한 것으로 표시되어 있으며 주요 릴리스 없이는 주요 변경 사항이 적용될 수 있습니다.</docs-warning>

[link]: ../components/link
[redirect]: ../fetch/redirect
[loaders]: ../route/loader
[actions]: ../route/action
[history-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/state
[scrollrestoration]: ../components/scroll-restoration
[use-view-transition-state]: ../hooks//use-view-transition-state
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
[picking-a-router]: ../routers/picking-a-router
