---
title: Tutorial
---

# 튜토리얼

튜토리얼에 오신 것을 환영합니다! 우리는 귀하의 연락처를 추적할 수 있는 작지만 기능이 풍부한 앱을 구축할 것입니다. 따라가신다면 30~60m 정도 소요될 것으로 예상됩니다.

<img class="tutorial" src="/_docs/tutorial/15.webp" />

👉 **이 내용을 볼 때마다 앱에서 뭔가를 해야 한다는 의미입니다!**

나머지는 귀하의 정보와 더 깊은 이해를 위해 존재합니다. 시작해 봅시다.

## 설정

<docs-info>자신의 앱에서 따라하지 않을 경우 이 섹션을 건너뛸 수 있습니다.</docs-info>

이 튜토리얼에서는 번들러와 개발 서버에 [Vite][vite]를 사용할 것입니다. `npm` 명령줄 도구를 사용하려면 [Node.js][node]가 설치되어 있어야 합니다.

👉️ **터미널을 열고 Vite로 새로운 React 앱을 부트스트랩하세요:**

```sh
npm create vite@latest name-of-your-project -- --template react
# follow prompts
cd <your new project directory>
npm install react-router-dom localforage match-sorter sort-by
npm run dev
```

터미널에 인쇄된 URL을 방문할 수 있어야 합니다.

```
 VITE v3.0.7  ready in 175 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
```

이 튜토리얼에는 React Router에 계속 집중할 수 있도록 미리 작성된 CSS가 있습니다. 가혹하게 판단하거나 직접 작성해 보세요 . 😅 (우리는 이 튜토리얼의 마크업을 최대한 최소화하기 위해 CSS에서 일반적으로 하지 않는 작업을 수행했습니다.)

👉 **튜토리얼 CSS [여기에 있음][tutorial-css]를 `src/index.css`에 복사/붙여넣기**

이 튜토리얼에서는 데이터를 생성하고, 읽고, 검색하고, 업데이트하고, 삭제합니다. 일반적인 웹 앱은 아마도 웹 서버의 API와 통신할 것입니다. 그러나 우리는 이에 집중하기 위해 브라우저 저장소를 사용하고 일부 네트워크 대기 시간을 가짜로 만들 것입니다. 이 코드는 React Router와 관련이 없으므로 모두 복사하여 붙여넣으세요.

👉 **튜토리얼 데이터 모듈 [여기에 있음][tutorial-data]을 `src/contacts.js`에 복사/붙여넣기**

src 폴더에 필요한 것은 `contacts.js`, `main.jsx`, `index.css`뿐입니다. 다른 항목(예: `App.js` 및 `assets` 등)을 삭제할 수 있습니다.

👉 **`src/`에서 사용하지 않는 파일을 삭제하면 다음 파일만 남게 됩니다.**

```
src
├── contacts.js
├── index.css
└── main.jsx
```

앱이 실행 중이면 순간적으로 폭발할 수 있으니 계속 진행하세요 😋 . 이제 시작할 준비가 되었습니다!

## 라우터 추가

가장 먼저 해야 할 일은 [브라우저 라우터][createbrowserrouter]를 만들고 첫 번째 경로를 구성하는 것입니다. 그러면 웹 앱에 대한 클라이언트 측 라우팅이 활성화됩니다.

`main.jsx` 파일이 진입점입니다. 그것을 열면 페이지에 React Router를 넣을 것입니다.

👉 **`main.jsx`에서 [브라우저 라우터][createbrowserrouter] 생성 및 렌더링**

```jsx lines=[3-6,9-14,18] filename=src/main.jsx
import * as React from "react";
import * as ReactDOM from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";
import "./index.css";

const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello world!</div>,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

이 첫 번째 경로는 나머지 경로가 루트 라우트 내부에서 렌더링되기 때문에 종종 "루트 라우트"라고 부릅니다. 이는 UI의 루트 레이아웃 역할을 할 것이며, 더 진행하면서 중첩된 레이아웃을 갖게 될 것입니다.

## 루트 라우트

이 앱의 전역 레이아웃을 추가해 보겠습니다.

👉 **`src/routes` 및 `src/routes/root.jsx` 생성**

```sh
mkdir src/routes
touch src/routes/root.jsx
```

<small>(명령줄 전문가가 되고 싶지 않다면 해당 명령 대신 편집기를 사용하세요 🤓 )</small>

👉 **루트 레이아웃 구성요소 생성**

```jsx filename=src/routes/root.jsx
export default function Root() {
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={true}
            />
            <div
              className="sr-only"
              aria-live="polite"
            ></div>
          </form>
          <form method="post">
            <button type="submit">New</button>
          </form>
        </div>
        <nav>
          <ul>
            <li>
              <a href={`/contacts/1`}>Your Name</a>
            </li>
            <li>
              <a href={`/contacts/2`}>Your Friend</a>
            </li>
          </ul>
        </nav>
      </div>
      <div id="detail"></div>
    </>
  );
}
```

아직 React Router와 관련된 것은 없으므로 자유롭게 복사하여 붙여넣으세요.

👉 **`<Root>`를 루트 경로의 [`element`][routeelement]로 설정**

```jsx filename=src/main.jsx lines=[2,7]
/* existing imports */
import Root from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

이제 앱은 다음과 같이 보일 것입니다. CSS를 작성할 수 있는 디자이너가 있다는 것은 정말 좋은 일이겠죠? ([짐][짐] 감사합니다 🙏 ).

<img class="tutorial" loading="lazy" src="/_docs/tutorial/01.webp" />

## Not found 오류 처리

우리 모두는 새로운 앱을 구축할 때 기능보다 훨씬 더 많은 버그를 작성하기 때문에 프로젝트 초기에 앱이 오류에 어떻게 반응하는지 아는 것이 항상 좋은 생각입니다! 이런 일이 발생하면 사용자는 좋은 경험을 얻을 수 있을 뿐만 아니라 개발 중에도 도움이 됩니다.

이 앱에 몇 가지 링크를 추가했습니다. 링크를 클릭하면 어떤 일이 일어나는지 볼까요?

👉 **사이드바 이름 중 하나를 클릭하세요**

<img class="tutorial" loading="lazy" alt="기본 React Router 오류 요소의 스크린샷" src="/_docs/tutorial/02.webp" />

역겨운! 이것은 React Router의 기본 오류 화면이며, 이 앱의 루트 요소에 있는 플렉스 박스 스타일로 인해 더욱 악화되었습니다 😂 .

렌더링, 데이터 로드 또는 데이터 변형을 수행하는 동안 앱에서 오류가 발생할 때마다 React Router는 이를 포착하고 오류 화면을 렌더링합니다. 우리만의 오류 페이지를 만들어 봅시다.

👉 **오류 페이지 컴포넌트 만들기**

```sh
touch src/error-page.jsx
```

```jsx filename=src/error-page.jsx
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```

👉 **`<ErrorPage>`를 루트 경로의 [`errorElement`][errorelement]로 설정**

