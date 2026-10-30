---
date: 2026-10-30
authors:
  - lee
categories:
  - Javascript
  - 编程
tages:
  - Web Worker
---
# 简单的WEB Worker示例代码

- Requirement: a web server, here we use python to run a simple web server

  ```bash
  python.exe -m SimpleHTTPServer 8080
  #For python3, use this command:
  python3 -m http.server 8080
  ```
- The HTML, master code

  ```xml
  <!DOCTYPE HTML>
  <html>
  	<head>
  		<title>WEB Workder DEMO</title>
  		<script>
  		//Add version number to avoid cache;
  		let workerJsFile = "worker.js?version=" + Date.now();
  		var worker = new Worker(workerJsFile);

  		worker.onmessage = function (event) {
  		    document.getElementById("content").innerHTML = event.data;
  		};

  		function startTimer() {
  		    worker.postMessage("START");
  		}

  		function stopTimer() {
  		    worker.postMessage("STOP");
  		}
  </script>
  		<style type="text/css">
  table {
  	border-collapse:true;
  	border-spacing:0;
  }
  thead {
  	background: lightgray;
  }
  </style>
  	</head>
  	<body>
  		<table border="1px">
  			<thead>
  				<tr>
  					<th>Current Time</th>
  				</tr
  			</thead>
  			<tbody>
  				<tr>
  					<td id="content">

  </td>
  				</tr>
  			</tbody>
  			<tfoot>
  				<tr>
  					<td>
  						<button id="start" onclick="startTimer()">Start</button>
  						<button id="stop" onclick="stopTimer()">Stop</button>
  					</td>
  				</tr>
  			</tfoot>
  		</table>
  	</body>
  </html>
  ```
- The JS, worker code

  ```javascript
  var runningTimer;
  //await must in async function;
  async function report() {
      //const d = new Date();
      var strPost = "Fail to get time from server";
      const timeServer = "http://worldtimeapi.org/api/timezone/Asia/Shanghai";
      let response = await fetch(timeServer);
      if (response.ok) {
          let json = await response.json();
  		strPost=json.datetime;
      }
      this.postMessage(strPost);
  }
  this.addEventListener("message", function (event) {
      let cmd = event.data;
      switch (cmd) {
      case "START":
          this.postMessage("Started");
          clearInterval(runningTimer);
          runningTimer = setInterval(report, 1000);
          break;
      case "STOP":
          this.postMessage("Stopped");
          clearInterval(runningTimer);
          break;
      }

  }, false);
  ```