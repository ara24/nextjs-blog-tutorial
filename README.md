# Next.js blog 튜토리얼
Next.js Document에 있는 기초 과정 연습

- 실습 후 vercel에 배포: [Demo](https://nextjs-blog-tutorial-wine-seven.vercel.app/)

- 학습 가이드: [Next.js Foundations Course](https://nextjs.org/learn-pages-router/foundations/about-nextjs)

---
# 학습 내용 정리
> 링크들  
> [Next.js 기초](https://nextjs.org/learn-pages-router/foundations/about-nextjs)  
> [Next.js 앱 만들기](https://nextjs.org/learn-pages-router/basics/create-nextjs-app/setup)  
> [vercel(배포 사이트)](https://vercel.com/)

## Next.js 라우터

/pages 디렉토리 내부 구조대로 경로가 지정 된다.

/pages/index.js 파일의 컴포넌트가 `/` (루트)에 노출된다.

/pages/posts/first-post.js 파일은 `/posts/first-post` 경로에 라우팅된다.

```jsx
import Link from 'next/link';

export default function FirstPost() {
    return (
        <>
            <h1>First Post</h1>
            <h2>
                <Link href="/">Back to home</Link>
            </h2>
        </>
    );
}
```

js 파일에 1개의 export default 컴포넌트가 있어야 한다.

## 링크

a 태그 대신에 Link 태그를 쓸 수 있다.

a 태그에서 경로를 이동하면 새로고침되고, link 태그를 클릭해서 이동하면 client-side navigation이 동작하기 때문에 전체 페이지를  새로 로드하지 않고 페이지 이동을 할 수 있다.

Next.js 이미지 등 정적 자산(static assets) 위치

/public 디렉토리 내부에 위치한다.

## 이미지 관리

html에서 `<img src="/images/profile.jpg" alt="Your Name" />` 처럼 img 태그로 이미지를 추가한다. 이렇게 이미지를 추가하면 다음 처리를 수동으로 해야 한다.

- 이미지가 다양한 화면 크기에 반응하는지 체크
- 타사 도구 또는 라이브러리를 사용하여 이미지 최적화
- 뷰포트에 들어갈 때만 이미지 로드

next.js에서는 이를 즉시 처리할 수 있는 `Image` 를 제공한다.

next.js는 사용자가 요청할 떄 필요에 따라 이미지를 최적화한다. 이미지는 기본적으로 지연로드된다. 뷰포트로 스크롤되면서 로드된다.

결론: img 태그 대신에 Image를 사용한다.

```jsx
import Image from 'next/image';

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
);
```

## 메타 데이터

next.js에 내장된 React component인 `<Head>` 를 사용한다. html `<head>`내용을 수정할 수 있다.

```jsx
import Head from 'next/head';

export default function FirstPost() {
    return (
        <>
            <Head>
                <title>First Post</title>
            </Head>
            <h1>First Post</h1>
        </>
    );
}
```

## Third-Party Javascript 추가

나쁜 예:

```jsx
<Head>
  <title>First Post</title>
  <script src="https://connect.facebook.net/en_US/sdk.js" />
</Head>
```

스크립트가 렌더링을 차단하고 페이지 콘텐츠 로드를 지연시킬 수 있다.

좋은 예:

```jsx
import Script from 'next/script';

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
      <Script
        src="https://connect.facebook.net/en_US/sdk.js"
        strategy="lazyOnload"
        onLoad={() =>
          console.log(`script loaded correctly, window.FB has been populated`)
        }
      />
      <h1>First Post</h1>
      <h2>
        <Link href="/">← Back to home</Link>
      </h2>
    </>
  );
}
```

next.js에서 제공하는 `<Script>` 를 사용한다.

- strategy는 서드파티 스크립트를 로드해야하는 시기를 제어한다. lazyOnload 값은 브라우저 idle time 동안 이 스크립트를 로드하도록 지시한다.
- onLoad는 스크립트 로드가 완료된 후 즉시 javascript 코드를 실행하는데 사용된다. 스크립트가 올바르게 로드되었음을 언급하는 메시지를 콘솔에 기록한다.

## 레이아웃 만들기

/components/layout.js 파일을 추가하고, 아래 코드를 입력한다.

```jsx
export default function Layout({children}) {
    return <div>{children}</div>
}
```

레이아웃을 적용할 컴포넌트(/pages/posts/first-post.js)에 레이아웃을 추가한다.

```jsx
import Link from 'next/link';
import Image from 'next/image';
import Head from 'next/head';
import Layout from '../../components/layout';

export default function FirstPost() {
    return (
        <Layout>
            <Head>
                <title>First Post</title>
            </Head>
            <h1>First Post</h1>
            <h2>
                <Link href="/">Back to home</Link>
            </h2>
            <Image
                priority={true}
                src="/images/profile.jpg"
                height={144}
                width={144}
                alt="Your name"
            />
        </Layout>
    );
}
```

## CSS 모듈 사용

파일 이름이 `.module.css`로 끝나야 한다.

쉽게 알아보기 위해 layout.js의 css 파일 이름을 layout.module.css로 생성한다.

layout.module.css

```jsx
.container {
    max-width: 36rem;
    padding: 0 1rem;
    margin: 3rem auto 6rem;
}
```

layout.js

```jsx
import styles from './layout.module.css';

export default function Layout({children}) {
    return <div className={styles.container}>{children}</div>
}
```

브라우저 개발자도구에서 렌더링된 div를 살펴보면 클래스 이름이 ‘layout_container__[hash]’로 지정된다.

next.js는 styled-components를 지원하지 않는다.

## 최상위 컴포넌트

/pages/_app.js 파일에서 export default를 붙인 컴포넌트가 최상위 React Component다.

최상위에서 모든 페이지를 래핑한다.

[https://nextjs.org/docs/pages/building-your-application/routing/custom-app](https://nextjs.org/docs/pages/building-your-application/routing/custom-app)

next.js는 App 컴포넌트를 사용하여 페이지를 초기화하고, 다음 작업을 수행할 수 있다.

- 페이지 변경 사이에 공유 레이아웃 만들기
- 페이지에 추가 데이터 삽입
- 전역 CSS 추가

/pages/_app.js

```jsx
export default function App({Component, pageProps}) {
    return <Component {...pageProps} />;
}
```

## Global CSS

전역으로 적용되어야하는 style이다. /styles/global.css 파일을 만들고 reset css 내용을 넣어준다. (css 이름은 자유롭게 지정해도 된다.

import는 /pages/_app.js에 전역에 적용된다.

/styles/global.css

```css
html,
body {
  padding: 0;
  margin: 0;
  font-family:
    Inter,
    -apple-system,
    BlinkMacSystemFont,
    Segoe UI,
    Roboto,
    Oxygen,
    Ubuntu,
    Cantarell,
    Fira Sans,
    Droid Sans,
    Helvetica Neue,
    sans-serif;
}

a {
  color: inherit;
  text-decoration: none;
}

* {
  box-sizing: border-box;
}

img {
  max-width: 100%;
  height: auto;
}
```

/pages/_app.js

```jsx
import '../styles/global.css';

export default function App({Component, pageProps}) {
    return <Component {...pageProps} />;
}
```

## CSS 유틸리티 클래스

여러 컴포넌트에 재사용 할 수 있는 클래스 세트다.

javascript에서도 여려군데에 사용할 함수들 모아놓고 util 함수라고 하는데, css도 마찬가지이다.

```html
<div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex items-center space-x-4">
  <div class="shrink-0">
    <img class="h-12 w-12" src="/img/logo.svg" alt="ChitChat Logo">
  </div>
  <div>
    <div class="text-xl font-medium text-black">ChitChat</div>
    <p class="text-slate-500">You have a new message!</p>
  </div>
</div>
```

위 코드처럼 css를 작성하지 않고 유틸에 만들어놓은 클래스들로 디자인을 구축하는 것이다.

tailwind css가 이런식으로 되어있다.

[https://tailwindcss.com/docs/utility-first](https://tailwindcss.com/docs/utility-first)

## 레이아웃 심화

Layout에 props를 받아서 상황에 따라 다른 레이아웃을 보여줄 수 있다.

아래 코드는 home을 받아서 처리한 코드이다. 홈 페이지에서는 `<Layout home />` 을 사용했고, 그 외의 페이지에서는 `<Layout />` 을 사용했다.

/components/layout.js

```jsx
import Head from 'next/head';
import Image from 'next/image';
import Link from 'next/link';
import styles from './layout.module.css';
import utilStyles from '../styles/utils.module.css';

const name = 'Ara';
export const siteTitle = 'Next.js Sample Website';

export default function Layout({children, home}) {
    return (
        <div className={styles.container}>
            <Head>
                <link rel="icon" href="/favicon.ico" />
                <meta
                    name="description"
                    content="Learn how to build a personal website using Next.js"
                />
                <meta
                    property="og:image"
                    content={`https://og-image.vercel.app/${encodeURI(siteTitle)}.png?theme=light&md=0&fontSize=75px&images=https%3A%2F%2Fassets.vercel.com%2Fimage%2Fupload%2Ffront%2Fassets%2Fdesign%2Fnextjs-black-logo.svg`}
                />
                <meta name="og:title" content={siteTitle} />
                <meta name="twitter:card" content="summary_large_image" />
            </Head>
            <header className={styles.header}>
                {home ? (
                    <>
                        <Image
                            priority
                            src="/images/profile.jpg"
                            className={utilStyles.borderCircle}
                            height={144}
                            width={144}
                            alt=""
                        />
                        <h1 className={utilStyles.heading2Xl}>{name}</h1>
                    </>
                ) : (
                    <>
                        <Link href="/">
                            <Image
                                priority
                                src="/images/profile.jpg"
                                className={utilStyles.borderCircle}
                                height={108}
                                width={108}
                                alt=""
                            />
                        </Link>
                        <h2 className={utilStyles.headingLg}>
                            <Link href="/" className={utilStyles.colorInherit}>
                                {name}
                            </Link>
                        </h2>
                    </>
                )}
            </header>
            <main>{children}</main>
            {!home && (
                <div className={styles.backToHome}>
                    <Link href="/">← Back to home</Link>
                </div>
            )}
        </div>
    );
}
```

/pages/index.js

```jsx
import Head from 'next/head';
import Link from 'next/link';
import Layout, {siteTitle} from '../components/layout';
import utilStyles from '../styles/utils.module.css';

export default function Home() {
  return (
    <Layout home>
      <Head>
        <title>{siteTitle}</title>
      </Head>
      <section className={utilStyles.headingMd}>
        <p>Hello, I'm Ara.</p>
        <p>
          ( This is a sample website - you’ll be building a site like this on{' '}
          <a href="https://nextjs.org/learn">our Next.js tutorial</a>.)
        </p>
      </section>
      <Link href="posts/first-post">navigate Page!</Link>
    </Layout>
  );
}
```

/pages/posts/first-post.js

```jsx
import Link from 'next/link';
import Image from 'next/image';
import Head from 'next/head';
import Layout from '../../components/layout';

export default function FirstPost() {
    return (
        <Layout>
            <Head>
                <title>First Post</title>
            </Head>
            <h1>First Post</h1>
            <h2>
                <Link href="/">Back to home</Link>
            </h2>
            <Image
                priority={true}
                src="/images/profile.jpg"
                height={144}
                width={144}
                alt="Your name"
            />
        </Layout>
    );
}
```

## 조건부 class 적용은 clsx 라이브러리 사용

clsx는 className을 쉽게 전환할 수 있는 라이브러리다.

clsx, postCSS, sass 설치는 아래 링크에서 확인

[https://nextjs.org/learn-pages-router/basics/assets-metadata-css/styling-tips](https://nextjs.org/learn-pages-router/basics/assets-metadata-css/styling-tips)

## 사전 렌더링(Pre-Rendering)

[https://nextjs.org/learn-pages-router/basics/data-fetching](https://nextjs.org/learn-pages-router/basics/data-fetching)

사전 렌더링이란 페이지를 미리 렌더링한다는 것이다. 성능과 SEO가 향상될 수 있다.

HTML과 최소한의 Javascript 코드로 페이지를 만들고, 브라우저가 페이지를 로드하면 해당 javascript 코드가 실행되어 페이지가 완전히 대화형(interactive)으로 만들어진다.(이 과정을 hydration이라고 한다.)

### 사전 렌더링이 진행되고 있는지 확인하기

1. chrome 개발자도구에서 javascript 비활성화 해보기.
[https://developer.chrome.com/docs/devtools/javascript/disable/](https://developer.chrome.com/docs/devtools/javascript/disable/)
2. 튜토리얼 결과페이지에서 확인
[https://next-learn-starter.vercel.app/](https://next-learn-starter.vercel.app/)

앱이 javascript 없이 렌더링 되는 것을 볼 수 있다.

Next.js가 앱을 정적 HTML로 사전 렌더링하여 javascript를 실행하지 않고도 앱 UI를 볼 수 있기 때문이다.

[https://create-react-template.vercel.app/](https://create-react-template.vercel.app/)

이 페이지는 react로 되어있고, 사전 렌더링을 지원하지 않기 때문에 javascript를 비활성화하면 페이지가 보이지 않는다.

### 두 가지 형태의 사전 렌더링

정적 생성(static generation)은 빌드 시, HTML을 생성한다. 빌드시 생성한 HTML은 사용자 각각의 요청에서 재사용된다. 한번 만든 것을 그대로 보여준다는 뜻이다.

서버 측 렌더링(server side rendering)은 각 요청에 대해 HTML을 생성한다.

개발모드에서는 모든 요청에 대해 페이지가 사전 렌더링 된다. 정적 생성에도 적용된다. (정적 페이지도 요청할때마다 새로 만들어 진다는 뜻이다. 개발모드에서만.)

프로덕션 단계에서 정적 생성은 빌드 시 한 번만 발생한다.

### 사전 렌더링 방식 선택

Next.js에서는 각 페이지에 사용할 사전 렌더링 form을 선택할 수 있다.

대부분의 페이지는 정적 생성을 사용하고, 다른 페이지에는 서버 사이드 렌더링을 사용하여 하이브리드(hybrid) Next.js 앱을 만들 수 있다.

가능하면 정적 생성을 사용하는게 좋다. 서버가 렌더링 하도록 하는 것보다 훨씬 빠르다.

정적 페이지 유형

- 마케팅 페이지
- 블로그 게시물
- 전자상거래 제품 목록
- 도움말 및 문서

스스로에게 물어보기. “사용자가 요청하기 전에 미리 이 페이지를 렌더링 할 수 있습니까?” 대답이 예라면 정적 생성을 선택해야 한다.

페이지에 자주 업데이트되는 데이터가 표시되고, 요청이 있을 때마다 페이지 콘텐츠가 변경되어야 한다면 서버사이드 렌더링을 사용할 수 있다.

속도는 느리지만 사전 렌더링된 페이지는 항상 최신 상태로 유지된다.

또는 사전 렌더링을 생략하고 클라이언트 측 javascrip를 사용하여 자주 업데이트되는 데이터를 채울 수 있다.(페이지 콘텐츠 변경이 있는 페이지라면 클라이언트 측 렌더링도 방법이라는 뜻)

## 정적 생성(Static Generation)

외부 데이터를 가져올 필요가 없는 페이지는 앱이 프로덕션용으로 빌드될 때 자도으로 정적으로 생성된다.

일부 페이지에서는 외부에서 데이터를 먼저 가져오지 않으면 HTML을 렌더링하지 못할 수도 있다.

Next.js는 데이터를 사용한 정적 생성을 기본적으로 지원한다.

### getStaticProps를 사용

getStaticProps를 사용하면 Next.js에게 다음과 같이 말할 수 있다. “이 페이지에는 일부 데이터 종속성이 있으므로 빌드 시 이 페이지를 사전 렌더링할 때 먼저 문제를 해결하세요!”

(참고로 개발 모드에서는 getStaticProps 대신 각 요청에 대해 실행된다)

### getStaticProps로 할 수 있는 것

- 외부 API 가져오기
Next.js에 fetch가 있고, 클라이언트와 서버 모두에서 폴리필을 수행한다. 따로 import 할 필요는 없다.
- 데이터베이스 쿼리
someDatabaseSDK를 사용해서 DB 쿼리를 직접 작성할 수 있다. 서버측에서만 실행된다.
브라우저 js 번들에 포함되지 않아서 브라우저로 전송되지 않는다.

develop 모드에서는 매 요청마다 getStaticProps실행된다.

production에서는 build time에 실행된다. 이 동작은 getstaticPaths에 반환되는 fallback 키를 사용하여 강화(enhance)할 수 있다.

getStaticProps는 페이지에서만 export 할 수 있다.

이러한 제한이 적용되는 이유 중 하나는 페이지가 렌더링되기 전에 React에 필요한 모든 데이터가 있어야하기 때문이다.

getStaticProps는 서버 측에서 실행된다.

## 서버 측 렌더링(Server Side Rendering)

getStaticProps 대신에 getServerSideProps를 export 해야한다.

요청시 데이터를 가져와야하는 페이지를 사전 렌더링해야 하는 경우에만 사용해야 한다.

첫 번째 바이트까지의 시간(TTFB = Time to First Byte, HTTP 요청을 했을 때 처음 byte(정보)가 브라우저에 도달하는 시간)이 더 느려진다.

서버가 모든 요청에 대해 결과를 계산해야하고, 여분의 구성(extra configuration) 없이 CDN에서 결과를 캐시할 수 없기 때문이다.

## 클라이언트 측 렌더링(Client Side Rendering)

데이터를 사전 렌더링 할 필요가 없는 경우 클라이언트 측 렌더링을 사용할 수도 있다.

- 외부 데이터가 필요하지 않은 페이지 부분을 정적으로 생성(사전 렌더링)
- 페이지가 로드되면  javascript를 사용하여 클라이언트에서 외부 데이터를 가져오고 나머지 부분을 채운다.

이 방식은 사용자 대시보드 페이지에 적합하다.

SEO와 관련 없는 페이지에 적합하다.

데이터는 자주 업데이트 되므로 요청 시 데이터를 가져와야 한다.

## SWR

Next.js에서 만든 데이터 가져오기용 React Hook이다.

클라이언트에서 데이터를 가져올 때 swr을 사용하기를 적극 권장한다.

캐싱, 재검증, 포커스 추적, 간격에 따른 다시 가져오기 등을 처리한다.

[https://swr.vercel.app/ko](https://swr.vercel.app/ko)

## 동적 라우팅

/posts/[id].js 파일을 만들면 /posts/1, /posts/2 등의 경로에 접근할 수 있다.

/posts/[id].js getStaticPaths 코드 추가했다.

fallback: false는 알려진 경로 외에는 404가 뜨도록 하는 것([https://nextjs.org/docs/pages/api-reference/functions/get-static-paths#fallback-false](https://nextjs.org/docs/pages/api-reference/functions/get-static-paths#fallback-false) 읽어보기)

```jsx
import Layout from '../../components/layout';
import { getAllPostIds } from '../../lib/posts';

export default function Post() {
    return <Layout>
        ...
    </Layout>
}

export async function getStaticPaths() {
    const paths = getAllPostIds();
    return {
        paths,
        fallback: false
    }
}

export async function getStaticProps({params}) {

}
```

/lib/posts.js

```jsx
export function getAllPostIds() {
    const fileNames = fs.readdirSync(postsDirectory);

    // 다음과 같이 보이는 배열 반환
    // [
    //   {
    //     params: {
    //       id: 'ssg-ssr'
    //     }
    //   },
    //   {
    //     params: {
    //       id: 'pre-rendering'
    //     }
    //   }
    // ]
    return fileNames.map((fileName) => {
        return {
            params: {
                id: fileName.replace(/\.md$/, '')
            }
        }
    })
}
```

### 개발모드 vs 프로덕션 모드

- 개발 모드에서는 모든 요청에서 getStaticPaths가 실행된다.
- 제품 모드에서는 빌드타임에 getStaticPaths가 실행된다.

### getStaticPaths()에서 fallback 리턴

- fallback: false를 리턴하면, getStaticPaths에 있지 않은 경로로 접근 시 404 에러 페이지를 보여준다.
- true를 리턴하면, getStaticPaths로부터 리턴된 경로라면 빌드타임에 렌더링된 HTML을 보여줄 것이다.
빌드 타임에 생성된 페이지가 없다면(경록 유효하지 않다면) Next.js는 페이지의 fallback(대체) 버전을 보여줄 것이다.
백그라운드에서 Next.js는 요청된 경로를 정적으로 생성한다. 그 후에 동일한 경로로 요청된 경우, 빌드 시 사전 렌더링된 다른 페이지와 마찬가지로 생성된 페이지를 제공한다.
요약: fallback: true를 하면 404 페이지 대신에 대체 페이지를 보여준다. 이 페이지는 요청 시에 정적으로 생성되고, 동일한 경로로 또 요청이 오면 이전에 생성한 대체 페이지를 보여준다.
- fallback: blocking을 리턴하면, 새 경로는 서버측 렌더링 되고, 미래의 요청들을 위해 캐시된다. 이 것은 경로당 한 번만 발생한다.

### Catch-all Routes

동적 라우팅은 브라켓([])에 점 세 개(…)를 추가해서 포괄 경로로 확장할 수 있다.

예를들어 pages/posts/[…id].js는 /post/a, /post/a/b, /post/a/b/c 등이 매치된다.

이렇게 하려면 getStaticPaths에서 id를 배열로 리턴하면 된다.

```jsx
return [
  {
    params: {
      // Statically Generates /posts/a/b/c
      id: ['a', 'b', 'c'],
    },
  },
  //...
];
```

getStaticProps에서 params.id는 배열로 들어온다.

```jsx
export async function getStaticProps({ params }) {
  // params.id will be like ['a', 'b', 'c']
}
```

Next.js Document Catch-all Segments 링크

[https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes#catch-all-segments](https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes#catch-all-segments)

pages/shop/[…slug].js를 사용하면 /shop/a, /shop/a/b, /shop/a/b/c 등이 가능하다.

pages/shop/[[…slug]].js를 사용하면 pages/shop/[…slug].js와 동일한 경로를 매치할 수 있고, 추가로  /shop 경로를 매치할 수 있다. params에는 빈배열(slug: [ ])이 들어온다.

## API Routes

/pages/api 디렉토리를 만들고 디렉토리 내부에 다음과 같은 함수를 만들면 된다.

```jsx
// req = HTTP incoming message, res = HTTP server response
export default function handler(req, res) {
  // ...
}
```

서버리스 함수(Lambda 라고도 함)로 배포할 수 있다.

### 간단한 api 엔드포인트 생성

/pages/api/hello.js 파일에 다음 코드를 입력한다.

```jsx
export default function handler(req, res) {
  res.status(200).json({ text: 'Hello' });
}
```

로컬주소/api/hello에 접근하면 {”text”:”Hello”}를 볼 수 있다.

- req는 http.IncomingMessage의 인스턴스와 일부 사전 구축된 미들웨어
- res는 http.ServerResponse의 인스턴스와 일부 헬퍼 함수

## API Route 세부 정보

getStaticProps, getStaticPaths에서 api Route를 fetch 하지 않는게 좋다.

이 코드는 서버측에서만 실행되고, 클라이언트 측에서는 실행되지 않는다. 브라우저용 js 번들에 포함되지 않는다.

즉, 브라우저로 보내지 않고도 직접 데이터베이스 쿼리와 같은 코드를 작성할 수 있다.

좋은 사용 사례는 Form Input 핸들링.

예를들어, 페이지에 form을 만들고, api route에 POST 요청을 보낼 수 있다. 그러면 입력 값을 데이터 베이스에 바로 저장하는 코드를 작성할 수 있다. api Route 코드는 클라이언트 번들의 일부가 되지 않고, 안전하게 서버측 코드로 작성할 수 있다.