```jsx filename=src/main.jsx lines=[2,8]
/* previous imports */
import ErrorPage from "./error-page";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

이제 오류 페이지는 다음과 같습니다.

<img class="tutorial" loading="lazy" alt="새 오류 페이지이지만 여전히 보기 흉함" src="/_docs/tutorial/03.webp" />

<small>(글쎄, 그다지 나아지지는 않았습니다. 어쩌면 누군가 디자이너에게 오류 페이지를 만들라고 요청하는 것을 잊었을 수도 있습니다. 어쩌면 모두가 디자이너에게 오류 페이지를 만들도록 요청하는 것을 잊고 디자이너가 그것을 생각하지 못했다고 비난할 수도 있습니다 😆 ) < / 작은>

[`useRouteError`][userouteerror]는 발생한 오류를 제공합니다. 사용자가 존재하지 않는 경로로 이동하면 '찾을 수 없음' `statusText`와 함께 [오류 응답][isrouteerrorresponse]가 표시됩니다. 튜토리얼의 뒷부분에서 몇 가지 다른 오류를 확인하고 이에 대해 더 자세히 논의하겠습니다.

지금은 거의 모든 오류가 무한 스피너, 응답하지 않는 페이지 또는 빈 화면 대신 이 페이지에서 처리된다는 점을 아는 것만으로도 충분합니다 🙌

## 연락처 라우트 UI

404 "찾을 수 없음" 페이지 대신 우리가 링크한 URL에서 실제로 무언가를 렌더링하려고 합니다. 그러기 위해서는 새로운 경로를 만들어야 합니다.

👉 **연락처 라우트 모듈 생성**

```sh
touch src/routes/contact.jsx
```

👉 **연락처 컴포넌트 UI 추가**

그것은 단지 여러 요소일 뿐이므로 자유롭게 복사/붙여넣기 하세요.

```jsx filename=src/routes/contact.jsx
import { Form } from "react-router-dom";

export default function Contact() {
  const contact = {
    first: "Your",
    last: "Name",
    avatar: "https://placekitten.com/g/200/200",
    twitter: "your_handle",
    notes: "Some notes",
    favorite: true,
  };

  return (
    <div id="contact">
      <div>
        <img
          key={contact.avatar}
          src={contact.avatar || null}
        />
      </div>

      <div>
        <h1>
          {contact.first || contact.last ? (
            <>
              {contact.first} {contact.last}
            </>
          ) : (
            <i>No Name</i>
          )}{" "}
          <Favorite contact={contact} />
        </h1>

        {contact.twitter && (
          <p>
            <a
              target="_blank"
              href={`https://twitter.com/${contact.twitter}`}
            >
              {contact.twitter}
            </a>
          </p>
        )}

        {contact.notes && <p>{contact.notes}</p>}

        <div>
          <Form action="edit">
            <button type="submit">Edit</button>
          </Form>
          <Form
            method="post"
            action="destroy"
            onSubmit={(event) => {
              if (
                !confirm(
                  "Please confirm you want to delete this record."
                )
              ) {
                event.preventDefault();
              }
            }}
          >
            <button type="submit">Delete</button>
          </Form>
        </div>
      </div>
    </div>
  );
}

function Favorite({ contact }) {
  // yes, this is a `let` for later
  let favorite = contact.favorite;
  return (
    <Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </Form>
  );
}
```

이제 컴포넌트가 있으므로 이를 새 경로에 연결해 보겠습니다.

👉 **연락처 컴포넌트 가져오기 및 새 라우트 만들기**

```js filename=src/main.jsx lines=[2,10-13]
/* existing imports */
import Contact from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
]); 

/* existing code */
```

이제 링크 중 하나를 클릭하거나 `/contacts/1`을 방문하면 새로운 컴포넌트를 얻을 수 있습니다!

그러나 루트 레이아웃 내부에는 없습니다 😠

## 중첩 라우트
우리는 연락처 컴포넌트가 이와 같이 <Root> 레이아웃 _내부_ 에 렌더링되기를 원합니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/05.webp" />

접촉 경로를 루트 경로의 _하위_ 경로로 만들어 이를 수행합니다.

👉 **연락처 경로를 루트 경로의 하위 경로로 이동**

```jsx filename=src/main.jsx lines=[6-11]
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

이제 루트 레이아웃이 다시 표시되지만 오른쪽에는 빈 페이지가 표시됩니다. 하위 경로를 렌더링할 _위치_ 를 루트 경로에 알려야 합니다. [`<Outlet>`][outlet]을 통해 그 일을 하고 있어요.

`<div id="detail">`을 찾아 콘센트를 안에 넣으세요.

👉 ** [`<Outlet>`][outlet] 렌더링 **

```jsx filename=src/routes/root.jsx lines=[1,8]
import { Outlet } from "react-router-dom";

export default function Root() {
  return (
    <>
      {/* all the other elements */}
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```

## 클라이언트 측 라우팅

눈치채셨을 수도 있고 안 느끼셨을 수도 있지만, 사이드바의 링크를 클릭하면 브라우저는 React Router를 사용하는 대신 다음 URL에 대한 전체 문서 요청을 수행합니다.

서버 에서 다른 문서를 요청하지 않고도 URL을 업데이트할 수 있습니다 . 대신 앱은 새 UI를 즉시 렌더링할 수 있습니다. [`<링크>`][링크]를 사용하여 이를 구현해 보겠습니다.

👉 **사이드바 `<a href>`를 `<Link to>`으로 변경**

```jsx filename=src/routes/root.jsx lines=[1,12,15]
import { Outlet, Link } from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other elements */}

        <nav>
          <ul>
            <li>
              <Link to={`contacts/1`}>Your Name</Link>
            </li>
            <li>
              <Link to={`contacts/2`}>Your Friend</Link>
            </li>
          </ul>
        </nav>

        {/* other elements */}
      </div>
    </>
  );
}
```

브라우저 개발자 도구에서 네트워크 탭을 열어 더 이상 문서를 요청하지 않는지 확인할 수 있습니다.

## 데이터 로딩

URL 세그먼트, 레이아웃 및 데이터는 함께 결합되지 않는 경우(3배?)가 더 많습니다. 우리는 이미 이 앱에서 그것을 볼 수 있습니다:

| URL 세그먼트 | 구성요소     | 데이터 |
| ------------ | ------------ | -------|
| /            | `<Root>`     | 연락처 목록 |
| contacts/:id | `<Contact>`  | 개별연락 |

이러한 자연스러운 결합으로 인해 React Router에는 라우트 컴포넌트에 데이터를 쉽게 가져올 수 있는 데이터 규칙이 있습니다.

데이터를 로드하는 데 사용할 두 가지 API, [`loader`][loader]와 [`useLoaderData`][useloaderdata]가 있습니다. 먼저 루트 모듈에서 로더 함수를 생성하고 내보낸 다음 이를 라우트에 연결합니다. 마지막으로 데이터에 액세스하고 렌더링하겠습니다.

👉 **`root.jsx`에서 로더 내보내기**

```jsx filename=src/routes/root.jsx lines=[2,4-7]
import { Outlet, Link } from "react-router-dom";
import { getContacts } from "../contacts";

export async function loader() {
  const contacts = await getContacts();
  return { contacts };
}
```

👉 **라우터에 로더 구성**

