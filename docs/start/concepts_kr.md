---
title: 주요 개념
---

# 주요 개념

<docs-warning>이 문서는 6.4 데이터 API에 대해 업데이트되어야 합니다</docs-warning>

<docs-warning>이 문서는 React Router에서 구현된 라우팅의 핵심 개념에 대해 자세히 설명합니다. 꽤 길기 때문에 보다 실용적인 가이드를 찾고 있다면 [빠른 시작 튜토리얼][튜토리얼]을 확인하세요.</docs-warning>

React Router가 정확히 무엇을 하는지 궁금할 수도 있습니다. 앱을 구축하는 데 어떻게 도움이 되나요? **라우터**란 정확히 무엇인가요?

이러한 질문이 있거나 라우팅의 기본 부분을 자세히 알아보고 싶다면 올바른 위치에 오셨습니다. 이 문서에는 React Router에서 구현된 라우팅의 모든 핵심 개념에 대한 자세한 설명이 포함되어 있습니다.

이 문서가 당신을 압도하지 않도록 하십시오! 일상적인 사용을 위해 React Router는 매우 간단합니다. 그것을 사용하기 위해 이렇게 깊이 들어갈 필요는 없습니다.

React Router는 단순히 URL을 함수나 컴포넌트와 일치시키는 것이 아닙니다. URL에 매핑되는 전체 사용자 인터페이스를 구축하는 것이므로 익숙했던 것보다 더 많은 개념이 포함될 수 있습니다. React Router의 세 가지 주요 작업에 대해 자세히 살펴보겠습니다.

