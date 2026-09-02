# 202430208 민지영
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

