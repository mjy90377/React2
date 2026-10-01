# 202430208 민지영
## [5주차 - 26.09.30]
## 1. 앱 라우터
### Pages 라우터와 App 라우터의 차이
* **Pages**    
    * 루트: `pages/`
        * `pages/index.tsx`가 루트가 됨
    * 문서 중심
        * 루트 경로 하위의 파일 이름으로 라우팅
        * ex) `pages/about.js`는 `/about`으로 라우팅된다.
    * 페이지가 많아질수록 관리에 어려움이 생길 수 있음
    * 현재까지 유지보수되고 있는 방식이지만 새로운 프로젝트에는 사용이 권장되지 않음
* **App**
    * 루트: `app/`
        * `app/page.tsx`가 루트 페이지가 됨
    * 디렉토리 중심
        * 디렉토리 이름으로 라우팅
        * ex) `about/page.tsx`는 `/about`으로 라우팅된다.
    * Next.js 14부터 기본 권장 방식
### 앱 라우터의 기능
- 중첩 레이아웃: 여러 레벨의 layout 파일로 계층적인 레이아웃 구성 가능
    - 페이지 라우터로는 구현하기 매우 어려움
- 서버 컴포넌트(RSC): 서버에서만 렌더링되는 컴포넌트로 성능 최적화
- 로딩 UI
- 에러 UI
- 병렬 라우팅 : 하나의 경로 안에서 독립적인 뷰를 병렬로 렌더링 가능
    - 디렉토리 밑에 같은 레벨의 하위 디렉토리를 여러 개 만들 수 있음
## 2. 네비게이션 작동 방식
: Next.js는 prefetching, streaming, client-side transitions을 기본 제공하여 네비게이션 속도가 빠르고 반응성이 뛰어남
### 서버 렌더링
* Next.js에서 layout과 page는 기본적으로 리액트 서버 컴포넌트(RSC)이다.
* 서버 컴포넌트 페이로드는 클라이언트로 전송되기 전에 서버에서 생성됨
* 2가지 유형
    1. **정적 렌더링:** 빌드 시점이나 재검증 중에 발생, 결과는 캐시됨
        *  애플리케이션을 다시 빌드하지 않고도 캐시 항목 업데이트 가능
    2. **동적 렌더링**: 요청에 대한 응답으로 요청 시점에 발생함
    > * **정적 페이지**는 <u>변하지 않는 페이지</u>이지만, **정적 렌더링**은 변화가 크거나 자주 일어나지 않지만 <u>변화의 가능성이 있음</u>
* 단점: 클라이언트가 새 경로를 표시하기 전에 서버의 응답을 기다려야 함<br>
   →  Next.js는 사용자가 방문할 가능성이 높은 경로를 미리 가져오고(prefetching), 클라이언트 측 전환(client-side transitions)으로 지연 문제를 해결한다.
> #### <최초 방문을 위한 HTML 생성>
> 1. 일반적인 React 앱에서 클라이언트 사이드 렌더링만 사용 시
>       - 처음 페이지를 방문할 때는 빈 HTML+JS 파일만 내려줌
>       - 브라우저가 JS를 실행해야 화면이 렌더링됨
> 2. Next.js에서 사용자가 특정 URL을 처음 방문하면
>       - 해당 페이지의 HTML을 미리 생성해서 브라우저에 전달
>       - 브라우저는 JS 실행 전에도 HTML 뼈대와 콘텐츠 표시 가능
>        이후 React가 하이드레이션 과정을 거쳐 상호작용 가능
>> → 초기 방문 시에도 HTML을 생성해서 내려주기 때문에 사용자 경험(UX)이 좋아지고 SEO에 유리
### Prefetching
: 사용자가 **경로를 이동하기 전에** <u>백그라운드에서 해당 경로를 로드</u>하는 프로세스
- 링크가 클릭되기 전에 렌더링에 필요한 데이터를 **클라이언트 측에 미리 준비**<br>
   → 애플리케이션에서 경로 간 이동이 즉각적으로 느껴짐
- Next.js는 `<Link>` 컴포넌트와 연결된 경로를 자동으로 사용자 뷰포트에 미리 가져옴
    - `<a>`태그 사용 시 프리페칭 X