1. [히스토리 스택](#history-stack) 구독 및 조작
2. [URL](#url)을 [라우트](#route-config)와 일치시킵니다.
3. [라우트 일치](#matches)에서 중첩된 UI 렌더링

## 정의

하지만 먼저 몇 가지 정의를 알아보세요! 백엔드 및 프런트엔드 프레임워크로부터의 라우팅에 대한 다양한 아이디어가 있습니다. 때로는 한 문맥의 단어가 다른 문맥과 다른 의미를 가질 수도 있습니다.

React Router에 대해 이야기할 때 많이 사용하는 단어는 다음과 같습니다. 이 가이드의 나머지 부분에서는 각 항목에 대해 자세히 설명합니다.

- <a id="url">**URL**</a> - 주소 표시줄의 URL입니다. 많은 사람들이 "URL"과 "라우트"라는 용어를 같은 의미로 사용하지만 이는 React Router의 경로가 아니라 단지 URL일 뿐입니다.

- <a id="location">**Location**</a> - 내장 브라우저의 `window.location` 객체를 기반으로 하는 React Router 관련 객체입니다. "사용자가 어디에 있는지"를 나타냅니다. 이는 대부분 URL의 객체 표현이지만 그보다 조금 더 많은 기능을 가지고 있습니다.

- <a id="location-state">**위치 상태**</a> - [URL](#url)에 인코딩되지 않은 [location](#location)과 함께 유지되는 값입니다. 해시 또는 검색 매개변수(URL에 인코딩된 데이터)와 비슷하지만 브라우저 메모리에 보이지 않게 저장됩니다.

- <a id="history-stack">**히스토리 스택**</a> - 사용자가 탐색할 때 브라우저는 스택의 각 [위치](#location)를 추적합니다. 브라우저에서 뒤로 버튼을 길게 클릭하면 브라우저의 히스토리 스택을 바로 볼 수 있습니다.

- <a id="csr">**클라이언트 측 라우팅(CSR)**</a> - 일반 HTML 문서는 다른 문서에 연결할 수 있으며 브라우저는 [히스토리 스택](#history-stack) 자체를 처리합니다. 클라이언트 측 라우팅을 사용하면 개발자는 서버에 문서를 요청하지 않고도 브라우저 히스토리 스택을 조작할 수 있습니다.

- <a id="history">**히스토리**</a> - React Router가 [URL](#url)의 변경 사항을 구독할 수 있게 하고 프로그래밍 방식으로 브라우저 [history 스택을 조작하기 위한 API를 제공하는 객체입니다. ](#history-stack) 

- <a id="history-action">**히스토리 엑션**</a> - `POP`, `PUSH` 또는 `REPLACE` 중 하나입니다. 사용자는 다음 세 가지 이유 중 하나로 인해 [URL](#url)에 도달할 수 있습니다. 새 항목이 히스토리 스택에 추가될 때의 푸시입니다(일반적으로 링크 클릭 또는 프로그래머가 탐색을 강제한 경우). 교체는 새 항목을 푸시하는 대신 스택의 현재 항목을 바꾸는 점을 제외하면 비슷합니다. 마지막으로 사용자가 브라우저 크롬에서 뒤로 또는 앞으로 버튼을 클릭하면 팝업이 발생합니다.

- <a id="segment">**세그먼트**</a> - `/` 문자 사이의 [URL](#url) 또는 [라우트 패턴](#path-pattern) 부분입니다. 예를 들어 "/users/123"에는 두 개의 세그먼트가 있습니다.

- <a id="path-pattern">**라우트 패턴**</a> - 이는 URL과 비슷해 보이지만 **동적 세그먼트**(`"/users/)와 같이 URL을 경로와 일치시키기 위한 특수 문자를 포함할 수 있습니다. :userId"`) 또는 **별표 세그먼트**(`"/docs/*"`). 이는 URL이 아니며 React Router가 일치하는 패턴입니다.

- <a id="dynamic-segment">**동적 세그먼트**</a> - 동적 라우트 패턴의 세그먼트입니다. 즉, 세그먼트의 모든 값과 일치할 수 있습니다. 예를 들어 `/users/:userId` 패턴은 `/users/123`과 같은 URL과 일치합니다.

- <a id="url-params">**URL 매개변수**</a> - [동적 세그먼트](#dynamic-segment)와 일치하는 URL에서 구문 분석된 값입니다.

- <a id="router">**라우터**</a> - 다른 모든 컴포넌트와 후크가 작동하도록 만드는 상태 저장 최상위 컴포넌트입니다.

- <a id="route-config">**라우트 구성**</a> - 현재 위치에 대해 순위를 매기고 일치(중첩 포함)하여 분기를 생성할 **라우트 개체** 트리입니다. **라우트 일치**.

- <a id="route">**라우트**</a> - 일반적으로 `{ 라우트, 요소 }` 또는 `<라우트 라우트 요소>` 모양의 객체 또는 라우트 요소입니다. 'path'는 라우트 패턴입니다. 라우트 패턴이 현재 URL과 일치하면 요소가 렌더링됩니다.

- <a id="route-element">**라우트 요소**</a> - 또는 `<Route>`. 이 요소의 props는 `<Routes>`에 의해 [route](#route)를 생성하기 위해 읽혀지지만, 그 외에는 아무 작업도 수행하지 않습니다.

- <a id="nested-routes">**중첩 라우트**</a> - 경로는 하위 항목을 가질 수 있고 각 경로는 [세그먼트](#segment)를 통해 [URL](#url)의 일부를 정의하기 때문입니다. , 단일 URL은 트리의 중첩된 "분기"에 있는 여러 경로와 일치할 수 있습니다. 이를 통해 [outlet](#outlet), [relative link](#relative-links) 등을 통해 자동 레이아웃 중첩이 가능해집니다.

- <a id="relative-links">**상대 링크**</a> - `/`로 시작하지 않는 링크는 렌더링되는 가장 가까운 경로를 상속합니다. 이렇게 하면 전체 경로를 알고 구축할 필요 없이 더 깊은 URL에 쉽게 연결할 수 있습니다.

- <a id="match">**Match**</a> - 일치하는 [url params](#url-params) 및 라우트 이름과 같이 경로가 URL과 일치할 때 정보를 보유하는 개체입니다.

- <a id="matches">**일치**</a> - 현재 [location](#location)과 일치하는 라우트 배열(또는 [route config](#route-config)의 분기) . 이 구조는 [중첩 라우트](#nested-routes)를 활성화합니다.

- <a id="parent-route">**상위 라우트**</a> - 하위 경로가 있는 경로입니다.

- <a id="outlet">**Outlet**</a> - [matches](#match) 집합에서 다음 일치 항목을 렌더링하는 컴포넌트입니다.

- <a id="index-route">**인덱스 라우트**</a> - 상위의 [URL](#url)에 있는 상위의 [outlet](#outlet)에서 렌더링되는 경로가 없는 하위 경로입니다. .

- <a id="layout-route">**레이아웃 라우트**</a> - 경로가 없는 **상위 라우트**로, 특정 레이아웃 내의 하위 경로를 그룹화하는 데만 사용됩니다.

## 히스토리와 위치

React Router가 무엇이든 할 수 있으려면 브라우저 [history stack](#history-stack)의 변경 사항을 구독할 수 있어야 합니다.

브라우저는 사용자가 탐색할 때 자체 히스토리 스택을 유지합니다. 이것이 뒤로 및 앞으로 버튼이 작동하는 방식입니다. 전통적인 웹사이트(JavaScript가 없는 HTML 문서)에서는 사용자가 링크를 클릭하거나, 양식을 제출하거나, 뒤로 및 앞으로 버튼을 클릭할 때마다 브라우저가 서버에 요청합니다.

예를 들어 다음과 같은 사용자를 생각해 보세요.

1. `/dashboard` 링크를 클릭합니다.
2. `/accounts` 링크를 클릭합니다.
3. `/customers/123` 링크를 클릭합니다.
4. 뒤로 버튼을 클릭합니다
5. `/dashboard` 링크를 클릭합니다.

히스토리 스택은 다음과 같이 변경됩니다. 여기서 **굵게** 항목은 현재 [URL](#url)을 나타냅니다.

1. **`/dashboard`**
2. `/dashboard`, **`/accounts`**
3. `/dashboard`, `/accounts`, **`/customers/123`**
4. `/dashboard`, **`/accounts`**, `/customers/123`
5. `/dashboard`, `/accounts`, **`/dashboard`**

### 히스토리 객체

**클라이언트 측 라우팅**을 통해 개발자는 프로그래밍 방식으로 브라우저 [히스토리 스택](#history-stack)을 조작할 수 있습니다. 예를 들어, 서버에 요청하는 브라우저의 기본 동작 없이 [URL](#url)을 변경하기 위해 다음과 같은 코드를 작성할 수 있습니다.

```jsx
<a
  href="/contact"
  onClick={(event) => {
    // stop the browser from changing the URL and requesting the new document
    event.preventDefault();
    // push an entry into the browser history stack and change the URL
    window.history.pushState({}, undefined, "/contact");
  }}
/>
```

<docs-warning>설명을 위해서만 React Router에서 `window.history.pushState`를 직접 사용하지 마세요</docs-warning>

이 코드는 [URL](#url)을 변경하지만 UI에 대해서는 아무 작업도 수행하지 않습니다. UI가 연락처 페이지로 변경되도록 하려면 어딘가에서 일부 상태를 변경하는 코드를 더 작성해야 합니다. 문제는 브라우저가 "URL을 수신"하고 이와 같은 변경 사항을 구독하는 방법을 제공하지 않는다는 것입니다.

글쎄, 그것은 완전히 사실이 아닙니다. [pop](#history-actions) 이벤트를 통해 URL 변경 사항을 수신할 수 있습니다.

```jsx
window.addEventListener("popstate", () => {
  // URL changed!
});
```

하지만 이는 사용자가 뒤로 또는 앞으로 버튼을 클릭할 때만 실행됩니다. 프로그래머가 `window.history.pushState` 또는 `window.history.replaceState`를 호출한 경우에 대한 이벤트가 없습니다.

여기가 React Router 특정 `history` 객체가 작동하는 곳입니다. [기록 작업](#history-actions)이 **push**, **pop** 또는 **replace**인지 여부에 따라 "[URL](#url)을 수신"하는 방법을 제공합니다.

```js
let history = createBrowserHistory();
history.listen(({ location, action }) => {
  // this is called whenever new locations come in
  // the action is POP, PUSH, or REPLACE
});
```

앱은 자체 기록 개체를 설정할 필요가 없습니다. 이것이 `<Router>`의 작업입니다. 이러한 객체 중 하나를 설정하고, [history stack](#history-stack)의 변경 사항을 구독하고, 마지막으로 [URL](#url)이 변경되면 상태를 업데이트합니다. 이로 인해 앱이 다시 렌더링되고 올바른 UI가 표시됩니다. 상태에 넣어야 하는 유일한 것은 '위치'이며, 다른 모든 것은 해당 단일 개체에서 작동합니다.

### 위치

브라우저의 `window.location`에 위치 개체가 있습니다. [URL](#url)에 대한 정보를 알려주지만 이를 변경할 수 있는 몇 가지 방법도 있습니다.

```js
window.location.pathname; // /getting-started/concepts/
window.location.hash; // #location
window.location.reload(); // force a refresh w/ the server
// and a lot more
```

<docs-warning>설명용입니다. 일반적으로 React Router 앱에서는 `window.location`을 사용하지 않습니다</docs-warning>

`window.location`을 사용하는 대신 React Router는 `window.location`을 따라 패턴화된 [location](#location) 개념을 가지지만 훨씬 더 간단합니다. 다음과 같습니다.

```js
{
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram",
  hash: "#menu",
  state: null,
  key: "aefz24ie"
}
```

처음 세 개: `{ 라우트 이름, 검색, 해시 }`는 `window.location`과 정확히 같습니다. 3개를 더하면 사용자가 브라우저에 표시되는 [URL](#url)을 얻게 됩니다.

```js
location.pathname + location.search + location.hash;
// /bbq/pig-pickins?campaign=instagram#menu
```

마지막 두 개 `{ state, key }`는 React Router에만 적용됩니다.

**위치 경로명**

이는 원본 뒤의 [URL](#url) 부분이므로 `https://example.com/teams/hotspurs`의 경우 라우트 이름은 `/teams/hotspurs`입니다. 이는 경로가 일치하는 위치의 유일한 부분입니다.

**위치 검색**

사람들은 [URL](#url)의 이 부분에 대해 다양한 용어를 사용합니다.

- 위치 검색
- 검색 매개변수
- URL 검색 매개변수
- 쿼리 문자열

React Router에서는 이를 "위치 검색"이라고 부릅니다. 그러나 위치 검색은 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)의 직렬화된 버전입니다. 따라서 때때로 이를 "URL 검색 매개변수"라고도 부를 수 있습니다.

```js
// given a location like this:
let location = {
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram&popular=true",
  hash: "",
  state: null,
  key: "aefz24ie",
};

// we can turn the location.search into URLSearchParams
let params = new URLSearchParams(location.search);
params.get("campaign"); // "instagram"
params.get("popular"); // "true"
params.toString(); // "campaign=instagram&popular=true",
```

정확할 경우 직렬화된 문자열 버전을 "검색"으로, 구문 분석된 버전을 "검색 매개변수"로 참조하지만 정밀도가 중요하지 않은 경우 용어를 서로 바꿔서 사용하는 것이 일반적입니다.

**위치 해시**

URL의 해시는 _현재 페이지의_ 스크롤 위치를 나타냅니다. `window.history.pushState` API가 도입되기 전에 웹 개발자는 [URL](#url)의 해시 부분만을 사용하여 클라이언트 측 라우팅을 수행했으며 이는 서버에 새로운 요청을 하지 않고 조작할 수 있는 유일한 부분이었습니다. . 그러나 오늘날 우리는 그것을 설계된 목적에 맞게 사용할 수 있습니다.

**위치 상태**

`window.history.pushState()` API를 "푸시 상태"라고 부르는 이유가 궁금할 것입니다. 상태? [URL](#url)만 변경하는 것이 아닌가요? `history.push`여야 하지 않나요? 글쎄, 우리는 API가 설계되었을 때 그 방에 없었기 때문에 왜 "상태"가 초점이었는지 확신할 수 없지만 그럼에도 불구하고 그것은 브라우저의 멋진 기능입니다.

브라우저에서는 'pushState'에 값을 전달하여 탐색에 대한 정보를 유지할 수 있습니다. 사용자가 다시 클릭하면 `history.state`의 값이 이전에 "푸시"된 값으로 변경됩니다.

```js
window.history.pushState("look ma!", undefined, "/contact");
window.history.state; // "look ma!"
// user clicks back
window.history.state; // undefined
// user clicks forward
window.history.state; // "look ma!"
```

<docs-warning>설명용입니다. React Router 앱에서는 `history.state`를 직접 읽지 않습니다.</docs-warning>

React Router는 이 브라우저 기능을 활용하고 이를 약간 추상화하며 `history` 대신 `location`에 값을 표시합니다.

`location.state`는 `location.hash` 또는 `location.search`처럼 생각할 수 있습니다. 단, [URL](#url)에 값을 넣는 대신 URL의 극비 부분처럼 숨겨져 있습니다. 프로그래머는 알고 있습니다.

위치 상태에 대한 몇 가지 유용한 사용 사례는 다음과 같습니다.

- 사용자가 어디에서 왔는지 다음 페이지를 알려주고 UI를 분기합니다. 여기서 가장 널리 사용되는 구현은 사용자가 그리드 보기에서 항목을 클릭한 경우 모달에 기록을 표시하는 것입니다. 그러나 URL에 직접 표시되는 경우 자체 레이아웃(pinterest, 이전 인스타그램)에 기록을 표시합니다.
- 부분 데이터를 즉시 렌더링하고 나중에 나머지 데이터를 가져올 수 있도록 목록에서 다음 화면으로 부분 레코드를 보냅니다.

`<Link>` 또는 `navigate`의 두 가지 방법으로 위치 상태를 설정합니다.

```jsx
<Link to="/pins/123" state={{ fromDashboard: true }} />;

let navigate = useNavigate();
navigate("/users/123", { state: partialUser });
```

다음 페이지에서는 `useLocation`을 사용하여 액세스할 수 있습니다.

```jsx
let location = useLocation();
location.state;
```

<docs-info>위치 상태 값은 직렬화되므로 `new Date()`와 같은 내용이 문자열로 변환됩니다.</docs-info>

**위치 키**

각 위치에는 고유한 키가 있습니다. 이는 위치 기반 스크롤 관리, 클라이언트 측 데이터 캐싱 등과 같은 고급 사례에 유용합니다. 각각의 새로운 위치는 고유한 키를 갖기 때문에 일반 객체인 'new Map()' 또는 심지어 'locationStorage'에 정보를 저장하는 추상화를 구축할 수 있습니다.

예를 들어, 매우 기본적인 클라이언트 측 데이터 캐시는 위치 키(및 가져오기 [URL](#url))별로 값을 저장하고 사용자가 다시 클릭할 때 데이터 가져오기를 건너뛸 수 있습니다.

```jsx
let cache = new Map();

function useFakeFetch(URL) {
  let location = useLocation();
  let cacheKey = location.key + URL;
  let cached = cache.get(cacheKey);

  let [data, setData] = useState(() => {
    // initialize from the cache
    return cached || null;
  });

  let [state, setState] = useState(() => {
    // avoid the fetch if cached
    return cached ? "done" : "loading";
  });

  useEffect(() => {
    if (state === "loading") {
      let controller = new AbortController();
      fetch(URL, { signal: controller.signal })
        .then((res) => res.json())
        .then((data) => {
          if (controller.signal.aborted) return;
          // set the cache
          cache.set(cacheKey, data);
          setData(data);
        });
      return () => controller.abort();
    }
  }, [state, cacheKey]);

  useEffect(() => {
    setState("loading");
  }, [URL]);

  return data;
}
```

## 매칭

초기 렌더링에서 [히스토리 스택](#history-stack)이 변경되면 React Router는 [위치](#location)를 [route config](#route-config)와 일치시켜 세트를 만듭니다. 렌더링할 [일치](#match) 수입니다.

### 라우트 정의

라우트 구성은 다음과 같은 [라우트](#route) 트리입니다.

```js
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

`<Routes>` 컴포넌트는 `props.children`을 통해 반복되고 해당 props를 제거하고 다음과 같은 객체를 생성합니다.

```js
let routes = [
  {
    element: <App />,
    path: "/",
    children: [
      {
        index: true,
        element: <Home />,
      },
      {
        path: "teams",
        element: <Teams />,
        children: [
          {
            index: true,
            element: <LeagueStandings />,
          },
          {
            path: ":teamId",
            element: <Team />,
          },
          {
            path: ":teamId/edit",
            element: <EditTeam />,
          },
          {
            path: "new",
            element: <NewTeamForm />,
          },
        ],
      },
    ],
  },
  {
    element: <PageLayout />,
    children: [
      {
        element: <Privacy />,
        path: "/privacy",
      },
      {
        element: <Tos />,
        path: "/tos",
      },
    ],
  },
  {
    element: <Contact />,
    path: "/contact-us",
  },
];
```

실제로 `<Routes>` 대신 `useRoutes(routesGoHere)` 후크를 사용할 수 있습니다. 이것이 `<Routes>`가 하는 일의 전부입니다.

보시다시피 경로는 `:teamId/edit`와 같은 여러 [세그먼트](#segment)를 정의하거나 `:teamId`와 같은 하나만 정의할 수 있습니다. [라우트 구성](#route-config) 분기 아래의 모든 세그먼트가 함께 추가되어 경로에 대한 최종 [라우트 패턴](#path-pattern)을 생성합니다.

### 일치 매개변수

`:teamId` 세그먼트를 참고하세요. 이를 [라우트 패턴](#path-pattern)의 [동적 세그먼트](#dynamic-segment)라고 합니다. 이는 URL과 정적으로(실제 문자) 일치하지 않지만 동적으로 일치한다는 의미입니다. `:teamId`에는 어떤 값이라도 채울 수 있습니다. `/teams/123` 또는 `/teams/cupcakes` 둘 다 일치합니다. 구문 분석된 값을 [URL 매개변수](#url-params)라고 부릅니다. 따라서 이 경우 `teamId` 매개변수는 `"123"` 또는 `"cupcakes"`가 됩니다. [렌더링](#rendering) 섹션에서 앱에서 이를 사용하는 방법을 살펴보겠습니다.

### 랭킹 루트

[route config](#route-config)의 모든 분기에 대한 모든 세그먼트를 합산하면 앱이 응답하는 다음과 같은 라우트 패턴이 생성됩니다.

```js
[
  "/",
  "/teams",
  "/teams/:teamId",
  "/teams/:teamId/edit",
  "/teams/new",
  "/privacy",
  "/tos",
  "/contact-us",
];
```

이제 상황이 정말 흥미로워집니다. [URL](#url) `/teams/new`을 고려하세요. 해당 목록의 어떤 패턴이 URL과 일치합니까?

맞아요, 두 명이에요!

```
/teams/new
/teams/:teamId
```

React Router는 여기서 결정을 내려야 하며 하나만 있을 수 있습니다. 클라이언트측과 서버측 모두 많은 라우터는 정의된 순서대로 패턴을 처리합니다. 먼저 일치하는 사람이 승리합니다. 이 경우 `/`를 일치시키고 `<Home/>` 컴포넌트를 렌더링합니다. 확실히 우리가 원하는 것은 아닙니다. 이러한 종류의 라우터에서는 예상되는 결과를 얻으려면 경로를 완벽하게 정렬해야 합니다. 이것이 v6까지 React Router가 작동했던 방식이지만 이제는 훨씬 더 똑똑해졌습니다.

이러한 패턴을 보면 `/teams/new`가 URL `/teams/new`와 일치하기를 원한다는 것을 직관적으로 알 수 있습니다. 완벽하게 일치합니다! React Router도 그것을 알고 있습니다. 일치 시 세그먼트 수, 정적 세그먼트, 동적 세그먼트, 스타 패턴 등에 따라 경로의 순위를 매기고 가장 구체적인 일치 항목을 선택합니다. 라우트 주문에 대해 생각할 필요가 없습니다.

### 라우트 없는 라우트

이전에 이상한 경로를 발견했을 수도 있습니다.

```jsx
<Route index element={<Home />} />
<Route index element={<LeagueStandings />} />
<Route element={<PageLayout />} />
```

길도 없는데 어떻게 길이 될 수 있겠습니까? 여기가 React Router에서 "route"라는 단어가 꽤 느슨하게 사용되는 곳입니다. `<Home/>` 및 `<LeagueStandings/>`는 [색인 라우트](#index-route)이고 `<PageLayout/>`은 [레이아웃 라우트](#layout-route)입니다. [렌더링](#rendering) 섹션에서 어떻게 작동하는지 논의하겠습니다. 둘 다 실제로 일치하는 것과는 큰 관련이 없습니다.

### 라우트 일치

경로가 URL과 일치하면 [match](#match) 개체로 표시됩니다. `<Route path=":teamId" element={<Team/>}/>`에 대한 일치는 다음과 같습니다.

```js
{
  pathname: "/teams/firebirds",
  params: {
    teamId: "firebirds"
  },
  route: {
    element: <Team />,
    path: ":teamId"
  }
}
```

`pathname`은 이 경로와 일치하는 URL 부분을 보유합니다(우리의 경우에는 전부입니다). `params`는 일치하는 모든 [동적 세그먼트](#dynamic-segment)에서 구문 분석된 값을 보유합니다. 매개변수의 객체 키는 세그먼트 이름에 직접 매핑됩니다. `:teamId`는 `params.teamId`가 됩니다.

우리의 경로는 트리이기 때문에 단일 URL이 트리의 전체 분기와 일치할 수 있습니다. URL `/teams/firebirds`를 고려하면 다음 라우트 분기가 됩니다.

```jsx [2,4,5]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

React Router는 이러한 경로와 URL에서 [일치](#match) 배열을 생성하여 라우트 중첩과 일치하는 중첩 UI를 렌더링할 수 있습니다.

```js
[
  {
    pathname: "/",
    params: null,
    route: {
      element: <App />,
      path: "/",
    },
  },
  {
    pathname: "/teams",
    params: null,
    route: {
      element: <Teams />,
      path: "teams",
    },
  },
  {
    pathname: "/teams/firebirds",
    params: {
      teamId: "firebirds",
    },
    route: {
      element: <Team />,
      path: ":teamId",
    },
  },
];
```

## 렌더링

마지막 컨셉은 렌더링입니다. 앱 항목이 다음과 같다고 생각하세요.

```jsx
const root = ReactDOM.createRoot(
  document.getElementById("root")
);
root.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />}>
        <Route index element={<Home />} />
        <Route path="teams" element={<Teams />}>
          <Route path=":teamId" element={<Team />} />
          <Route path="new" element={<NewTeamForm />} />
          <Route index element={<LeagueStandings />} />
        </Route>
      </Route>
      <Route element={<PageLayout />}>
        <Route path="/privacy" element={<Privacy />} />
        <Route path="/tos" element={<Tos />} />
      </Route>
      <Route path="contact-us" element={<Contact />} />
    </Routes>
  </BrowserRouter>
);
```

다시 예로 `/teams/firebirds` URL을 사용해 보겠습니다. `<Routes>`는 [location](#location)을 [route config](#route-config)와 일치시키고 [matches](#match) 세트를 가져온 다음 다음과 같이 React 요소 트리를 렌더링합니다.

```jsx
<App>
  <Teams>
    <Team />
  </Teams>
</App>
```

상위 경로의 요소 내부에 렌더링된 각 일치 항목은 정말 강력한 추상화입니다. 대부분의 웹사이트와 앱은 다음과 같은 특성을 공유합니다. 즉, 상자 안의 상자, 상자 안의 상자, 각각 페이지의 하위 섹션을 변경하는 탐색 섹션이 있습니다.

### 아울렛

이 중첩된 요소 트리는 자동으로 발생하지 않습니다. `<Routes>`는 첫 번째 일치 항목의 요소를 렌더링합니다(이 경우 `<App/>`). 다음 일치 항목의 요소는 `<Teams>`입니다. 이를 렌더링하려면 'App'이 [outlet](#outlet)을 렌더링해야 합니다.

```jsx [5]
function App() {
  return (
    <div>
      <GlobalNav />
      <Outlet />
      <GlobalFooter />
    </div>
  );
}
```

'Outlet' 컴포넌트는 항상 다음 일치 항목을 렌더링합니다. 이는 `<Teams>`도 `<Team/>`을 렌더링하기 위한 콘센트가 필요하다는 것을 의미합니다.

URL이 `/contact-us`인 경우 요소 트리는 다음과 같이 변경됩니다.

```jsx
<ContactForm />
```

문의 양식이 기본 `<App>` 라우트 아래에 있지 않기 때문입니다.

URL이 `/teams/firebirds/edit`인 경우 요소 트리는 다음과 같이 변경됩니다.

```jsx
<App>
  <Teams>
    <EditTeam />
  </Teams>
</App>
```

콘센트는 일치하는 새 하위 항목으로 하위 항목을 교체하지만 상위 레이아웃은 유지됩니다. 미묘하지만 컴포넌트를 정리하는 데 매우 효과적입니다.

### 색인 라우트

`/teams`에 대한 [route config](#route-config)를 기억하세요:

```js
<Route path="teams" element={<Teams />}>
  <Route path=":teamId" element={<Team />} />
  <Route path="new" element={<NewTeamForm />} />
  <Route index element={<LeagueStandings />} />
</Route>
```

URL이 `/teams/firebirds`인 경우 요소 트리는 다음과 같습니다.

```jsx
<App>
  <Teams>
    <Team />
  </Teams>
</App>
```

그러나 URL이 `/teams`인 경우 요소 트리는 다음과 같습니다.

```jsx
<App>
  <Teams>
    <LeagueStandings />
  </Teams>
</App>
```

리그 순위? 도대체 `<Route index element={<LeagueStandings>}/>`가 거기에 어떻게 나타났나요? 심지어 경로도 없습니다! 그 이유는 [인덱스 라우트](#index-route)이기 때문입니다. 인덱스 경로는 상위 라우트 경로에 있는 상위 경로의 [outlet](#outlet)에서 렌더링됩니다.

이렇게 생각해보세요. 하위 경로의 라우트 중 하나가 아닌 경우 `<Outlet>`은 UI에 아무것도 렌더링하지 않습니다.

```jsx
<App>
  <Teams />
</App>
```

모든 팀이 왼쪽 목록에 있으면 콘센트가 비어 있다는 것은 오른쪽에 빈 페이지가 있다는 의미입니다! UI에는 공간을 채울 무언가가 필요합니다. 즉, 구조를 위한 인덱스 경로가 필요합니다.

인덱스 경로를 생각하는 또 다른 방법은 상위 경로는 일치하지만 하위 경로는 일치하지 않는 경우 기본 하위 경로라는 것입니다.

사용자 인터페이스에 따라 인덱스 경로가 필요하지 않을 수도 있지만 상위 경로에 지속적인 탐색 기능이 있는 경우 사용자가 라우트 중 하나를 클릭하지 않았을 때 공간을 채우기 위해 인덱스 경로가 필요할 가능성이 높습니다. 아직 항목이 없습니다.

### 레이아웃 라우트

아직 일치하지 않은 라우트 구성의 일부는 다음과 같습니다: `/privacy`. 일치하는 경로를 강조하여 라우트 구성을 다시 살펴보겠습니다.

```jsx [2,11,12]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

렌더링된 결과 요소 트리는 다음과 같습니다.

```jsx
<PageLayout>
  <Privacy />
</PageLayout>
```

<docs-warning>자식 라우트 요소를 렌더링하려는 레이아웃에 `<Outlet>`을 추가하는 것을 잊지 마세요. `{children}`을 사용하면 예상대로 작동하지 않습니다.</docs-warning>

`PageLayout` 경로는 확실히 이상합니다. (자식은 참여하지만) 일치에 전혀 참여하지 않기 때문에 이를 [레이아웃 라우트](#layout-route)라고 부릅니다. 이는 동일한 레이아웃에서 여러 하위 경로를 더 간단하게 래핑하기 위해서만 존재합니다. 이를 허용하지 않으면 두 가지 다른 방식으로 레이아웃을 처리해야 합니다. 때로는 경로가 자동으로 처리되고, 때로는 앱 전체에서 레이아웃 컴포넌트가 많이 반복되어 수동으로 처리되기도 합니다.

<docs-error>이렇게 할 수 있지만 레이아웃 경로를 사용하는 것이 좋습니다</docs-error>

```jsx bad lines=[14-16,22-24]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route
    path="/privacy"
    element={
      <PageLayout>
        <Privacy />
      </PageLayout>
    }
  />
  <Route
    path="/tos"
    element={
      <PageLayout>
        <Tos />
      </PageLayout>
    }
  />
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

예, 레이아웃 "라우트"의 의미는 URL 일치와 아무 관련이 없기 때문에 약간 어리석지만 허용하지 않기에는 너무 편리합니다.

## 탐색 중

[URL](#url)이 변경되면 이를 "탐색"이라고 부릅니다. React Router에서 탐색하는 방법에는 두 가지가 있습니다.

- `<링크>`
- `탐색`

### 링크

이것이 탐색의 기본 수단입니다. `<Link>`를 렌더링하면 사용자가 클릭할 때 URL을 변경할 수 있습니다. React Router는 브라우저의 기본 동작을 방지하고 [history](#history)에 새 항목을 [history 스택](#history-stack)에 푸시하도록 지시합니다. [location](#location)이 변경되고 새로운 [matches](#match)가 렌더링됩니다.

그러나 다음과 같은 경우에 링크에 액세스할 수 있습니다.

- 모든 기본 접근성 문제(예: 키보드, 포커스 가능성, SEO 등)가 충족되도록 `<a href>`를 렌더링합니다.
- "새 탭에서 열기"를 위해 마우스 오른쪽 버튼을 클릭하거나 명령/컨트롤을 클릭하는 경우 브라우저의 기본 동작을 방해하지 마세요.

[중첩 라우트](#nested-routes)는 단지 레이아웃 렌더링에 관한 것이 아닙니다. 또한 "상대 링크"도 활성화합니다. 이전의 '팀' 경로를 고려해보세요.

```jsx
<Route path="teams" element={<Teams />}>
  <Route path=":teamId" element={<Team />} />
</Route>
```

`<Teams>` 컴포넌트는 다음과 같은 링크를 렌더링할 수 있습니다.

```jsx
<Link to="psg" />
<Link to="new" />
```

연결되는 전체 경로는 `/teams/psg` 및 `/teams/new`입니다. 렌더링되는 경로를 상속합니다. 이렇게 하면 라우트 컴포넌트가 앱의 나머지 경로에 대해 실제로 알 필요가 없습니다. 매우 많은 양의 링크가 하나 더 [세그먼트](#segment) 더 깊어집니다. 전체 [라우트 구성](#route-config)을 다시 정렬할 수 있으며 이러한 링크는 여전히 잘 작동할 것입니다. 이는 처음에 사이트를 구축하고 디자인과 레이아웃이 바뀔 때 매우 유용합니다.

### 탐색 기능

이 함수는 `useNavigate` 후크에서 반환되며 프로그래머는 원할 때마다 URL을 변경할 수 있습니다. 시간 초과 시 수행할 수 있습니다.

```js
let navigate = useNavigate();
useEffect(() => {
  setTimeout(() => {
    navigate("/logout");
  }, 30000);
}, []);
```

또는 양식이 제출된 후:

```js
<form onSubmit={event => {
  event.preventDefault();
  let data = new FormData(event.target)
  let urlEncoded = new URLSearchParams(data)
  navigate("/create", { state: urlEncoded })
}}>
```

`Link`와 마찬가지로 `navigate`도 중첩된 "to" 값과 함께 작동합니다.

```js
navigate("psg");
```

`<Link>` 대신 `navigate`를 사용해야 하는 타당한 이유가 있어야 합니다. 이것은 우리를 매우 슬프게 만듭니다:

```js bad nonumber
<li onClick={() => navigate("/somewhere")} />
```

링크 및 양식을 제외하면 접근성 및 사용자 기대에 대한 복잡성을 초래하기 때문에 URL을 변경해야 하는 상호 작용은 거의 없습니다.

## 데이터 접근

마지막으로 애플리케이션은 전체 UI를 구축하기 위해 React Router에 몇 가지 정보를 요청하려고 합니다. 이를 위해 React Router에는 수많은 후크가 있습니다.

```js
let location = useLocation();
let urlParams = useParams();
let [urlSearchParams] = useSearchParams();
```

## 검토

위에서부터 모두 정리해보자!

1. 앱을 렌더링합니다.

   ```jsx
   const root = ReactDOM.createRoot(
     document.getElementById("root")
   );
   root.render(
     <BrowserRouter>
       <Routes>
         <Route path="/" element={<App />}>
           <Route index element={<Home />} />
           <Route path="teams" element={<Teams />}>
             <Route path=":teamId" element={<Team />} />
             <Route path="new" element={<NewTeamForm />} />
             <Route index element={<LeagueStandings />} />
           </Route>
         </Route>
         <Route element={<PageLayout />}>
           <Route path="/privacy" element={<Privacy />} />
           <Route path="/tos" element={<Tos />} />
         </Route>
         <Route path="contact-us" element={<Contact />} />
       </Routes>
     </BrowserRouter>
   );
   ```

2. `<BrowserRouter>`는 [history](#history)를 생성하고 초기 [location](#location)을 상태에 넣고 [URL](#url)을 구독합니다.

3. `<Routes>`는 [하위 라우트](#child-route)를 반복하여 [route config](#route-config)를 구축하고 해당 경로를 [location](#location)과 일치시키고 [ 일치](#match)를 수행하고 첫 번째 일치 항목의 라우트 요소를 렌더링합니다.

4. 각 [상위 라우트](#parent-route)에서 [`<Outlet/>`](#outlet)을 렌더링합니다.

5. 아웃렛은 [matches](#match) 경로에서 다음 일치를 렌더링합니다.

6. 사용자가 링크를 클릭합니다.

7. 링크는 `navigate()`를 호출합니다.

8. [history](#history)는 URL을 변경하고 `<BrowserRouter>`를 알립니다.

9. `<BrowserRouter>`가 다시 렌더링됩니다. (2)에서 다시 시작하세요!

그게 다야! 이 가이드가 React Router의 주요 개념을 더 깊이 이해하는 데 도움이 되기를 바랍니다.

[튜토리얼]: ./tutorial
