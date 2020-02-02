# Webhacking18
워게임 18번문제 풀이입니다.

# 18번 문제 풀이

## 소스코드
```javascript
<?php
  include "../../config.php";
  if($_GET['view_source']) view_source();
?><html>
<head>
<title>Challenge 18</title>
<style type="text/css">
body { background:black; color:white; font-size:10pt; }
input { background:silver; }
a { color:lightgreen; }
</style>
</head>
<body>
<br><br>
<center><h1>SQL INJECTION</h1>
<form method=get action=index.php>
<table border=0 align=center cellpadding=10 cellspacing=0>
<tr><td><input type=text name=no></td><td><input type=submit></td></tr>
</table>
</form>
<a style=background:gray;color:black;width:100;font-size:9pt;><b>RESULT</b><br>
<?php
if($_GET['no']){
  $db = dbconnect();
  if(preg_match("/ |\/|\(|\)|\||&|select|from|0x/i",$_GET['no'])) exit("no hack");
  $result = mysqli_fetch_array(mysqli_query($db,"select id from chall18 where id='guest' and no=$_GET[no]")); // admin's no = 2

  if($result['id']=="guest") echo "hi guest";
  if($result['id']=="admin"){
    solve(18);
    echo "hi admin!";
  }
}
?>
</a>
<br><br><a href=?view_source=1>view-source</a>
</center>
</body>
</html>
```

## 접근
1. 빈칸에 값을 입력하면 ?no=[입력한 값] 으로 이동함.
2. 소스코드 확인했을때 / |\/|\(|\)|\|| 입력시 종료
3. select id from chall18 where id='guest' and no=$_GET[no] 분석했을때 chall18이라는 테이블에 id가 guest인 데이터가 존재한다. 주석에는 admin의 no는 2인것이 확인됨.
4. guest의 no는 1인걸로 유추가능하다. 1을 넣었더니 hi guest가 출력된다.
5. 로그인에 성공하려면 admin으로 로그인을 해야한다.
6. 여기서 sql injection을 이용해서 no=[ ] 자리에 admin으로 로그인할 수 있도록 해야한다
7. sql문이 select id from chall18 where id='guest' and no=[0 or no=2]이 되도록 해야한다. 이렇게 만들면 (id=‘guest’ and no=0)[id가 guset && no=0] or (no=2) 이 되기때문에 guest의 no은 0이 아니어서 no=2인 id admin으로 들어가진다.
8. 그래서 [0 or no=2] 이 정답인데, 공백은 공백우회를 해야한다.
9. 그리고 빈칸에 입력하면 %를 자동으로 변환시켜 들어가기 때문에 주소창에 직접 넣어야한다.