- <u>타임 딜레이 해결</u>을 위한 여러 방법 중 하나
- 경로의 프리페칭 범위는 정적/동적에 따라 달라짐
    - **정적 경로** : 전체 경로가 프리페치됨
    - **동적 경로** : 프리페치를 건너뛰거나 loading.tsx가 있는 경우 부분적으로 프리페칭
        - slug 등이 동적 경로이다.
> *Next.js는 동적 라우팅을 건너뛰거나 부분적으로 프리페칭하는 방법으로 사용자가 방문하지 않을 수도 있는 경로에 대한 서버의 불필요한 작업을 방지한다.*

→ 동적 경로의 네비게이션 개선을 위해 Streaming을 사용
### Streaming
: 서버에서 전체 경로가 렌더링될 때까지 기다리지 않고, 동적 경로의 일부가 준비되는 즉시 클라이이언트에 전송하는 것
- 페이지 일부가 로딩 중이더라도 사용자는 콘텐츠를 더 빨리 볼 수 있음
- 동적 경로를 부분적으로 미리 가져올 수 있음
- 공유 레이아웃과 로딩 스켈레톤을 미리 요청할 수 있음
- 사용법: 라우팅 폴더에 `loading.tsx` 파일 생성
- Next.js는 내부적으로 page.tsx 콘텐츠를 `<Suspense>` 경계로 자동 매핑함
    - Suspense라는 내부 컴포넌트가 컨텐츠 전체를 감싸는 형태
- 미리 가져온 대체 UI는 경로가 로드되는 동안 표시되고, 준비가 되면 실제 콘텐츠로 대체
    #### [loading.tsx의 이점] 
    - 사용자에게 즉각적 네비게이션과 시각적 피드백 제공
    - 공유 레이아웃 상호작용 가능, 네비게이션은 중단될 수 있음
    - 개선된 웹 핵심 지표 제공 : TTFB, FCP, TTI
    > #### Core Web Vitals (웹 성능 지표)
    > * <레거시 지표>
    >   - TTFB: 네트워크와 서버의 성능
    >   - FCP: 서버에 뭐가 처음 뜰 때까지 걸리는 시간
    >   - TTI: 상호작용이 가능할 때까지 걸리는 시간
    > * <현재 사용되는 지표>
    >    - LCP: 뷰포트 내에서 가장 큰 페이지 요소를  표시하는 데 걸리는 시간
    >    - FID: 사용자가 웹페이지와 상호작용을 시도하는 첫 번째 순간부터 웹 페이지가 응답하는 시간
    >    - CLS: 콘텐츠가 얼마나 불안정한 지 측정한 값 (레이아웃의 이동 빈도 등)

→ Link 컴포넌트를 통해 클라이언트 측 전환을 사용함
### Client-side transitions(클라이언트 측 전환)
* 서버 렌더링 페이지로 이동하면 전체 페이지가 로드됨
- state가 삭제 → 뷰포트 초기화, 상호작용 차단, 스크롤 위치 재설정
    - Next.js는 Link 컴포넌트를 사용하는 클라이언트 측 전환을 통해 이를 방지함
* 페이지를 다시 로딩하는 대신 콘텐츠를 동적으로 업데이트

    * 공유 레이아웃과 UI가 유지됨
    * 현재 페이지를 프리페칭 로딩 상태거나 사용 가능한 새 페이지로 바꿈

- 클라이언트 측 전환은 서버에서 렌더링된 앱을 클라이언트에서 렌더링된 앱처럼 느껴지게 함
- 프리페칭, 스트리밍과 함께 사용하면 동적 경로에서 빠른 전환 가능

⇒ 이 3가지가 서버 사이드 렌더링의 로딩 시간을 줄이면서 UX를 개선할 수 있는 대표적  방법

### 전환을 느리게 만드는 요인
: Next.js는 최적화(위 3가지)를 통해 네비게이션 속도가 빠르고 반응성이 뛰어나지만, 특정 조건에서 전환 속도가 느릴 수 있다.
#### 1. loading.tsx가 없는 동적 경로
* 동적 경로로 이동할 때 클라이언트는 결과를 표시하기 전에 서버의 응답을 기다려야 함
* 동적 경로에는 loading.tsx를 추가하는 것이 권장됨
#### 2. generateStaticParams가 없는 동적 세그먼트
* 동적 세그먼트는 사전 렌더링될 수 있지만 `generateStaticParams`가 누락되어 사전 렌더링되지 않는 경우 
    * 요청 시점에 동적 렌더링으로 대체됨
