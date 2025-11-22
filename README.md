解决 Antigravity Google 登录失败的详细指南
如果在使用 Antigravity 时点击 "Sign in with Google" 没有反应或无法跳转，请按照以下步骤手动完成授权。

步骤 1：启动 Antigravity 调试模式
打开第一个 PowerShell 窗口。

进入程序目录（请将 <username> 替换为您的实际 Windows 用户名）：

PowerShell

cd "C:\Users\<username>\AppData\Local\Programs\Antigravity"
使用调试端口启动程序：

PowerShell

.\Antigravity.exe --inspect=9229
在弹出的 Antigravity 窗口中，点击 "Sign in with Google"。

注意：只点击 1次。每次点击都会随机生成新的监听端口。

回到 PowerShell 窗口，寻找类似以下的日志输出，并记下端口号（例如 11819）：

[Auth] Localhost server listening on port 11819

步骤 2：生成授权链接
保持第一个窗口不动，打开 第二个 PowerShell 窗口。

复制并运行以下完整脚本：

PowerShell

# 1. 输入端口号
$port = Read-Host "Enter the port from the log"

# 2. 设置回调地址
$redirect = [uri]::EscapeDataString("http://localhost:$port/oauth-callback")

# 3. 设置 Scope
$scope = [uri]::EscapeDataString(
    "https://www.googleapis.com/auth/cloud-platform " +
    "https://www.googleapis.com/auth/userinfo.email " +
    "https://www.googleapis.com/auth/userinfo.profile " +
    "https://www.googleapis.com/auth/cclog " +
    "https://www.googleapis.com/auth/experimentsandconfigs"
)

# 4. 设置 Client ID
$clientId = "1071006060591-tmhssin2h21lcre235vtolojh4g403ep.apps.googleusercontent.com"

# 5. 生成随机 State
$state = [uri]::EscapeDataString([guid]::NewGuid().ToString())

# 6. 拼接最终 URL
$url = "https://accounts.google.com/o/oauth2/v2/auth" +
       "?client_id=$clientId" +
       "&redirect_uri=$redirect" +
       "&response_type=code" +
       "&scope=$scope" +
       "&access_type=offline" +
       "&prompt=consent" +
       "&state=$state"

# 7. 尝试打开浏览器
Write-Host "Opening browser..." -ForegroundColor Green
Start-Process $url
步骤 3：完成验证
脚本运行后会提示：Enter the port from the log:。

输入步骤 1 中获取的端口号（例如 11819）并回车。

此时默认浏览器应自动弹出 Google 登录界面。

登录并点击允许，授权完成后浏览器通常会跳转或显示无法连接（这是正常的），此时 Antigravity 客户端应已成功登录。

常见问题处理
如果运行脚本后没有自动弹出浏览器，请在第二个 PowerShell 窗口中继续输入以下命令：

PowerShell

Write-Host $url
PowerShell 会打印出完整的长链接。请复制该链接手动粘贴到浏览器地址栏访问即可。

提示：此方法因使用系统默认浏览器进行验证，经测试在部分网络受限环境（如大陆白名单模式）下也能顺利通过，无需额外配置代理。