```jsx filename=src/main.jsx lines=[2,9]
/* other imports */
import Root, { loader as rootLoader } from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

👉 **데이터 액세스 및 렌더링**

```jsx filename=src/routes/root.jsx lines=[4,11,19-40]
import {
  Outlet,
  Link,
  useLoaderData,
} from "react-router-dom";
import { getContacts } from "../contacts";

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <Link to={`contacts/${contact.id}`}>
                    {contact.first || contact.last ? (
                      <>
                        {contact.first} {contact.last}
                      </>
                    ) : (
                      <i>No Name</i>
                    )}{" "}
                    {contact.favorite && <span>★</span>}
                  </Link>
                </li>
              ))}
            </ul>
          ) : (
            <p>
              <i>No contacts</i>
            </p>
          )}
        </nav>

        {/* other code */}
      </div>
    </>
  );
}
```

그게 다야! 이제 React Router는 해당 데이터를 UI와 자동으로 동기화합니다. 아직 데이터가 없으므로 아마도 다음과 같은 빈 목록이 표시될 것입니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/06.webp" />

## 데이터 쓰기 + HTML Forms

잠시 후 첫 번째 연락처를 생성하겠습니다. 먼저 HTML에 대해 이야기해 보겠습니다.

React Router는 JavaScript Cambrian 폭발 이전의 웹 개발에 따라 HTML Forms 탐색을 데이터 변형 기본 요소로 에뮬레이트합니다. "구식" 웹 모델의 단순성과 함께 클라이언트 렌더링 앱의 UX 기능을 제공합니다.

일부 웹 개발자에게는 익숙하지 않은 HTML Form은 실제로 링크를 클릭하는 것처럼 브라우저에서 탐색을 유발합니다. 유일한 차이점은 요청에 있습니다. 링크는 URL만 변경할 수 있는 반면 forms는 요청 방법(GET 대 POST)과 요청 본문(POST form 데이터)도 변경할 수 있습니다.

클라이언트 측 라우팅이 없으면 브라우저는 양식의 데이터를 자동으로 직렬화하여 POST의 경우 요청 본문으로, GET의 경우 URLSearchParams로 서버에 보냅니다. React Router는 서버에 요청을 보내는 대신 클라이언트 측 라우팅을 사용하여 이를 라우팅 작업으로 보내는 점을 제외하면 동일한 작업을 수행합니다.

앱에서 "새로 만들기" 버튼을 클릭하여 이를 테스트할 수 있습니다. Vite 서버가 POST 요청을 처리하도록 구성되지 않았기 때문에 앱이 폭발해야 합니다(아마도 405 🤷여야 하지만 404를 보냅니다).

<img class="tutorial" loading="lazy" src="/_docs/tutorial/07.webp" />

새 연락처를 생성하기 위해 해당 POST를 Vite 서버로 보내는 대신 클라이언트 측 라우팅을 사용해 보겠습니다.

## 연락처 만들기

루트 경로에서 `action`을 내보내고 이를 라우트 구성에 연결한 다음 `<form>`을 React Router [`<Form>`][form]으로 변경하여 새 연락처를 만듭니다.

👉 **액션을 생성하고 `<form>`을 `<Form>`으로 변경합니다**

```jsx filename=src/routes/root.jsx lines=[5,7,9-12,24-26]
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return { contact };
}

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          {/* other code */}
          <Form method="post">
            <button type="submit">New</button>
          </Form>
        </div>

        {/* other code */}
      </div>
    </>
  );
}
```

👉 **라우터에서 엑션 가져오기 및 설정**

```jsx filename=src/main.jsx lines=[5,14]
/* other imports */

import Root, {
  loader as rootLoader,
  action as rootAction,
} from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

그게 다야! 계속해서 "새로 만들기" 버튼을 클릭하면 목록에 새 레코드가 나타나는 것을 볼 수 있습니다 🥳

<img class="tutorial" loading="lazy" src="/_docs/tutorial/08.webp" />

`createContact` 메소드는 이름이나 데이터 등이 없는 빈 연락처를 생성합니다. 하지만 그래도 기록은 남습니다. 약속해요!

> 🧐 잠깐... 사이드바는 어떻게 업데이트됐나요? '액션'을 어디에서 호출했나요? 데이터를 다시 가져오는 코드는 어디에 있나요? 'useState', 'onSubmit' 및 'useEffect'는 어디에 있습니까?!

이것이 "구식 웹" 프로그래밍 모델이 나타나는 곳입니다. 앞서 논의한 것처럼 [`<Form>`][form]은 브라우저가 서버에 요청을 보내는 것을 방지하고 대신 `action` 경로로 보냅니다. 웹 의미론에서 POST는 일반적으로 일부 데이터가 변경되고 있음을 의미합니다. 관례적으로 React Router는 이를 힌트로 사용하여 작업이 완료된 후 페이지의 데이터를 자동으로 재검증합니다. 이는 모든 `useLoaderData` 후크가 업데이트되고 UI가 자동으로 데이터와 동기화 상태를 유지한다는 의미입니다! 정말 멋진.

## 로더의 URL 매개변수

👉 **이름 없음 기록을 클릭하세요**

한 가지 차이점만 제외하면 이전 정적 연락처 페이지가 다시 표시됩니다. 이제 URL에 레코드에 대한 실제 ID가 있습니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/09.webp" />

라우트 구성을 검토하면 경로는 다음과 같습니다.

```jsx
[
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
];
```

`:contactId` URL 세그먼트를 참고하세요. 콜론(`:`)은 "동적 세그먼트"로 변환하는 특별한 의미를 갖습니다. 동적 세그먼트는 연락처 ID와 같은 URL의 해당 위치에 있는 동적(변경) 값과 일치합니다. 우리는 URL에서 이러한 값을 "URL 매개변수"라고 부르거나 줄여서 "매개변수"라고 부릅니다.

이러한 [`params`][params]는 동적 세그먼트와 일치하는 키와 함께 로더에 전달됩니다. 예를 들어 세그먼트 이름은 `:contactId`이므로 값은 `params.contactId`로 전달됩니다.

이러한 매개변수는 ID로 레코드를 찾는 데 가장 자주 사용됩니다. 한번 시험해 봅시다.

👉 **연락처 페이지에 로더를 추가하고 `useLoaderData`를 사용하여 데이터에 액세스하세요**

```jsx filename=src/routes/contact.jsx lines=[1,2,4-6,10]
import { Form, useLoaderData } from "react-router-dom";
import { getContact } from "../contacts";

export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  return { contact };
}

export default function Contact() {
  const { contact } = useLoaderData();
  // existing code
}
```

👉 **경로에 로더 구성**

