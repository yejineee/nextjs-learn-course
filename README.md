# 🍎 Pages

컴포넌트의 이름은 아무거나 다 괜찮다.

다만, 반드시 export default로 모듈을 내보내야 한다. 

페이지는 pages 디렉토리에서 생성하면 되고, pages부터 file의 Path 구조가 url path가 된다.

페이지가 pages/hello/first-post.js에 있다면, url path는 /hello/first-post가 된다.


## Client-side Navigation

Next.js 애플리케이션 내부의 페이지로 이동할 때는 `<Link>` 컴포넌트로 이동해야 한다. 

이를 **client-side navigation**이라고 하는데, 클라이언트 네비게이션은 자바스크립트를 사용하여 페이지 전환이 이루어진다는 것을 뜻한다.

이는 브라우저가 수행하는 페이지 네비게이션보다 더 빠르다.



```javascript
import Link from 'next/link';

export default function FirstPost(){
  return (
  <>
    <h1>First Post</h1>
    <Link href="/">
      <a>Back to Home</a>
    </Link>
  </>)
}
```

## Code Splitting 

Next.js는 코드 스플리팅을 자동으로 해준다. 따라서 페이지는 그 페이지에서 필요한 코드만을 로드하게 된다.

이는 홈페이지가 렌더링되었을 때, 다른 페이지를 위한 코드는 초기에 서빙되지 않는다는 것을 의미한다.

- 빠른 로드 타임
  이를 통해 홈페이지는 수백개의 페이지가 있다 하더라도, 빠르게 로드될 수 있다.

- 독립적인 페이지
  코드스플리팅이 되었따는 의미는, 유저가 요청한 페이지에 대한 코드만 로딩한다는 것은, 각 페이지가 분리(isolate)된다는 의미이다. 

  따라서, 특정 페이지가 에러를 던지더라도, 애플리케이션의 나머지 부분은 제대로 동작할 수 있다.

## Prefetching

Next.js의 프로덕션 빌드에서는, 브라우저에 `Link` 컴포넌트가 보이면 Next.js가 자동으로 연결된 페이지의 코드를 **prefetch**하게 된다. 

따라서, 페이지 링크를 클릭하면, 해당 페이지의 코드는 이미 백그라운드에서 로드가 되어있고, 페이지 전환은 즉각적으로 이루어진다.

# 🍎 Assets, Metadata and CSS

## Assets

이미지와 같은 정적 asset들은 `public` 디렉토리에서 서빙된다. public 폴더는 robots.txt에 유용하다. 

### Image component and Image Optimization

- 기본적으로 이미지 최적화를 지원한다.
  - resizing, optimizing
- Optimize images on-demand
  - 빌드타임에 최적화를 하는 것이 아니기 때문에, 빌드 시간이 증가하지 않는다.
- lazy loaded images
  - 이미지는 기본적으로 레이지 로딩된다. 
  - 이미지가 보여지는 뷰포트로 진입했을 때, 로딩된다. 따라서, 뷰포트를 벗어난 이미지때문에 페이지의 속도가 느려지지 않는다.
  

## Metadata

metadata를 변경하려면, `netxt/head` 모듈의 `<Head>` 컴포넌트를 사용하면 된다.

```javascript
import Head from 'next/head'

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
    </>
  )
}
```

## CSS Styling

- nextjs에서는 `styled-jsx`를 지원한다.
  - 다른 css-in-js 라이브러리를 사용할 수도 있다.
- `CSS`와 `sass`를 지원한다.
  - .css 파일이나 .scss 파일을 불러올 수 있게 한다.
  - tailwind css 또한 지원한다.
- `CSS Module`을 지원한다.

### Layout Component

Layout 컴포넌트는 모든 페이지에서 공유할 수 있는 컴포넌트이다.

#### CSS Module로 컴포넌트에 스타일 적용하기

- CSS Module을 사용하려면 css파일은 반드시 `.module.css`로 끝나야 한다.
- CSS Module은 빌드 타임에 자바스크립트 번들에서 분리되어, `.css` 파일로 생성되고, Next.js에 의해 자동으로 로드된다.

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

css 파일을 styles로 불러온 후, className에 styles.container를 사용한다.

```javascript
import styles from './layout.module.css';

export default function Layout({children}){
  
  return <div className={styles.container}>
    {children}
    </div>
}
```

이렇게 하면, 클래스 명은 `layout_container__...`이 된다.

CSS Module은 자동으로 유니크한 클래스 이름을 만들어준다. 따라서 클래스 명이 충돌할 걱정을 하지 않아도 된다.

### Global Styles

전체 페이지에 적용시킬 global css는 `pages/_app.js`에서 적용할 수 있다.

- App 컴포넌트

  - App 컴포넌트는 최상위 컴포넌트로서, 모든 페이지에 공통적으로 적용되는 페이지이다.
  - 페이지 네비게이션이 이루어지더라도 유지되어야 하는 state는 App 컴포넌트에 사용할 수 있다. 

  global style 파일을 아무데나 만들고, pages/_app.js에 불러오면 된다.

```javascript
import '../styles/global.css';

export default function App({Component, pageProps}){
return <Component {...pageProps} />
}
```

# 🍎 Prerendering and Data Fetching

**디폴트로, Next.js는 모든 페이지를 pre-render**한다.

이는 Next.js가 미리 각 페이지의 HTML 파일을 생성한다는 것이다. 

Pre-rendering을 하면 성능과 SEO에 더 좋다.

Next.js에서는 **Static Generation**과 **Server-side Rendering**의 형태로 프리렌더링을 지원한다.

둘의 차이는 **언제** 페이지의 HTML이 생성되냐이다. 

![](https://nextjs.org/static/images/learn/data-fetching/static-generation.png)

![](https://nextjs.org/static/images/learn/data-fetching/server-side-rendering.png)

- **Static Generation**
  - HTML은 **build-time**에 생성되고, 각 요청마다 해당 페이지가 사용자에게 보여진다.
  - 정적 렌더링 방식은 한 번만 빌드되고, CDN에 의해 계속해서 서빙될 수 있기 떄문에, 서버사이드렌더링보다 더 빠르다.
  - 유저 요청 이전에 페이지를 만들 수 있다면, 정적 렌더링을 할 것을 추천한다.

- **Server-side Rendering**
  - HTML은 **요청이 발생할 떄마다 생성**된다.
  - 유저의 요청 전에 페이지를 만들 수 없는 경우에는 서버사이드 렌더링을 사용하자. pre-rendered 페이지는 항상 최신일 것이다.
  
Next.js는 각 페이지마다 어떤 pre-rendering 방식을 할 것인지를 선택할 수 있다.


## Static Generation with Data 

### getStaticProps

페이지가 빌드 시간에 외부의 데이터를 fetch해야한다면, 페이지 컴포넌트를 getStaticProps라고 불리는 async 함수를 같이 export해야 한다.

- getStaticProps는 production 모드에서는 build 타임에 실행된다. fallback key를 잘 활용하자.
- getStaticProps는 development 모드에서는 매 요청마다 실행된다.
- 함수 내에서는 외부의 data를 fetch하고, 페이지의 props로 전달한다.
- getStaticProps에서는 외부의 API로 데이터를 가져오는 것이나, 데이터베이스에 직접 쿼리를 호출하는 것도 가능하다.
  - gtetStaticProps는 **Server-side**에서 동작하기 떄문이다. 
- getStaticProps는 **page에서만 export**될 수 있다.

# 🍎 Dynamic Routes

![](https://nextjs.org/static/images/learn/dynamic-routes/how-to-dynamic-routes.png)