* `generateStaticParams`을 통해 <u>빌드 시점에 경로가 정적으로 생성</u>되도록 할 수 있다
* 없을 경우 요청할 때마다 동적으로 서버에서 처리
    * 런타임에서 슬러그 해석
> * 자주 변하지 않는 사이트에서 사용이 권장됨
>    * 정적 사이트처럼 빠름
> * 사용자 입력, DB 조회 등이 필요한 경우 비권장
>   *  런타임 처리가 효율적

 ---   
## [4주차 - 26.09.23]
## 1. 레이아웃과 페이지
> #### *루트 레이아웃은 필수
> * 서브페이지는 레이아웃 파일이 선택 사항이지만 루트에는 `page.tsx`와 함께 `layout.tsx`또한 필수이다.
> * 루트 레이아웃에는 `<html>`태그와 `<body>` 태그가 반드시 있어야 한다.
### 중첩 라우트 만들기
: 여러 개의 URL 세그먼트로 구성된 경로
- Next.js에서 
    * 폴더는 URL 세그먼트에 매핑되는 경로 세그먼트를 만듦
    * 디렉토리 구조 -> 라우팅 주소
    * 페이지와 레이아웃 -> UI
- 폴더를 계속 중첩하여 중첩된 경로를 만들 수 있음
    - 폴더 이름을 <u>대괄호로 묶으면</u> 데이터를 기반으로 여러 페이지를 생성하는 데 사용되는 **동적 경로 세그먼트가** 생성됨 (ex: `[slug]`)
### slug
: 사이트의 특정 페이지를 쉽게 읽을 수 있는 형태로 식별하는 URL의 일부
-* `/blog/[slug]`의 `[slug]`는 불러올 데이터의 **key**
    
    ⇒ 데이터에 slug key가 있어야 함
    
    > * 이름은 반드시 slug일 필요 없고, 해당하는 이름의 키가 데이터에 있으면 된다.
    > * 그러나 대부분 slug라는 이름으로 사용한다.
- 하나의 페이지로 데이터 수만큼 페이지를 제공할 수 있음

- params가 비동기 객체처럼 다뤄지는 경우 오류 발생
```tsx
export default async function Posts({params}: {params: Promise<{slug: string}>}){
    const { slug } = await params;
    const post = posts.find((p) => p.slug === slug);
```
* `await` 사용
    * 함수를 `async`로 선언해야 사용 가능
    * 서버의 데이터를 읽어올 때 <u>타임 딜레이에 의한 오류</u>를 방지함
- `posts`: 배열
    - `.find()`는 조건에 맞는 첫 번째 요소를 반환함
        - .find()는O(n) — 시간 복잡도가 데이터의 크기에 비례
        - 때문에 데이터가 크면 비효율적

⇒ `p.slug`가 URL에서 온 slug와 일치하는 게시글을 찾음
### map() 함수로 slug 가져오기
```tsx
import Link from "next/link";
import {posts} from "./blog/posts"

export default function Home() {
  return (
    <div>
            <h1>블로그 목록</h1>
            <ul>
            {posts.map((post) => (
                <li key={post.slug}>
                    <Link href={`/blog/${post.slug}`}>{post.title}</Link>
                </li>
            ))}
            </ul>
     </div>
  );
}

```
### searchParams prop
: URL의 쿼리 문자열을 읽는 방법
> #### <params와 비교>
>— **params**: 동적 세그먼트에서 가져오는 값 (URL의 path 부분에 포함된 데이터)
<br>
>— **searchParams** : URL에서 `?` 뒤에 붙는 key=value
* ex) `/products?category=shoes&page=2`에서 `category=shoes`와 `page=2`가 searchParams이다.
* 컴포넌트의 props로 전달됨
* 동적 렌더링으로 처리됨
    #### #동적 렌더링인 이유
    :Next.js에서 페이지는 정적/동적으로 렌더링될 수 있음
    * searchParams는 요청이 들어와야만 값을 알 수 있음
    * 페이지를 정적으로 미리 생성할 수 없고 <u>요청이 올 때마다 렌더링</u> 필요
    * 정적 렌더링에서는 searchParams 사용 불가능
