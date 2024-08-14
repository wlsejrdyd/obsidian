---

mindmap-plugin: basic

---

# nodejs01

## version
- v16.20.2

## 목적
- nodejs 공부 및 exec 함수를 통한 서버 스크립트 결과 값 출력

## v1
- index.js

	-
	  ```
	  
	  const express = require('express');
	  const app = express();
	  const port = 3000;
	  const { exec } = require('child_process');
	  
	  
	  app.use(express.static('public'));
	  
	  
	  // Home 페이지 라우트
	  app.get('/', (req, res) => {
	  res.sendFile(__dirname + '/public/home.html');
	  });
	  
	  
	  // About 페이지 라우트
	  app.get('/about', (req, res) => {
	  res.sendFile(__dirname + '/public/about.html');
	  });
	  
	  
	  // Product 페이지 라우트
	  app.get('/product', (req, res) => {
	  res.sendFile(__dirname + '/public/product.html');
	  });
	  
	  
	  //// API 엔드포인트 - 제품 정보 제공
	  //app.get('/api/product', (req, res) => {
	  //  // 임의의 제품 데이터를 생성하여 반환
	  //  const productData = {
	  //    name: 'Sample Product',
	  //    price: '$19.99'
	  //  };
	  //  res.json(productData);
	  //});
	  
	  
	  // API 엔드포인트 - 쉘 스크립트를 실행하여 현재 시간 제공
	  app.get('/api/time', (req, res) => {
	  exec('./get_time.sh', (error, stdout, stderr) => {
	  if (error) {
	  console.error(`exec error: ${error}`);
	  return res.status(500).send('Server error');
	  }
	  res.json({ currentTime: stdout.trim() });
	  });
	  });
	  
	  
	  app.listen(port, () => {
	  console.log(`Example app listening at http://localhost:${port}`);
	  });
	  ```

		- home.html

			-
			  ```
			  <!DOCTYPE html>
			  <html>
			  <head>
			  <title>Home Page</title>
			  </head>
			  <body>
			  <h1>Welcome to Our Website</h1>
			  <nav>
			  <ul>
			  <li><a href="/">Home</a></li>
			  <li><a href="/about">About Us</a></li>
			  <li><a href="/product">Our Products</a></li>
			  </ul>
			  </nav>
			  <p>This is the Home page. Explore our website to learn more about us!</p>
			  </body>
			  </html>
			  ```

		- about.html

			-
			  ```
			  
			  <!DOCTYPE html>
			  <html>
			  <head>
			  <title>Home Page</title>
			  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
			  
			  
			  </head>
			  
			  
			  <script>
			  $(document).ready(function() {
			  $("#showPopup").click(function() {
			  $("#popup").show();
			  });
			  
			  
			  $("#closePopup").click(function() {
			  $("#popup").hide();
			  });
			  });
			  </script>
			  
			  
			  
			  
			  <body>
			  <h1>Welcome to Our Website</h1>
			  <p>This is the Home page. Explore our website to learn more about us!</p>
			  <button id="showPopup">Show Popup</button>
			  <div id="popup" style="display:none; position: fixed; left: 50%; top: 50%; transform: translate(-50%, -50%); padding: 20px; background-color: white; border: 2px solid #000; z-index: 1000;">
			  <p>This is a popup!</p>
			  <button id="closePopup">Close</button>
			  </div>
			  
			  
			  </body>
			  </html>
			  ```

		- product.html

			-
			  ```
			  
			  <!DOCTYPE html>
			  <html lang="en">
			  <head>
			  <meta charset="UTF-8">
			  <meta name="viewport" content="width=device-width, initial-scale=1.0">
			  <title>Product</title>
			  <script>
			  // 5초마다 페이지 새로고침
			  setTimeout(() => {
			  window.location.reload();
			  }, 5000);
			  
			  
			  // 스크립트로 서버에서 시간을 가져와서 출력하는 함수
			  function loadTime() {
			  fetch('/api/time')
			  .then(response => response.json())
			  .then(data => {
			  document.getElementById('current-time').innerText = `Current Time: ${data.currentTime}`;
			  })
			  .catch(error => console.error('Error:', error));
			  }
			  
			  
			  window.onload = loadTime;
			  </script>
			  </head>
			  <body>
			  <h1>Current Server Time</h1>
			  <div id="current-time">Loading...</div>
			  <a href="/">Home</a>
			  <a href="/about">About</a>
			  </body>
			  </html>
			  ```