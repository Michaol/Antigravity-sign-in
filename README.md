# 解决Antigravity登录问题

打开第一个powershell窗口
···
cd "C:\Users\<username>\AppData\Local\Programs\Antigravity"
···

Debug模式运行antigravity
···
.\Antigravity.exe --inspect=9229
···

按需设置并继续，直到点击“Sign in with Google”（确保只点击1次，因为点一次端口随机变），同时powershell窗口见到以下：
```
[Auth] Localhost server listening on port 11819
```

打开第二个powershell窗口
···
$port = Read-Host "Enter the port from the log"

$redirect = [uri]::EscapeDataString("http://localhost:$port/oauth-callback")

$scope = [uri]::EscapeDataString(
    "https://www.googleapis.com/auth/cloud-platform " +
    "https://www.googleapis.com/auth/userinfo.email " +
    "https://www.googleapis.com/auth/userinfo.profile " +
    "https://www.googleapis.com/auth/cclog " +
    "https://www.googleapis.com/auth/experimentsandconfigs"
)

$clientId = "1071006060591-tmhssin2h21lcre235vtolojh4g403ep.apps.googleusercontent.com"

$state = [uri]::EscapeDataString([guid]::NewGuid().ToString())

$url = "https://accounts.google.com/o/oauth2/v2/auth" +
       "?client_id=$clientId" +
       "&redirect_uri=$redirect" +
       "&response_type=code" +
       "&scope=$scope" +
       "&access_type=offline" +
       "&prompt=consent" +
       "&state=$state"

Write-Host "Opening browser..." -ForegroundColor Green
Start-Process $url
···

复制上面的代码并在 PowerShell 中运行。
当屏幕显示 Enter the port from the log: 时，输入 11819（或者你日志里显示的那个端口号）
按回车，浏览器将会弹出 Google 登录界面

如果弹窗失败，补一个命令
···
Write-Host $url
···
powershell会输出整个验证网址包含随机id，直接复制到默认浏览器打开，就会见到验证页面。因为上面的命令已经处理过callback，所以应该不会因此不能跳回验证成功。

此方法个人测试大陆白名单就能通过，不需要特别设置。
