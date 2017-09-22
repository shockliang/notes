[Doc Reference](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+MSBuild)

1. 將 C:\SonarQube\bin 設置路徑設為置環境變數

2. 打開 C:\SonarQube\bin\SonarQube.Analysis.xml 文件修下列的參數：
    - `sonar.host.url` => 修改指向建置的 sonarqube web (ex:http://192.168.1.111:9000)
    - `sonar.login` => 填入指定帳號的 `Token`。該帳號必需有能夠執行 "Execute Analysis" 權限。這個 Token 只有在建立的時候才能夠看得到一次。因此要備份該 Token 。或是重新建 Token.

3. 使用管理者身份開 cmd 視窗。分別執行下列的指令
  參數說明：

  `/k (/key)`:要被分析的專案的唯一辨別碼，在 sonar web 專案中可以找到。

  `/n`:專案名稱 (可忽略)

  `/v`:版本 (可忽略)

``` bat
SonarQube.Scanner.MSBuild.exe begin /k:"org.sonarqube:sonarqube-scanner-msbuild" /n:"Project Name" /v:"1.0"

MSBuild.exe /t:Rebuild

SonarQube.Scanner.MSBuild.exe end
```
