# HTML을 CSS로 꾸며보자 - 파트2(Font)

* em: 현재 정해져있는 폰트 사이즈에서 크거나 작게 할 수 있다.
  * 1em: 똑같은 크기
  * 2em: 현재 사이즈에서 2배가 큰 크기

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="advanced.css">
</head>
<body>
    Here is body
    <p>Hello world</p>
</body>
</html>
```

```css
body {
    font-size: 30px;
}

@font-face {
    font-family: Roboto-Black;
    src: url('Roboto-Black.ttf') format('truetype')
}

p {
    font-family: Roboto-Black;
    font-size: 2em; /* 30 * 2인 크기 */
}
```



<figure><img src="../../../.gitbook/assets/스크린샷 2023-06-24 오후 7.00.07.png" alt=""><figcaption><p>위 HTML 코드 결과(CSS2)</p></figcaption></figure>









