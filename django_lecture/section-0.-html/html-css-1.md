# HTML을 CSS로 꾸며보자 - 파트1



```html
<!DOCTYPE html>
<html lang="en">
<!-- metadata, title -->
<head>
  <meta charset="UTF-8">
  <title>This is title</title>
  <link rel="stylesheet" href="style.css">
</head>
<!-- contents -->
<body> 
  <h1>Hello World</h1>
  <p>This is test</p>
  <h2>This is sibling</h2>

  <div class="outer">
    outer
    <div class="inner">
      inner
    </div>
    <p>paragraph</p>
  </div>
  
  <div id="smaller">
    Smaller
  </div>

  <ul>
    <li><a href="">One</a></li>
    <li>Two</li>
    <li>Three</li>
  </ul>
</body>
</html>
```

```css
/* Here is CSS comment */

/* select tag{
    property: value;
} */

h1 {
    color: red;
    text-decoration: underline;
}

p {
    font-size: 50px;
}

/* class는 이렇게 .을 붙인다. */
.outer {
    background-color: orange;
    border-style: dashed;
}

.inner {
    background-color: yellow;
    border-width: 5px;
    border-style: double;
}

/* outer라는 class 안에 p라는 태그가 존재하면, 아래를 실행한다. */
.outer > p {
    font-size: 30px;
}

/* #모양은 id */
#smaller {
    color: green;
}

/* +는 sibling이라고 해서, p 다음에 h2가 왔을 때, 이 걸 적용한다. */
p + h2 {
    color: blue;
}

/* ul 안에 li 안에 a 가 있다면 이렇게 바꿔라. */
ul li a {
    color: violet;
}
```



<figure><img src="../../.gitbook/assets/스크린샷 2023-06-24 오후 7.02.26.png" alt=""><figcaption><p>위 HTML 코드 결과(CSS1)</p></figcaption></figure>



* **CSS Specitificity**
  * CSS가 여러개가 있을 때, 어떤 CSS를 HTML에 적용하는지 중요도에 따라 나눠져있다.
  * **id > class > type > No value**