```jsx filename=src/main.jsx lines=[3,17]
/* existing code */
import Contact, {
  loader as contactLoader,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/10.webp" />

## 데이터 업데이트

데이터 생성과 마찬가지로 [`<Form>`][form]을 사용하여 데이터를 업데이트합니다. `contacts/:contactId/edit`에 새로운 경로를 만들어 보겠습니다. 다시 한번 컴포넌트부터 시작한 다음 이를 라우트 구성에 연결하겠습니다.

👉 **편집 구성요소 생성**

```
touch src/routes/edit.jsx
```

👉 **편집 페이지 UI 추가**

이전에 본 적이 없는 내용은 없습니다. 자유롭게 복사/붙여넣으세요.

```jsx filename=src/routes/edit.jsx
import { Form, useLoaderData } from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();

  return (
    <Form method="post" id="contact-form">
      <p>
        <span>Name</span>
        <input
          placeholder="First"
          aria-label="First name"
          type="text"
          name="first"
          defaultValue={contact.first}
        />
        <input
          placeholder="Last"
          aria-label="Last name"
          type="text"
          name="last"
          defaultValue={contact.last}
        />
      </p>
      <label>
        <span>Twitter</span>
        <input
          type="text"
          name="twitter"
          placeholder="@jack"
          defaultValue={contact.twitter}
        />
      </label>
      <label>
        <span>Avatar URL</span>
        <input
          placeholder="https://example.com/avatar.jpg"
          aria-label="Avatar URL"
          type="text"
          name="avatar"
          defaultValue={contact.avatar}
        />
      </label>
      <label>
        <span>Notes</span>
        <textarea
          name="notes"
          defaultValue={contact.notes}
          rows={6}
        />
      </label>
      <p>
        <button type="submit">Save</button>
        <button type="button">Cancel</button>
      </p>
    </Form>
  );
}
```

👉 **새 편집 라우트 추가**

```jsx filename=src/main.jsx lines=[2,17-21]
/* existing code */
import EditContact from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```

루트 경로의 아웃렛에서 렌더링되기를 원하므로 이를 기존 하위 경로의 형제로 만들었습니다.

(우리가 이 경로에 `contactLoader`를 재사용했다는 것을 알 수 있습니다. 이는 튜토리얼에서 게으르기 때문입니다. 라우트 간에 로더를 공유하려고 시도할 이유가 없으며 일반적으로 자체 로더가 있습니다.)

좋습니다. "편집" 버튼을 클릭하면 다음과 같은 새로운 UI가 제공됩니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/11.webp" />

## FormData로 연락처 업데이트

방금 만든 편집 경로는 이미 양식을 렌더링합니다. 레코드를 업데이트하기 위해 해야 할 일은 경로에 작업을 연결하는 것뿐입니다. 양식이 작업에 게시되고 데이터가 자동으로 재검증됩니다.

👉 **editcont 모듈에 작업 추가**

```jsx filename=src/routes/edit.jsx lines=[4,6,8-13]
import {
  Form,
  useLoaderData,
  redirect,
} from "react-router-dom";
import { updateContact } from "../contacts";

export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}

/* existing code */
```

👉 **경로까지 작업 연결**

```jsx filename=src/main.jsx lines=[3,23]
/* existing code */
import EditContact, {
  action as editAction,
} from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
        action: editAction,
      },
    ],
  },
]);

/* existing code */
```

양식을 작성하고 저장을 누르면 다음과 같은 내용이 표시됩니다! <small>(눈이 편하고 털이 덜 난다는 점만 빼고요.)</small>

<img class="tutorial" loading="lazy" src="/_docs/tutorial/12.webp" />

## 변형 토론

> 😑 성공했지만 여기서 무슨 일이 일어나고 있는지 전혀 모르겠습니다 ...

좀 파헤쳐보자...

`src/routes/edit.jsx`를 열고 양식 요소를 살펴보세요. 각각의 이름이 어떻게 지정되어 있는지 확인하세요.

```jsx lines=[5] filename=src/routes/edit.jsx
<input
  placeholder="First"
  aria-label="First name"
  type="text"
  name="first"
  defaultValue={contact.first}
/>
```

JavaScript가 없으면 양식이 제출되면 브라우저는 [`FormData`][formdata]를 생성하고 이를 서버에 보낼 때 요청 본문으로 설정합니다. 앞서 언급했듯이 React Router는 이를 방지하고 대신 [`FormData`][formdata]를 포함하여 작업에 요청을 보냅니다.

양식의 각 필드는 `formData.get(name)`을 사용하여 액세스할 수 있습니다. 예를 들어, 위의 입력 필드가 있으면 다음과 같이 성과 이름에 액세스할 수 있습니다.

```jsx lines=[3,4]
export async function action({ request, params }) {
  const formData = await request.formData();
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
}
```

우리는 몇개의 양식 필드를 가지고 있기 때문에 [`Object.fromEntries`][fromentries]를 사용하여 그것들을 모두 객체로 수집했습니다. 이것이 바로 `updateContact` 함수가 원하는 것입니다.

```jsx lines=[2,3]
const updates = Object.fromEntries(formData);
updates.first; // "Some"
updates.last; // "Name"
```

`action` 외에, 우리가 논의하고 있는 API 중 어느 것도 React Router에서 제공되지 않습니다: [`request`][request], [`request.formData`][requestformdata], [`Object.fromEntries`][fromentries] 모두 웹 플랫폼에서 제공됩니다.

작업을 마친 후 끝에 있는 [`redirect`][redirect]를 확인하세요.

```jsx filename=src/routes/edit.jsx lines=[5]
export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}
```

로더와 액션은 둘 다 [`응답`을 반환][returningresponses]할 수 있습니다([`요청`][request]을 수신했기 때문에 의미가 있습니다!). [`redirect`][redirect] 도우미를 사용하면 앱에 위치를 변경하라고 지시하는 [응답][response]을 더 쉽게 반환할 수 있습니다.

클라이언트 측 라우팅이 없으면 POST 요청 후 서버가 리디렉션되면 새 페이지가 최신 데이터를 가져와 렌더링합니다. 이전에 배운 것처럼 React Router는 이 모델을 에뮬레이션하고 작업 후 페이지의 데이터를 자동으로 다시 검증합니다. 이것이 양식을 저장할 때 사이드바가 자동으로 업데이트되는 이유입니다. 추가 재검증 코드는 클라이언트 측 라우팅 없이는 존재하지 않으므로 클라이언트 측 라우팅에도 존재할 필요가 없습니다!

## 새 레코드를 편집 페이지로 리디렉션

이제 리디렉션 방법을 알았으므로 새 연락처를 생성하여 편집 페이지로 리디렉션하는 작업을 업데이트해 보겠습니다.

👉 **새 레코드의 편집 페이지로 리디렉션**

```jsx filename=src/routes/root.jsx lines=[6,12]
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return redirect(`/contacts/${contact.id}/edit`);
}
```

이제 "새로 만들기"를 클릭하면 편집 페이지가 표시됩니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/13.webp" />

👉 **몇 가지 기록 추가**

첫 번째 리믹스 컨퍼런스의 뛰어난 스피커 라인업을 활용해보겠습니다 😁

<img class="tutorial" loading="lazy" src="/_docs/tutorial/14.webp" />

## 액티브 링크 스타일링

이제 많은 레코드가 있으므로 사이드바에서 어떤 레코드를 보고 있는지 명확하지 않습니다. 이 문제를 해결하려면 [`NavLink`][navlink]를 사용할 수 있습니다.

👉 **사이드바에서 'NavLink' 사용**

```jsx filename=src/routes/root.jsx lines=[3,20-31]
import {
  Outlet,
  NavLink,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <NavLink
                    to={`contacts/${contact.id}`}
                    className={({ isActive, isPending }) =>
                      isActive
                        ? "active"
                        : isPending
                        ? "pending"
                        : ""
                    }
                  >
                    {/* other code */}
                  </NavLink>
                </li>
              ))}
            </ul>
          ) : (
            <p>{/* other code */}</p>
          )}
        </nav>
      </div>
    </>
  );
}
```

`className`에 함수를 전달하고 있다는 점에 유의하세요. 사용자가 `NavLink`의 URL에 있으면 `isActive`가 true가 됩니다. 활성화될 _대략_ 시간(데이터가 아직 로드 중)이면 'isPending'이 true가 됩니다. 이를 통해 사용자의 위치를 쉽게 표시할 수 있을 뿐만 아니라 클릭했지만 여전히 데이터 로드를 기다리고 있는 링크에 대한 즉각적인 피드백을 제공할 수 있습니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/15.webp"/>

## 글로벌 보류 UI

사용자가 앱을 탐색할 때 React Router는 다음 페이지에 대한 데이터가 로드되는 동안 _이전 페이지를 그대로 둡니다_. 목록 사이를 클릭할 때 앱이 약간 응답하지 않는 느낌을 받았을 수 있습니다. 앱이 응답하지 않는 느낌이 들지 않도록 사용자에게 몇 가지 피드백을 제공하겠습니다.

React Router는 이면의 모든 상태를 관리하고 동적 웹 앱을 구축하는 데 필요한 부분을 보여줍니다. 이 경우에는 [`useNavigation`][usenavigation] 후크를 사용합니다.

👉 **`useNavigation`을 사용하여 글로벌 보류 UI 추가**

```jsx filename=src/routes/root.jsx lines=[3,10,17-19]
import {
  // existing code
  useNavigation,
} from "react-router-dom";

