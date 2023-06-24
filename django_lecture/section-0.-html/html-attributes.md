# HTML - Attributes(속성)

* src: source
* alt: source가 존재하지 않을 경우, 이게 나오게 된다.
* href: 목적지 페이지로 이동(링크: 절대링크, 파일: 상대링크 가능)
* target: **'\_blank'**라는 속성을 주게 되면, 클릭했을 때, 페이지가 새 탭에서 열리게 된다.



```html
<!DOCTYPE html>
<html lang="en">
<!-- metadata, title -->
<head>
  <meta charset="UTF-8">
  <title>This is title</title>
</head>
<!-- contents -->
<body> 
  <h1>Image</h1>
  <!-- <div>
    <img src="tesla.jpeg" alt="tesla image">
  </div> -->

  <div>
    <!-- a는 하이퍼링크 제공 -->
    <a href="page1.html">Click here</a>
  </div>

  <div>
    <a href="https://google.com">Redirect to Google</a>
  </div>
</body>
</html>
```

<figure><img src="../../.gitbook/assets/스크린샷 2023-06-24 오후 7.21.59.png" alt=""><figcaption><p>위 HTML 코드 결과(Attributes, img 제외)</p></figcaption></figure>
