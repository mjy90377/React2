# 202430208 민지영
## [2주차 - 26.09.09]
## 1. 프로젝트 수동 생성
### 생성
프로젝트 디렉토리 생성 후 내부에서
```
pnpm i next@latest react@latest react-dom@latest
```
### package.json
 package.json 파일에 `"scripts"` 추가
```
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
*  루트 레이아웃은 필수적이지 않지만 <u>루트 페이지는 필수</u>
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
    ```
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
    ```
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