* 사용법
    ```tsx
    export default async function ProductsPage({
        searchParams
    } : {
        searchParams: Promise<{id?: string; name?: string}>
    }) {
        const {id = "non id", name = "non name"} = await searchParams
        return(
            <div>
                <h1>Products Page</h1>
                <p>id : {id}</p>
                <p>name : {name}</p>
            </div>
        )
    }
    ```
    --> /products로 라우팅된 page.tsx
    * url을 통해 전달된 값이 페이지에 출력됨
    * 전달 방법: `?` 뒤에 `key=value` 형식으로 전달하고 `&`로 구분한다.
        * ex) `http://localhost:3000/products?id=123&name=foo`
        * -> '123', 'foo' 가 전달됨
    * 아무 값도 전달하지 않으면 기본값이 출력됨
        * `await`을 통해 들어온 값 유무에 따라 출력값 결정
    * 없는 속성을 전달해도 오류가 발생하지 않음
        * 아무 일도 일어나지 않는다.
### Next.js의 라우팅 시스템
: Next.js는 자체적인 라우팅 시스템을 내장하고 있다.
| 일반 React | Next.js |
| --- | --- |
| 수동 라우팅 | 자동 라우팅 |
| 외부 라이브러리 필요 | 자체적 시스템 | 
| 코드에서 직접 정의 |    파일/폴더 이름으로 라우트가 자동 매핑됨 | 

---
## [3주차 - 26.09.16]
## 1. 폴더 및 파일 규칙
### [라우팅 그룹 및 비공개 폴더]
: 라우팅에 관여하지 않으면서 페이지들을 <u>종류별로 모아둘 수 있음</u>
- `()`(소괄호) 사용
    - 소괄호로 감싼 부분은 URL에서 제외, 주소로 들어가지 않음
    - app/(abc)/page.tsx→  `/`
    - app/(abc)/ddd/page.tsx → `/ddd`
- `_`(언더스코어) 사용
    - 해당 폴더와 모든 하위 폴더까지 **라우팅에서 제외**
    - app/aaa/_components/Post.tsx → 라우팅되지 않음. 비공개 디렉토리
### [병렬 및 가로채기 라우팅]
: 다른 페이지를 끌어다 사용하는 방법
- `@folder` : 명명된 슬롯 (사이드바 + 메인 콘텐츠)
- `(.)folder` : 동일 레벨 (형제 라우트 미리보기)
- `(..)folder` : 한 레벨 위 (부모의 자식 오버레이)
- `(..)(..)folder` : 두 레벨 위(중첩 오버레이)
- `(…)folder` : 루트에서
### [메타데이터 파일 규칙]
:종류별 파일들을 .js, .ts 등 형식으로 저장
- 앱 아이콘
- 오픈 그래프와 트위터 이미지
- SEO (사이트맵, 로봇 접근 관련)
### [Open Graph Protocol]
: 웹사이트를 SNS에 전송할 때 미리보기를 생성하는 프로토콜
- 인스타그램, X, 카카오톡 등에서 사용
- 페이스북이 주도하는 표준화 규칙
    - 모든 플랫폼이 동일하지는 않음
- 웹페이지의 메타 태그에 선언함
## 2. 프로젝트 구성하기
> Next.js 프로젝트 구성에 대한 제약은 없지만, 체계적 구성에 도움되는 몇 가지 기능이 제공됨
### [component 계층 구조]
- layout.js
- template.js
- error.js(React 오류 경계)
- loading.js(리액트 서스펜스 경계)
- not-found.js(React 오류 경계)
- page.js 또는 중첩 layout.js
### [layout과 template의 차이]

: 동작 방식의 차이
- layout.tsx
    - 정적, 상태가 유지됨
- template.tsx
    - 동적, 매번 초기화됨
### [Colocation]

: 파일 및 폴더를 <u>기능별로 그룹화</u>하여 프로젝트의 구조를 명확하게 정의함

- app 디렉토리에서 중첩된 폴더는 라우팅 구조가 된다.
    - 각 폴더는 url에서 해당 세그먼트에 매핑되는 **라우팅 세그먼트**를 나타낸다.
