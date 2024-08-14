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