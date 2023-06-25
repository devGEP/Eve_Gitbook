# HTML - Form(폼)

* input data를 모아서 한꺼번에 보내주는 역할을 한다.

<figure><img src="../../../.gitbook/assets/스크린샷 2023-06-24 오후 7.11.41.png" alt=""><figcaption><p>input type</p></figcaption></figure>



* Login

```html
<!DOCTYPE html>
<html lang="en">
<!-- metadata, title -->
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<!-- contents -->
<body> 
  <form>
    <h1>Login</h1>
    <label for="username">Username: </label><br>
    <input id="username" type="text" name="username" value="" required>
    <br>

    <label for="pwd">Password: </label><br>
    <input id="pwd" type="password" name="pwd">
    <br>

    <label for="name">Name: </label><br>
    <input id="name" type="text" name="name" placeholder="Write your name">
    <br>

    <button type="submit">Submit</button>
  </form>
</body>
</html>
```

<figure><img src="../../../.gitbook/assets/스크린샷 2023-06-24 오후 7.15.48.png" alt=""><figcaption><p>위 HTML 코드 결과(Form)</p></figcaption></figure>



* Restaurant Booking

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
  <form>
    <h1>Restaurant Booking</h1>
    <label for="position">position</label> <br>
    <input type="radio" id="inside" name="position" value="inside" checked>
    <label for="html">inside</label> <br>
    <input type="radio" id="outside" name="position" value="outside">
    <label for="css">Outside</label> <br>
    <br>

    <label for="reservationDate">Reservation Date: </label>
    <input type="date" id="reservationDate" name="reservationDate">
    <br>
    
    <label for="party">Party: </label>
    <select id="party" name="party" id="party">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="2">3</option>
      <option value="2">4</option>
    </select>
    <br>
    
    <label for="name">Name: </label>
    <input type="text" name="name" id="name">
    <br>

    <label for="phone">Phone: </label>
    <input type="tel" name="phone" id="phone">
    <br>
    
    <button type="submit">Submit</button>
  </form>
</body>
</html>
```

<figure><img src="../../../.gitbook/assets/스크린샷 2023-06-24 오후 7.10.06.png" alt=""><figcaption><p>위 HTML 코드 결과(Form)</p></figcaption></figure>