- 폴더로 라우트 구조가 정의되어도, page.js 또는 route.js파일이 추가되기 전까지는 외부에서 해당 라우트에 접근할 수 없다.
- 경로가 공개적 접근 가능으로 설정되어도 page.js나 route.js가 반환하는 내용만 라우팅된다.
    - <u>다른 프로젝트 파일</u>을 app 디렉토리의 라우팅 세그먼트에 배치해도 안전하다.
    - page나 route를 제외한 컴포넌트는 라우팅되지 않기 때문
### [프로젝트 파일]
: 프로젝트에 필요한 컴포넌트들을 프로젝트 파일이라고 한다.
- app 디렉토리에 저장할 수 있음
- 라우팅 페이지와 분류하여, src 디렉토리에 저장할 수 있음
    * -> src 디렉토리에 저장하는 것이 주로 권장됨

### [비공개 폴더의 용도]

: app 디렉토리의 파일은 안전하게 코로케이션 될 수 있기 때문에, 코로케이션에 비공개 폴더는 불필요

1. UI 로직과 라우팅 로직 부리
2. 프로젝트와 Next.js 전반에서 내부 파일을 일관되게 구성
3. 코드 편집기에서 파일 정렬, 그룹화
4. 잠재적인 이름 충돌 방지

### [라우팅 그룹의 용도]
: app 디렉토리 하위에 소괄호로 감싼 폴더를 통해 그룹화시킬 수 있다
* url 경로에 포함되지 않으면서 종류별로 정리할 수 있음
* 동일한 레벨에서 중첩 레이아웃을 만들 수 있음

## 3. 레이아웃과 페이지
### [layout]
: 여러 페이지에서 공유되는 UI
- 네비게이션에서 state 및 상호작용을 유지함
  - 리렌더링되지 않음
- layout 컴포넌트는 페이지나 다른 레이아웃이 될 수 있는 children prop를 받을 수 있게 해야 함
- app 디렉토리 바로 하위에 있는 layout은 **루트 레이아웃**이 됨 
    - 루트 레이아웃에서 `<body>`와 `<html>` 태그는 필수적으로 있어야 한다.
- 기본 구조
```tsx
    export default function MarketingLayout({
    children,
    }: {
    childern: React.ReactNode;
    }) {
    return (
        <html
        lang="en"
        >
        <body>
            <header>Marketing Layout Header</header>
            {children}
            <footer>Marketing Layout Footer</footer>
            </body>
        </html>
    );
    }
```
> *page와 layout 등의 라우팅 파일은 page.tsx, layout.tsx처럼 정해진 이름을 사용해야 한다.
>> ->파일 내부에서의 컴포넌트 이름은 구분 가능하도록 지정할 수 있음 (ex: MarketingLayout)


---
## [2주차 - 26.09.09]
## 1. 프로젝트 수동 생성
### 생성
프로젝트 디렉토리 생성 후 내부에서
```
pnpm i next@latest react@latest react-dom@latest
```
### package.json
 package.json 파일에 `"scripts"` 추가
```json
"scripts": {
		"dev": "next dev",
		"build": "next build",
		"start": "next start",
		"lint": "eslint"
	},
```
- `next dev`: 개발 서버 시작
- `next build`: 애플리케이션 빌드
- `next start`: 프로덕션 서버 시작
- `eslint`: ESLint 실행 관련
### app 디렉토리
* 라우팅에서 루트 (`/`)가 되는 디렉토리
* 일반적으로 `layout.tsx`, `page.tsx`가 위치함
    * 이름이 관례적으로 소문자로 시작
    * 컴포넌트 형태이지만 <u>라우팅 관련</u> 파일이기 때문
    * 내부 코드에서의 이름은 대문자로 시작함
*  루트 레이아웃과 루트 페이지는 필수
### TypeScript 환경 만들기
: **타입 정의**를 제공하는 패키지 설치 필요
```
pnpm add -D @types/react @types/react-dom
```
* 타입스크립트 환경에서 react와 react-dom을 사용할 수 있게 하는 패키지 추가
    > #### *npm과 pnpm의 차이점
    > * npm: 패키지 일괄 설치와 단일 패키지 설치 모두 `install` 명령어를 사용한다.
    > * pnpm: 패키지 일괄 설치 시에는 `install`, 단일 패키지 설치 시에는 `add` 명령어를 사용한다.
