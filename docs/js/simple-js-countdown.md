# 简单的js倒计时代码

```javascript
$(function () function countdown(dateString, callback) 
 var timestamp = Date.parse(dateString), timer;
 function timeDiff() 
 var t = timestamp - (new Date).getTime(),
 d = time: t, day: '00', hour: '00', min: '00', sec: '00',
 s = '';
 if (t > 0) 
 s = '0' + parseInt(t / 1000 % 60);
 d.sec = s.substr(s.length - 2); //秒
 s = '0' + parseInt(t / 60000 % 60);
 d.min = s.substr(s.length - 2); //分
 s = '0' + parseInt(t / 3600000 % 24);
 d.hour = s.substr(s.length - 2); //时
 s = '0' + parseInt(t / 86400000 % 30);
 d.day = s.substr(s.length - 2); //天
 
 return d;
 
 timer = setInterval(function () 
 var result = timeDiff();
 result.time > 0 ? callback(result) : clearInterval(timer);
 , 1000);
 callback(timeDiff());
 
 var day = $('#theDay'), hour = $('#theHour'), min = $('#theMin'), sec = $('#theSec'),
 endTime = '2012/12/21 00:00:00';
 countdown(endTime, function (d) 
 day.text(Number(d.day));
 hour.text(d.hour);
 min.text(d.min);
 sec.text(d.sec);
 );
); 
 
```