// existing code

export default function Root() {
  const { contacts } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">{/* existing code */}</div>
      <div
        id="detail"
        className={
          navigation.state === "loading" ? "loading" : ""
        }
      >
        <Outlet />
      </div>
    </>
  );
}
```

[`useNavigation`][usenavigation]은 현재 탐색 상태를 반환합니다. `"idle" | "submitting" | "loading"`.

우리의 경우 유휴 상태가 아닌 경우 앱의 주요 부분에 `"loading"` 클래스를 추가합니다. 그런 다음 CSS는 짧은 지연 후에 멋진 페이드를 추가합니다(빠른 로드를 위해 UI가 깜박이는 것을 방지하기 위해). 상단에 스피너나 로딩 바를 표시하는 등 원하는 것은 무엇이든 할 수 있습니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/16.webp" />

우리의 데이터 모델(`src/contacts.js`)에는 클라이언트 측 캐시가 있으므로 두 번째로 동일한 연락처로 이동하는 것이 더 빠릅니다. 이 동작은 React Router가 _아닙니다_, 이전에 가본 적이 있든 없든 경로를 변경하기 위해 데이터를 다시 로드합니다. 그러나 탐색 중에 (목록과 같은) _변경되지 않는_ 경로에 대한 로더 호출은 피합니다.

## 기록 삭제

연락처 경로의 코드를 검토하면 삭제 버튼이 다음과 같은 것을 볼 수 있습니다.

```jsx filename=src/routes/contact.jsx lines=[3]
<Form
  method="post"
  action="destroy"
  onSubmit={(event) => {
    if (
      !confirm(
        "Please confirm you want to delete this record."
      )
    ) {
      event.preventDefault();
    }
  }}
>
  <button type="submit">Delete</button>
</Form>
```

'action'은 "destroy"를 가리킵니다. `<Link to>`과 마찬가지로 `<Form action>`은 _상대적_ 값을 사용할 수 있습니다. 양식은 `contact/:contactId`에서 렌더링되므로 `destroy`를 사용한 상대 작업은 클릭 시 양식을 `contact/:contactId/destroy`에 제출합니다.

이 시점에서는 삭제 버튼을 작동시키기 위해 알아야 할 모든 것을 알아야 합니다. 계속 진행하기 전에 한번 시도해 볼까요? 너는 필요할거야:

1. 새로운 라우트
2. 해당 경로에서의 `action`
3. `src/contacts.js`에서 `deleteContact`

👉 **"destroy" 라우트 모듈 생성**

```
touch src/routes/destroy.jsx
```

👉 **destroy action 추가**

```jsx filename=src/routes/destroy.jsx
import { redirect } from "react-router-dom";
import { deleteContact } from "../contacts";

export async function action({ params }) {
  await deleteContact(params.contactId);
  return redirect("/");
}
```

👉 **라우트 구성에 destroy route 추가**

```jsx filename=src/main.jsx lines=[2,10-13]
/* existing code */
import { action as destroyAction } from "./routes/destroy";

const router = createBrowserRouter([
  {
    path: "/",
    /* existing root route props */
    children: [
      /* existing routes */
      {
        path: "contacts/:contactId/destroy",
        action: destroyAction,
      },
    ],
  },
]);

