## <a name="rest"></a>Distribuera ZIP-filen med REST API: er 

Du kan använda den [deployment service REST API: er](https://github.com/projectkudu/kudu/wiki/REST-API) distribuera .zip-filen till din app i Azure. Distribuera genom att skicka en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/zipdeploy. POST-begäran måste innehålla ZIP-filen i meddelandetexten. Autentiseringsuppgifter för distribution för din app tillhandahålls i begäran med hjälp av grundläggande autentisering. Mer information finns i [.zip push distribution referens](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Du måste dina autentiseringsuppgifter för distribution av App Service för grundläggande HTTP-autentisering. Information om hur du anger dina autentiseringsuppgifter för distribution finns [ange och återställa användarnivå autentiseringsuppgifter](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel använder cURL-verktyget för att distribuera en .zip-fil. Ersätt platshållarna `<username>`, `<password>`, `<zip_file_path>`, och `<app_name>`. När du uppmanas av cURL, Skriv in lösenordet.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Denna begäran utlöser push-distribution från den överförda ZIP-filen. Du kan granska aktuella och tidigare distributioner med hjälp av https://<app_name>.scm.azurewebsites.net/api/deployments-slutpunkt som visas i exemplet nedan cURL. Igen och Ersätt `<app_name>` med namnet på din app och `<deployment_user>` med användarnamnet för dina autentiseringsuppgifter för distribution.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) att skicka en begäran som innehåller ZIP-filen. Ersätt platshållarna `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, och `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Denna begäran utlöser push-distribution från den överförda ZIP-filen. Kör följande kommandon om du vill granska aktuella och tidigare distributioner. Igen och Ersätt den `<app_name>` platshållare.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
