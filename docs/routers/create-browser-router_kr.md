---
title: createBrowserRouter
---

# `createBrowserRouter`

이는 모든 React Router 웹 프로젝트에 권장되는 라우터입니다. [DOM History API][historyapi]를 사용하여 URL을 업데이트하고 기록 스택을 관리합니다.

또한 [로더][loader], [엑션][action], [페처][fetcher] 등과 같은 v6.4 데이터 API를 활성화합니다.

<docs-info>데이터 API 디자인에서 가져오기와 렌더링의 분리로 인해 정적으로 정의된 경로 세트를 사용하여 React 트리 외부에 라우터를 만들어야 합니다. 이 디자인에 대한 자세한 내용은 [Remixing React Router][remixing-react-router] 블로그 게시물과 [When to Fetch][when-to-fetch] 컨퍼런스 토크를 참조하세요.</docs-info>

```tsx lines=[4,11-24]
import * as React from "react";
import * as ReactDOM from "react-dom";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";

import Root, { rootLoader } from "./routes/root";
import Team, { teamLoader } from "./routes/team";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    children: [
      {
        path: "team",
        element: <Team />,
        loader: teamLoader,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />
);
```

## 유형 선언

```tsx
function createBrowserRouter(
  routes: RouteObject[],
  opts?: {
    basename?: string;
    future?: FutureConfig;
    hydrationData?: HydrationState;
    window?: Window;
  }
): RemixRouter;
```

## `routes`

`children` 속성에 중첩된 경로가 있는 [`Route`][route] 객체의 배열입니다.

```jsx
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    children: [
      {
        path: "events/:id",
        element: <Event />,
        loader: eventLoader,
      },
    ],
  },
]);
```

## `basename`

도메인의 루트가 아닌 하위 디렉터리에 배포할 수 없는 상황에 대한 앱의 basename입니다.

```jsx
createBrowserRouter(routes, {
  basename: "/app",
});
```

루트에 연결할 때 후행 슬래시가 적용됩니다.

```jsx
createBrowserRouter(routes, {
  basename: "/app",
});
<Link to="/" />; // results in <a href="/app" />

createBrowserRouter(routes, {
  basename: "/app/",
});
<Link to="/" />; // results in <a href="/app/" />
```

## `future`

이 라우터에 대해 활성화할 [Future 플래그][api-development-strategy]의 선택적 세트입니다. 최종적으로 v7로 쉽게 마이그레이션할 수 있도록 새로 출시된 향후 플래그를 더 빨리 선택하는 것이 좋습니다.

```js
const router = createBrowserRouter(routes, {
  future: {
    // Normalize `useNavigation()`/`useFetcher()` `formMethod` to uppercase
    v7_normalizeFormMethod: true,
  },
});
```

## `window`

브라우저 개발 도구 플러그인과 같은 환경이나 전역 `window`이 아닌 다른 창을 사용하는 테스트에 유용합니다.

[loader]: ../route/loader
[action]: ../route/action
[fetcher]: ../hooks/use-fetcher
[browser-router]: ./browser-router
[form]: ../components/form
[route]: ../route/route
[routes]: ../components/routes
[historyapi]: https://developer.mozilla.org/en-US/docs/Web/API/History
[api-development-strategy]: ../guides/api-development-strategy
[remixing-react-router]: https://remix.run/blog/remixing-react-router
[when-to-fetch]: https://www.youtube.com/watch?v=95B8mnhzoCM