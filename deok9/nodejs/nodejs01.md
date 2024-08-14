---

mindmap-plugin: basic

---

# nodejs01

## version
- v16.20.2

## 목적
- v1 : nodejs 공부 및 exec 함수를 통한 서버 스크립트 결과 값 출력
v2 : wget 에서 각자 다른 서버에서 웹서버로 데이터 전송하는 기능을 추가하고, shell script 업로드 할 수 있는 페이지를 새로 만듦.

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
	  // // 임의의 제품 데이터를 생성하여 반환
	  // const productData = {
	  // name: 'Sample Product',
	  // price: '$19.99'
	  // };
	  // res.json(productData);
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


## Dockerfile

-
  ```
  FROM node
  
  
  WORKDIR /app/node01
  
  
  COPY package.json .
  
  
  RUN npm install
  
  
  COPY . .
  
  
  EXPOSE 3000
  
  
  CMD [ "node", "index.js" ]
  ```


## kubernetes
- Sub title
	- deployment yaml

		-
		  ```
		  kubectl create deployment node01-deploy --image=localhost/node01:latest --replicas=2 --dry-run=client -o yaml > node01.yaml
		  ```

- Sub title
	- service

		-
		  ```
		  kubectl expose deployment node01-deploy --name=node01-svc --type=NodePort --port=3000 --target-port=3000
		  ```


## v2
- isnta.js

	-
	  ```
	  const express = require('express');
	  const app = express();
	  const multer = require('multer');
	  const path = require('path');
	  const port = 80;
	  const { exec } = require('child_process');
	  
	  
	  app.use(express.static('public'));
	  
	  
	  // Multer 설정: 파일을 저장할 위치와 파일명 지정
	  const storage = multer.diskStorage({
	  destination: function (req, file, cb) {
	  cb(null, 'uploads/'); // 업로드된 파일을 저장할 폴더
	  },
	  filename: function (req, file, cb) {
	  cb(null, file.originalname); // 원래 파일명을 사용하여 저장
	  }
	  });
	  
	  
	  const upload = multer({ storage: storage });
	  
	  
	  // 서버별 데이터를 저장할 객체
	  let serverData = {};
	  
	  
	  // Home 페이지 라우트
	  app.get('/', (req, res) => {
	  res.sendFile(__dirname + '/public/home.html');
	  });
	  
	  
	  // Product 페이지 라우트
	  app.get('/product', (req, res) => {
	  res.sendFile(__dirname + '/public/product.html');
	  });
	  
	  
	  // 파일 업로드를 위한 HTML 폼을 제공하는 라우트
	  app.get('/upload', (req, res) => {
	  res.send(`
	  <html>
	  <body>
	  <h1>Upload Shell Script</h1>
	  <form action="/upload" method="POST" enctype="multipart/form-data">
	  <input type="file" name="scriptFile" accept=".sh" />
	  <button type="submit">Upload</button>
	  </form>
	  </body>
	  </html>
	  `);
	  });
	  
	  
	  // 파일 업로드를 처리하는 라우트
	  app.post('/upload', upload.single('scriptFile'), (req, res) => {
	  if (req.file) {
	  res.send(`File uploaded successfully: ${req.file.originalname}`);
	  } else {
	  res.status(400).send('No file uploaded.');
	  }
	  });
	  
	  
	  // 업로드된 파일을 서빙하기 위한 정적 파일 제공
	  app.use('/uploads', express.static(path.join(__dirname, 'uploads')));
	  
	  
	  // Update 데이터 저장 라우트
	  app.get('/update', (req, res) => {
	  const serverId = req.query.id;  // 서버 식별자
	  const data = req.query.data;    // 전송된 데이터
	  
	  
	  if (serverId && data) {
	  // 서버 ID에 해당하는 데이터 저장
	  serverData[serverId] = data;
	  console.log(`Data received from server ${serverId}: ${data}`);
	  res.send(`Data from server ${serverId} has been updated.`);
	  } else {
	  res.status(400).send('Missing server ID or data.');
	  }
	  });
	  
	  
	  // 모든 서버의 데이터를 출력하는 라우트
	  app.get('/display', (req, res) => {
	  // 저장된 데이터를 출력하는 HTML 페이지 반환
	  let html = `
	  <html>
	  <head>
	  <meta http-equiv="refresh" content="5"> <!-- 5초마다 페이지 새로고침 -->
	  </head>
	  <body>
	  <h1>Server Data</h1>
	  `;
	  
	  
	  // 서버별 데이터 출력
	  for (const [serverId, data] of Object.entries(serverData)) {
	  html += `
	  <h2>Server ID: ${serverId}</h2>
	  <pre>${data}</pre>
	  `;
	  }
	  
	  
	  html += `
	  </body>
	  </html>
	  `;
	  
	  
	  res.send(html);
	  });
	  
	  
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

		- pre 태그를 사용해서 문자 그대로 가져오게 함. (p 태그에서 변경.)