/* existing code */
```

좋습니다. 레코드로 이동하여 '삭제' 버튼을 클릭하세요. 효과가있다!

> 😅 이 모든 것이 왜 작동하는지 여전히 혼란스럽습니다.

사용자가 제출 버튼을 클릭하면:

1. `<Form>`은 새 POST 요청을 서버에 보내는 기본 브라우저 동작을 방지하지만 대신 클라이언트 측 라우팅으로 POST 요청을 생성하여 브라우저를 에뮬레이트합니다.
2. `<Form action="destroy">`는 `"contacts/:contactId/destroy"`의 새 경로와 일치하고 요청을 보냅니다.
3. 작업이 리디렉션된 후 React Router는 최신 값을 얻기 위해 페이지의 데이터에 대한 모든 로더를 호출합니다(이것이 "재검증"입니다). `useLoaderData`는 새 값을 반환하고 컴포넌트를 업데이트합니다!

양식을 추가하고 작업을 추가하면 React Router가 나머지 작업을 수행합니다.

## 상황별 오류

재미삼아, destroy 액션에 오류를 던져보세요:

```jsx filename=src/routes/destroy.jsx lines=[2]
export async function action({ params }) {
  throw new Error("oh dang!");
  await deleteContact(params.contactId);
  return redirect("/");
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/17.webp" />

그 화면을 인식하시나요? 이전의 [`errorElement`][errorelement]입니다. 그러나 사용자는 새로 고침을 누르는 것 외에는 이 화면에서 복구하기 위해 실제로 아무것도 할 수 없습니다.

삭제 경로에 대한 상황별 오류 메시지를 만들어 보겠습니다.

```jsx filename=src/main.jsx lines=[6]
[
  /* other routes */
  {
    path: "contacts/:contactId/destroy",
    action: destroyAction,
    errorElement: <div>Oops! There was an error.</div>,
  },
];
```

이제 다시 시도해 보세요.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/18.webp" />

이제 사용자에게는 새로 고침을 하는 것보다 더 많은 옵션이 있으며, 문제가 없는 페이지 부분과 계속 상호 작용할 수 있습니다 🙌

삭제 경로에는 자체 `errorElement`가 있고 루트 경로의 하위 경로이므로 오류는 루트 대신 거기에서 렌더링됩니다. 아마도 눈치채셨겠지만 이러한 오류는 가장 가까운 `errorElement`까지 나타납니다. 루트에 하나가 있는 한 원하는만큼 많이 또는 적게 추가하십시오.

## 인덱스 라우트

앱을 로드하면 목록 오른쪽에 큰 빈 페이지가 표시됩니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/19.webp" />

경로에 하위 항목이 있고 상위 경로의 경로에 있는 경우 일치하는 하위 항목이 없기 때문에 `<Outlet>`에는 렌더링할 항목이 없습니다. 인덱스 경로를 해당 공간을 채우는 기본 하위 경로로 생각할 수 있습니다.

👉 **인덱스 라우트 모듈 생성**

```
touch src/routes/index.jsx
```

👉 **인덱스 컴포넌트의 요소 채우기**

붙여넣기를 자유롭게 복사하세요. 여기에는 특별한 것이 없습니다.

```jsx filename=src/routes/index.jsx
export default function Index() {
  return (
    <p id="zero-state">
      This is a demo for React Router.
      <br />
      Check out{" "}
      <a href="https://reactrouter.com">
        the docs at reactrouter.com
      </a>
      .
    </p>
  );
}
```

👉 **인덱스 라우트 구성**

```jsx filename=src/main.jsx lines=[2,12]
// existing code
import Index from "./routes/index";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      /* existing routes */
    ],
  },
]);
```

[`{ path: "" }`][path] 대신 [`{ index:true }`][index]를 참고하세요. 이는 사용자가 상위 경로의 정확한 경로에 있을 때 라우터가 이 경로를 일치시키고 렌더링하도록 지시하므로 `<Outlet>`에 렌더링할 다른 하위 경로가 없습니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/20.webp" />

짜잔! 더 이상 공백이 없습니다. 대시보드, 통계, 피드 등을 인덱스 경로에 배치하는 것이 일반적입니다. 데이터 로딩에도 참여할 수 있습니다.

## 취소 버튼

편집 페이지에는 아직 아무것도 하지 않는 취소 버튼이 있습니다. 우리는 이것이 브라우저의 뒤로 버튼과 같은 일을 하길 원합니다.

버튼에 대한 클릭 핸들러와 React Router의 [`useNavigate`][usenavigate]가 필요합니다.

👉 **`useNavigate`를 사용하여 취소 버튼 클릭 핸들러를 추가하세요**

```jsx filename=src/routes/edit.jsx lines=[5,10,20-22]
import {
  Form,
  useLoaderData,
  redirect,
  useNavigate,
} from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();
  const navigate = useNavigate();

  return (
    <Form method="post" id="contact-form">
      {/* existing code */}

      <p>
        <button type="submit">Save</button>
        <button
          type="button"
          onClick={() => {
            navigate(-1);
          }}
        >
          Cancel
        </button>
      </p>
    </Form>
  );
}
```

이제 사용자가 "취소"를 클릭하면 브라우저 기록에 하나의 항목이 다시 전송됩니다.

> 🧐 왜 버튼에 `event.preventDefault`가 없나요?

`<button type="button">`은 중복되는 것처럼 보이지만 버튼이 양식을 제출하는 것을 방지하는 HTML 방식입니다.

두 가지 기능이 더 추가되었습니다. 우리는 집으로 스트레칭 중입니다!

## URL 검색 매개변수 및 GET 제출

지금까지의 모든 대화형 UI는 URL을 변경하는 링크이거나 데이터를 작업에 게시하는 양식이었습니다. 검색 필드는 두 가지가 혼합되어 있다는 점에서 흥미롭습니다. 양식이지만 URL만 변경하고 데이터는 변경하지 않습니다.

지금은 React Router `<Form>`이 아닌 일반 HTML `<form>`입니다. 브라우저가 기본적으로 무엇을 하는지 살펴보겠습니다.

👉 **검색창에 이름을 입력하고 Enter 키를 누르세요**

이제 브라우저의 URL에는 [URLSearchParams][urlsearchparams]와 같은 URL의 쿼리가 포함되어 있습니다.

```
http://127.0.0.1:5173/?q=ryan
```

검색 양식을 검토하면 다음과 같습니다.

```jsx filename=src/routes/root.jsx lines=[1,7]
<form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</form>
```

이전에 살펴본 것처럼 브라우저는 입력 요소의 `name` 속성으로 양식을 직렬화할 수 있습니다. 이 입력의 이름은 `q`이므로 URL에 `?q=`가 있습니다. 이름을 'search'로 지정하면 URL은 '?search='가 됩니다.

이 양식은 우리가 사용한 다른 양식과 다르며 `<form method="post">`가 없습니다. 기본 `method`는 `"get"`입니다. 즉, 브라우저가 다음 문서에 대한 요청을 생성할 때 양식 데이터를 요청 POST 본문에 넣지 않고 GET 요청의 [`URLSearchParams`][urlsearchparams]에 넣습니다.

## 클라이언트 측 라우팅을 통한 제출 GET

클라이언트측 라우팅을 사용하여 이 양식을 제출하고 기존 로더에서 목록을 필터링해 보겠습니다.

👉 **`<양식>`을 `<양식>`으로 변경**

```jsx filename=src/routes/root.jsx lines=[1,11]
<Form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</Form>
```

👉 **URLSearchParams가 있는 경우 목록 필터링**

```jsx filename=src/routes/root.jsx lines=[1,2-4]
export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts };
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/21.webp" />

이는 POST가 아닌 GET이기 때문에 React Router는 `action`을 호출하지 _않습니다_. GET 양식을 제출하는 것은 링크를 클릭하는 것과 같습니다. URL만 변경됩니다. 그렇기 때문에 필터링을 위해 추가한 코드는 이 경로의 `action`이 아닌 `loader`에 있습니다.

이는 또한 일반적인 페이지 탐색임을 의미합니다. 뒤로 버튼을 클릭하면 이전 위치로 돌아갈 수 있습니다.

## 양식 상태에 URL 동기화

여기에는 신속하게 처리할 수 있는 몇 가지 UX 문제가 있습니다.

1. 검색 후 뒤로를 클릭하면 목록이 더 이상 필터링되지 않더라도 양식 필드에는 입력한 값이 여전히 남아 있습니다.
2. 검색 후 페이지를 새로 고치면 목록이 필터링되어도 양식 필드에 더 이상 값이 없습니다.

즉, URL과 양식 상태가 동기화되지 않았습니다.

👉 **로더에서 `q`를 반환하고 검색 필드 기본값으로 설정**

```jsx filename=src/routes/root.jsx lines=[7,11,26]
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts, q };
}

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

그러면 문제 (2)가 해결됩니다. 지금 페이지를 새로 고치면 입력 필드에 쿼리가 표시됩니다.

<img class="tutorial" loading="lazy" src="/_docs/tutorial/21.webp" />

이제 문제 (1)의 경우 뒤로 버튼을 클릭하고 입력을 업데이트합니다. React에서 `useEffect`를 가져와 DOM의 양식 상태를 직접 조작할 수 있습니다.

👉 **입력값을 URL 검색 매개변수와 동기화**

```jsx filename=src/routes/root.jsx lines=[1,9-11]
import { useEffect } from "react";

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  // existing code
}
```

> 🤔 이를 위해 제어 컴포넌트와 React State를 사용해야 하지 않나요?

확실히 제어되는 컴포넌트로 이 작업을 수행할 수 있지만 동일한 동작에 대해 더 복잡해지게 됩니다. URL을 제어하는 것은 사용자가 뒤로/앞으로 버튼을 사용하는 것이 아닙니다. 제어되는 컴포넌트에는 더 많은 동기화 지점이 있습니다.

<상세>
<summary>그래도 걱정된다면 이 페이지를 펼쳐서 어떤 모습인지 확인하세요.</summary>

입력을 제어하려면 이제 하나가 아닌 세 가지 동기화 지점이 필요합니다. 동작은 동일하지만 코드가 더 복잡합니다.

```jsx filename=src/routes/root.jsx lines=[1,6,15,18-20,34-37]
import { useEffect, useState } from "react";
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q") || "";
  const contacts = await getContacts(q);
  return { contacts, q };
}

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const [query, setQuery] = useState(q);
  const navigation = useNavigation();

  useEffect(() => {
    setQuery(q);
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              value={query}
              onChange={(e) => {
                setQuery(e.target.value);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
    </>
  );
}
```