* -D 옵션: 개발할 때만 사용하고 배포 시 제외한다는 의미
    * package.json의 `devDependencies`에 등록됨

### public 디렉토리
: 변동 없는 **정적 리소스**를 보관
* 선택사항이지만 있는 것이 편리함
* 별도의 경로 없이 루트 경로로 리소스 참조 가능
    * --> "/public/logo.png"가 아닌 “/logo.png” 형태로 참조 

### 기타 설정
1. VS Code에 TypeScript 플러그인 설치
    * Ctrl + Shift + P로 TypeScript 검색
    * "TypeScript: Select TypeScript Version" 선택 
2.  src/app/about 폴더 생성
    *  내부에 `page.tsx`만들기
    * app의 페이지와 혼동될 수 있어 기존에는 구분을 위한 설정이 필요했지만, 현재는 VS Code에서도 기본적으로 구별이 가능함

### 경로 별칭(alias) 설정
* 자동 설치 시 옵션에서 기본값인 no를 선택해도 적용됨
* 수동 설치 시 `tsconfig.json`의 `"compilerOptions"`에 추가
    1. 기존(레거시) 사용법
    ```json
    "compilerOptions": {
        "baseUrl": "src/",
        "paths": {
            "@/styles/*": ["styles/*"],
            "@/components/*": ["components/*"]
        }
    }
    ```
    * `"baseUrl"`옵션은 사용 중단될 예정
        * 현재 위 방식 사용 시 경고 발생
        * `“ignoreDeprecations” : “6.0”` 로 사용 중단 예정 항목 무시 가능
    2. 현재 사용법
    ```json
    "compilerOptions": {
        "paths": {
            "@/*": ["./src/*"],
            "@/components/*": ["./src/components/*"]
        }
    }
    ```
    * `./src` 경로를 `@` 로 사용
### Next.js 앱 업그레이드
`pnpm next upgrade`
* 프로젝트를 최신 상태로 유지하기 위함
* 내부 문서까지 업그레이드됨

## 2.프로젝트 구조
### 자동 생성되는 항목
: 프로젝트 자동 생성 시 기본적 혹은 선택 가능한 항목
- pacakage.json 파일의 scripts
- public 디렉토리
- TypeScript 사용(선택) : tsconfig.json 파일 생성
- Eslint 설정(선택) : eslintrc.json 대신 eslint.config.mjs 파일
    > * **mjs**: 복잡한 JSON 대신 ESLint가 새로 도입한 방식 (Module JavaScript)
    >   - 조건문, 변수 등 사용 가능
    >   - 다른 설정파일 import 가능
    >   - 프로젝트 유지보수 용이
    > * json도 여전히 사용되지만, mjs는 최신 권장 방식이다.
- Tailwind CSS 사용(선택) : default는 yes
- src 디렉토리 사용 (선택) : 사용 권장
    > * src 디렉토리를 사용하지 않으면 코드가 루트 디렉토리에 위치하기 때문에 관리가 어려울 수 있다.
- App Router(선택)
- app/layout.tsx 파일 및 app/page.tsx
- Turbopack 사용(선택)
- import alias 사용(선택): No를 선택해도 `@`는 사용됨
- .gitignore
### 폴더 및 파일 규칙
1. 최상위 폴더
    * app : 앱 라우터
    * pages : 페이지 라우터 (앱 라우터 선택 시 사용하지 않음)
    * public : 제공될 정적 자산
    * src : 소스 폴더
2. 최상위 파일
    *  애플리케이션 구성, 종속성 관리, 프록시 실행, 모니터링 도구 통합, 환경 변수 정의에 사용됨
    * 환경설정용 파일
3. 라우팅 파일
    >  헤더, 내비게이션, 푸터 등 공유 UI 레이아웃과 스켈레톤 로딩 화면, 오류 화면 등을 추가 가능
    - layout
    - page
    - loading
        - 스켈레톤
    - not-found
        - not found 시 보여줄 UI
    - error
    - global-error
    - route
    - template
        - 리렌더링된 레이아웃
    - default
