프로젝트에서 공통으로 필요한 부분들을 설정

# 1. 폰트 적용

- React에서 폰트 변경하는 2가지 방법

## 1.1. 링크를 이용한 방법

- 구글 WebFont를 `<link>` 태그를 이용해서 사용
- [Google Font](https://fonts.google.com/knowledge)에서 마음에 드는 폰트 선택 후 우측에 `<head>` 태그 안에 붙여 넣을 수 있는 `<link>` 태그를 복사
    
- React 프로젝트 폴더에서 `pubilc/index.html` 파일의 `<head>` 태그 내부에 붙여넣기 후 `.css` 파일에서 `font-family` 속성을 사용해서 폰트 적용
    
    ```css
    body {
    	font-family: 'Noto Sans KR', sans-serif;
    }
    ```
    

## 1.2 폰트 파일을 업로드 하는 방법

- `@font-face`를 이용해서 폰트 파일을 적용
- 사용할 폰트를 다운로드 후 React 프로젝트에서 `src/fonts` 디렉토리 생성 후 폰트 파일을 붙여넣기하고 `font.css` 파일 생성
    
    ```css
    @font-face {
        font-family: "MaplestoryOTFBold";
    
        src: url("./Maplestory_OTF_Bold.ttf") format("truetype"),
            url("./Maplestory_OTF_Bold.woff") format("woff"),
            url("./Maplestory_OTF_Bold.eot?iefix") format("embedded-opentype");
    
        font-weight: bold;
    }
    
    @font-face {
        font-family: "MaplestoryOTFLight";
    
        src: url("./Maplestory_OTF_Light.ttf") format("truetype"),
            url("./Maplestory_OTF_Light.woff") format("woff"),
            url("./Maplestory_OTF_Light.eot?iefix") format("embedded-opentype");
    }
    ```
    
- `index.js`에서 `font.css` 파일 `import` 이후 사용
    
    ```jsx
    import './fonts/font.css'
    ```
    

[3 quick ways to add fonts to your React app](https://blog.greenroots.info/3-quick-ways-to-add-fonts-to-your-react-app)

[티스토리 폰트 글씨체 바꾸는 법(feat.눈누)](https://gootart003.tistory.com/entry/%ED%8B%B0%EC%8A%A4%ED%86%A0%EB%A6%AC-%ED%8F%B0%ED%8A%B8-%EA%B8%80%EC%94%A8%EC%B2%B4-%EB%B3%80%EA%B2%BD-%EB%B0%A9%EB%B2%95-%EB%88%88%EB%88%84-%EC%9B%B9%ED%8F%B0%ED%8A%B8-%EC%82%AC%EC%9A%A9)

[리액트 프로젝트에 폰트추가](https://flamingotiger.github.io/frontend/react/react-add-font/)

# 2. Image Assets

- 이미지를 React 프로젝트의 `public/assets` 디렉토리 아래에 복사 붙여넣기
- 이후 Javascript에서 `process.env.PUBLIC_URL`로 `public` 디렉토리의 경로를 가져와서 `/assets/image.png` 이미지 상대 경로와 더해서 이미지를 로딩
    
    ```jsx
    <img src={process.env.PUBLIC_URL + '/assets/image.png'} alt={"image"} />
    ```