</세부사항>

## 'onChange' 양식 제출

여기서 제품 결정을 내려야 합니다. 이 UI의 경우 양식을 명시적으로 제출할 때보다는 모든 키 입력에서 필터링이 수행되도록 하는 것이 좋습니다.

우리는 이미 `useNavigate`를 보았습니다. 이를 위해 사촌인 [`useSubmit`][usesubmit]를 사용하겠습니다.

```jsx filename=src/routes/root.jsx lines=[4,10,25-27]
// existing code
import {
  // existing code
  useSubmit,
} from "react-router-dom";

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
              onChange={(event) => {
                submit(event.currentTarget.form);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

이제 입력하면 양식이 자동으로 제출됩니다!

[`submit`][usesubmit]에 대한 인수를 참고하세요. 'event.currentTarget.form'을 전달하고 있습니다. `currentTarget`은 이벤트가 연결된 DOM 노드이고 `currentTarget.form`은 입력의 상위 양식 노드입니다. `제출` 기능은 전달된 모든 양식을 직렬화하고 제출합니다.

## 검색 스피너 추가

프로덕션 앱에서 이 검색은 한 번에 모두 보내고 클라이언트 측을 필터링하기에는 너무 큰 데이터베이스의 레코드를 찾을 가능성이 높습니다. 이것이 바로 이 데모에 가짜 네트워크 대기 시간이 있는 이유입니다.

로딩 표시가 없으면 검색이 다소 느린 느낌이 듭니다. 데이터베이스를 더 빠르게 만들 수 있더라도 항상 사용자의 네트워크 대기 시간이 방해가 되고 통제할 수 없게 됩니다. 더 나은 UX를 위해 검색에 대한 즉각적인 UI 피드백을 추가해 보겠습니다. 이를 위해 [`useNavigation`][usenavigation]을 다시 사용하겠습니다.

👉 **검색 스피너 추가**

```jsx filename=src/routes/root.jsx lines=[8-12,26,32]
// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  const searching =
    navigation.location &&
    new URLSearchParams(navigation.location.search).has(
      "q"
    );

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              className={searching ? "loading" : ""}
              // existing code
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={!searching}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/22.webp" />

앱이 새 URL로 이동하고 이에 대한 데이터를 로드할 때 `navigation.location`이 표시됩니다. 그런 다음 더 이상 보류 중인 탐색이 없으면 사라집니다.

## 히스토리 스택 관리

이제 모든 키 입력에 대해 양식이 제출되므로 "seba" 문자를 입력한 다음 백스페이스로 삭제하면 스택에 7개의 새로운 항목이 생깁니다 😂 . 우리는 확실히 이것을 원하지 않습니다

<img class="tutorial" loading="lazy" src="/_docs/tutorial/23.webp" />

우리는 히스토리 스택의 현재 항목을 밀어넣는 대신 다음 페이지로 _대체_함으로써 이를 방지할 수 있습니다.

👉 **`제출`에서 `교체`를 사용하세요**

```jsx filename=src/routes/root.jsx lines=[16-19]
// existing code

export default function Root() {
  // existing code

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              // existing code
              onChange={(event) => {
                const isFirstSearch = q == null;
                submit(event.currentTarget.form, {
                  replace: !isFirstSearch,
                });
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

검색을 시작하기 전의 페이지가 아닌 검색 결과만 교체하고 싶기 때문에, 이것이 첫 번째 검색인지 아닌지 빠르게 확인한 후 교체를 결정합니다.

각 키를 누를 때마다 더 이상 새 항목이 생성되지 않으므로 사용자는 7번 클릭하지 않고도 검색 결과를 다시 클릭할 수 있습니다 😅 .

## 탐색 없는 변형

지금까지 모든 변이(데이터를 변경하는 시간)는 탐색하는 양식을 사용하여 히스토리 스택에 새 항목을 생성했습니다. 이러한 사용자 흐름은 일반적이지만 탐색을 유발하지 않고 데이터를 변경하려는 경우도 마찬가지로 일반적입니다.

이러한 경우에는 [`useFetcher`][usefetcher] 후크가 있습니다. 이를 통해 탐색을 유발하지 않고 로더 및 작업과 통신할 수 있습니다.

연락처 페이지의 ★ 버튼이 이에 적합합니다. 우리는 새 레코드를 생성하거나 삭제하는 것이 아니며 페이지를 변경하고 싶지 않고 단지 우리가 보고 있는 페이지의 데이터를 변경하고 싶을 뿐입니다.

👉 **`<Favorite>` 형식을 가져오기 형식으로 변경**

```jsx filename=src/routes/contact.jsx lines=[4,10,14,26]
import {
  useLoaderData,
  Form,
  useFetcher,
} from "react-router-dom";

// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();
  let favorite = contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

우리가 여기 있는 동안 그 양식을 살펴보고 싶을 수도 있습니다. 항상 그렇듯이 우리 양식에는 'name' PROP이 있는 필드가 있습니다. 이 양식은 `"true" | "false"`인 `favorite` 키와 함께 [`formData`][formdata]를 전송합니다. `method="post"`가 있으므로 엑션을 호출합니다. `<fetcher.Form action="...">` PROP이 없으므로 양식이 렌더링되는 경로에 게시됩니다.

👉 **액션 만들기**

```jsx filename=src/routes/contact.jsx lines=[2,4-9]
// existing code
import { getContact, updateContact } from "../contacts";

export async function action({ request, params }) {
  let formData = await request.formData();
  return updateContact(params.contactId, {
    favorite: formData.get("favorite") === "true",
  });
}

export default function Contact() {
  // existing code
}
```

꽤 간단합니다. 요청에서 양식 데이터를 가져와 데이터 모델로 보냅니다.

👉 **라우트의 신규 엑션 구성**

```jsx filename=src/main.jsx lines=[4,20]
// existing code
import Contact, {
  loader as contactLoader,
  action as contactAction,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
        action: contactAction,
      },
      /* existing code */
    ],
  },
]);
```

자, 사용자 이름 옆에 있는 별표를 클릭할 준비가 되었습니다!

<img class="tutorial" loading="lazy" src="/_docs/tutorial/24.webp" />

두 별이 자동으로 업데이트되는지 확인하세요. 새로운 `<fetcher.Form method="post">`는 우리가 사용했던 `<Form>`과 거의 동일하게 작동합니다. 액션을 호출한 다음 모든 데이터가 자동으로 재검증됩니다. 심지어 오류도 포착됩니다. 같은 길.

그러나 한 가지 주요 차이점은 탐색이 아니라는 점입니다. 즉, URL이 변경되지 않고 히스토리 스택이 영향을 받지 않습니다.

## 낙관적 UI

마지막 섹션에서 즐겨찾기 버튼을 클릭했을 때 앱이 응답하지 않는 느낌을 받았을 것입니다. 다시 한 번, 실제 세계에서 발생하게 될 것이기 때문에 약간의 네트워크 대기 시간을 추가했습니다!

사용자에게 피드백을 제공하기 위해 [`fetcher.state`][fetcherstate](이전의 `navigation.state`와 매우 유사)를 사용하여 별을 로드 상태로 설정할 수 있지만 이번에는 더 나은 작업을 수행할 수 있습니다. "낙관적 UI"라는 전략을 사용할 수 있습니다.

