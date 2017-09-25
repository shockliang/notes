### Base Setup ###

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

### Integration with GitLab CI runner ###
1. 新增名為`analysis`的 stage 至 .gitlab-ci.yml 中。
2. 以呼叫 bat 的方式放至 script section 中，使用方式跟 base setup 的方式一樣。其中`SonarQube.Scanner.MSBuild.exe` 已在 base setup 時被為環境變數的 path 之中。
3. 參數說明：
  * `/key`: SonarQube上專案的唯一key
  * `/d:sonar.host.url`: 指向建置的 SonarQube websit url.
  * `/d:sonar.login`: 用於登入 SonarQube 的 token. 若不想將該 secret 保存在 runner 之中則可以在 `GitLab Project > SettingS > CI/CD` 的頁面中的 `Secret variables` 將 token 存在 GitLab 中。當在跑 CI 時，GitLab CI 會將 `SONAR_AUTH_TOKEN` 傳入至 runner 的環境變數之中。這樣就可以避免敏感的資訊曝露出來。
  ![GitLab secret values](/images/gitlab-secret-values.png)


```yml
variables:
  SONAR_HOST_URL: 'http://192.168.100.111:9000'

stages:
  - build
  - test
  - analysis

sonarqube:
  stage: analysis
  script:
    - SonarQube.Scanner.MSBuild.exe begin
        /key:Shock-WebApplication
        /d:sonar.host.url=%SONAR_HOST_URL%
        /d:sonar.login=%SONAR_AUTH_TOKEN%"
    - MSBuild.exe /t:Rebuild
    - SonarQube.Scanner.MSBuild.exe end
```