4. 중첩 라우팅(Nested routes)
    -  앱 라우터의 핵심: 디렉토리는 URL의 **세그먼트**를 정의함
        - 디렉토리를 중첩하면 세그먼트도 중첩됨
        - 모든 수준의 레이아웃은 하위 세그먼트를 감쌈
    - 페이지나 경로 파일이 존재하면 해당 경로는 공개됨
        - app/blog/page.tsx에서 /blog가 세그먼트
5. 동적 라우팅(Dynamic routes)
    - 대괄호를 이용해서 세그먼트를 매개변수화 할 수 있음
        - 대괄호로 감싼 디렉토리 이름 뒤에 쓴 내용은 props로 페이지에 전달됨
    1.  단일 매개변수 -  [segment]
        - 하나의 하위 경로만 허용
            - 없거나 여러 개면 오류
        - ex) [segment]/abcd
    2. 모든 값을 포괄하는 매개변수 - […segment]
        - 배열로 전달함
        - ex) […segment]/aaa/bbb/ccc
        - 개수 상관 없음
            - 해당 경로 아래의 모든 하위 경로를 하나의 배열로
            - 1개 이상 있어야 함
            - 없으면 오류
    3. 선택적 포괄 매개변수 - [[…segment]]
        - 위와 같이 배열 형태의 매개변수
        - 기본 경로도 허용됨(세그먼트 없는 경우)



---
## [1주차 - 26.09.02]
## 1. Next.js
: Next.js는 <u>React의 프레임워크</u>이다.
### [기본 정보]
* App Router과 Pages Router 두 가지의 라우터가 있다.
    * **Pages Router**: 과거부터 지금까지 사용되어온 라우터
    * **App Router**: 최근 프로젝트에 주로 사용되는 최신 라우터
* TypeScript와 JavaScript 문법을 사용한다.
* `npm`을 권장하는 React와 달리 `pnpm` 사용이 권장된다.
* `localhost:3000` 포트에서 실행된다.

## 2. pnpm
: 고성능 Node.js <u>패키지 매니저</u>
* npm, yarn 등과 같은 용도이지만, 많은 문제점을 개선함
### [장점] 
1. 하드 링크 기반의 효율적인 저장 공간
    - 패키지 설치 시 <u>전역 store에 저장</u>함
    - 각 프로젝트의 node-modules 디렉토리에 패키지에 대한 **하드 링크** 생성
    > #### <npm과의 차이점>
    > npm 사용 시: 프로젝트 생성 시마다 같은 노드 모듈을 새로 설치
    > pnpm사용 시: 한 번 설치된 패키지의 링크만 가져와서 사용
2. 빠른 설치 속도
    * 패키지 재사용으로 초기 설치뿐만 아니라 종속성 설치, 업데이트 속도가 모두 빠름
3. 효율적인 종속성 관리

>  **일반 React 프로젝트에서는 npm이 주로 사용되며 pnpm을 사용하기 복잡하다.*

### [하드 링크]
#### <파일의 구조>
1. Directory Entry
    - 파일 이름과 inode 번호 매핑 정보
2. inode
    - 데이터를 제외한 모든 정보
3. data blocks
    - 실제 데이터

#### <하드 링크의 특징>
* Directory Entry에 매핑 정보가 추가되어 동일한 inode를 가리킴
    * ==> 원본과 하드 링크는 사본의 개념이 아닌 **같은 파일**이다.
* 데이터 블록을 원본과 100% 공유함
* 원본과 하드 링크 중 하나만 삭제하면 Directory Entry에서 이름만 삭제됨
    * 둘 중 하나만 남아있어도 데이터 블록이 유지된다.

## 3. Next.js 프로젝트 생성
### [생성 방식]
1. `pnpm create next-app@latest my-app --yes`
    * 프롬프트 건너뛰고 기본 설정 사용   

2. `pnpm create next-app`
    *  커스텀 설정 사용
    > #### 커스텀 설정 종류
    > * TypeScript 사용 (yes)
    > * React compiler 사용(no)
    > * Tailwind CSS 사용(yes)
    > * src 디렉터리 사용 (yes)
        >> * 기본 설정은 no이기 때문에 사용하려면 커스텀 설정이 필요하다. 
    > * App Router 사용(yes, 필수)
    > * @/* 별칭 사용 (yes)
    > * AGENTS.md 사용 (yes)
3. 수동으로 생성