가져오기 도구는 작업에 제출되는 양식 데이터를 알고 있으므로 `fetcher.formData`에서 사용할 수 있습니다. 네트워크가 완료되지 않은 경우에도 이를 사용하여 별의 상태를 즉시 업데이트합니다. 업데이트가 결국 실패하면 UI는 실제 데이터로 되돌아갑니다.

👉 **`fetcher.formData`에서 낙관적 값 읽기**

```jsx filename=src/routes/contact.jsx lines=[7-9]
// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();

  let favorite = contact.favorite;
  if (fetcher.formData) {
    favorite = fetcher.formData.get("favorite") === "true";
  }

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

지금 버튼을 클릭하면 별이 _즉시_ 새로운 상태로 바뀌는 것을 볼 수 있습니다. 항상 실제 데이터를 렌더링하는 대신 페처에 제출된 `formData`가 있는지 확인하고, 그렇다면 이를 대신 사용합니다. 작업이 완료되면 `fetcher.formData`는 더 이상 존재하지 않으며 실제 데이터를 다시 사용하게 됩니다. 따라서 낙관적 UI 코드에 버그를 작성하더라도 결국에는 올바른 상태로 돌아갑니다 🥹

## 데이터를 찾을 수 없음

로드하려는 연락처가 존재하지 않으면 어떻게 되나요?

<img loading="lazy" class="tutorial" src="/_docs/tutorial/25.webp" />

`null` 연락처를 렌더링하려고 할 때 루트 [`errorElement`][errorelement]가 이 예상치 못한 오류를 포착하고 있습니다. 오류가 제대로 처리되어서 좋았지만 더 잘할 수 있습니다!

존재하지 않는 데이터와 같이 로더나 액션에서 예상되는 오류 사례가 있을 때마다 `throw`할 수 있습니다. 호출 스택이 중단되고 React Router가 이를 포착하며 대신 오류 경로가 렌더링됩니다. 우리는 'null' 연락처를 렌더링하려고 시도하지도 않을 것입니다.

👉 **로더에 404 응답 보내기**

```jsx filename=src/routes/contact.jsx lines=[2-9]
export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response("", {
      status: 404,
      statusText: "Not Found",
    });
  }
  return { contact };
}
```

<img loading="lazy" class="tutorial" src="/_docs/tutorial/27.webp" />

'Null의 속성을 읽을 수 없습니다'라는 렌더링 오류가 발생하는 대신 컴포넌트를 완전히 피하고 대신 오류 경로를 렌더링하여 사용자에게 더 구체적인 내용을 알려줍니다.

이것은 당신의 행복한 길을 행복하게 유지합니다. 라우트 요소는 오류 및 로드 상태에 관심을 가질 필요가 없습니다.

## 경로 없는 라우트

마지막 한가지. 우리가 본 마지막 오류 페이지는 전체 페이지 대신 루트 콘센트 내부에서 렌더링되면 더 좋을 것입니다. 실제로 모든 하위 경로의 모든 오류는 콘센트에서 더 나을 것입니다. 그러면 사용자는 새로 고침을 누르는 것보다 더 많은 옵션을 갖게 됩니다.

우리는 다음과 같이 보이기를 원합니다:

<img loading="lazy" class="tutorial" src="/_docs/tutorial/26.webp" />

모든 하위 경로에 오류 요소를 추가할 수 있지만 모두 동일한 오류 페이지이므로 권장되지 않습니다.

더 깨끗한 방법이 있습니다. 라우트는 path없이 사용할 수 있으므로 URL에 새 경로 세그먼트가 필요하지 않고 UI 레이아웃에 참여할 수 있습니다. 확인 해봐:

👉 **하위 라우트를 path없는 라우트로 래핑합니다.**

```jsx filename=src/main.jsx lines=[9-21]
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    action: rootAction,
    errorElement: <ErrorPage />,
    children: [
      {
        errorElement: <ErrorPage />,
        children: [
          { index: true, element: <Index /> },
          {
            path: "contacts/:contactId",
            element: <Contact />,
            loader: contactLoader,
            action: contactAction,
          },
          /* the rest of the routes */
        ],
      },
    ],
  },
]);
```

하위 라우트에 오류가 발생하면 새로운 경로 없는 라우트가 오류를 포착하고 렌더링하여 루트 라우트의 UI를 유지합니다!

## JSX 라우트

그리고 마지막 트릭으로 많은 사람들이 JSX를 사용하여 경로를 구성하는 것을 선호합니다. `createRoutesFromElements`를 사용하면 됩니다. 경로를 구성할 때 JSX나 객체 사이에는 기능적 차이가 없으며 단순히 스타일에 따른 선호일 뿐입니다.

```jsx
import {
  createRoutesFromElements,
  createBrowserRouter,
  Route,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/"
      element={<Root />}
      loader={rootLoader}
      action={rootAction}
      errorElement={<ErrorPage />}
    >
      <Route errorElement={<ErrorPage />}>
        <Route index element={<Index />} />
        <Route
          path="contacts/:contactId"
          element={<Contact />}
          loader={contactLoader}
          action={contactAction}
        />
        <Route
          path="contacts/:contactId/edit"
          element={<EditContact />}
          loader={contactLoader}
          action={editAction}
        />
        <Route
          path="contacts/:contactId/destroy"
          action={destroyAction}
        />
      </Route>
    </Route>
  )
);
```

---

그게 다야! React Router를 사용해 주셔서 감사합니다. 이 튜토리얼이 훌륭한 사용자 경험을 구축하기 위한 확실한 시작이 되기를 바랍니다. React Router로 할 수 있는 일이 훨씬 더 많기 때문에 모든 API를 확인하세요 😀

[vite]: https://vitejs.dev/guide/
[node]: https://nodejs.org
[createbrowserrouter]: ../routers/create-browser-router
[route]: ../route/route
[tutorial-css]: https://gist.githubusercontent.com/ryanflorence/ba20d473ef59e1965543fa013ae4163f/raw/499707f25a5690d490c7b3d54c65c65eb895930c/react-router-6.4-tutorial-css.css
[tutorial-data]: https://gist.githubusercontent.com/ryanflorence/1e7f5d3344c0db4a8394292c157cd305/raw/f7ff21e9ae7ffd55bfaaaf320e09c6a08a8a6611/contacts.js
[routeelement]: ../route/route#element
[jim]: https://blog.jim-nielsen.com/
[errorelement]: ../route/error-element
[userouteerror]: ../hooks/use-route-error
[isrouteerrorresponse]: ../utils/is-route-error-response
[outlet]: ../components/outlet
[link]: ../components/link
[setup]: #setup
[loader]: ../route/loader
[useloaderdata]: ../hooks/use-loader-data
[action]: ../route/action
[params]: ../route/loader#params
[form]: ../components/form
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[formdata]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[fromentries]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries
[requestformdata]: https://developer.mozilla.org/en-US/docs/Web/API/Request/formData
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[redirect]: ../fetch/redirect
[returningresponses]: ../route/loader#returning-responses
[usenavigation]: ../hooks/use-navigation
[index]: ../route/route#index
[path]: ../route/route#path
[usenavigate]: ../hooks/use-navigate
[uselocation]: ../hooks/use-location
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[usesubmit]: ../hooks/use-submit
[navlink]: ../components/nav-link
[usefetcher]: ../hooks/use-fetcher
[fetcherstate]: ../hooks/use-fetcher#fetcherstate
