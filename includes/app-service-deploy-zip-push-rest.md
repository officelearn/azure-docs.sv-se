---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954064"
---
## <a name="rest"></a>Distribuera ZIP-fil med REST API: er 

Du kan använda [REST-API: er för distributions tjänsten](https://github.com/projectkudu/kudu/wiki/REST-API) för att distribuera. zip-filen till din app i Azure. Om du vill distribuera skickar du en POST-begäran till https://< APP_NAME >. scm. azurewebsites. NET/API/zipdeploy. POST-begäran måste innehålla. zip-filen i meddelande texten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering. Mer information finns i referens för [post-push-distribution](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

För den grundläggande HTTP-autentiseringen behöver du autentiseringsuppgifterna för App Service distribution. Information om hur du anger autentiseringsuppgifter för distributionen finns i [Ange och återställa autentiseringsuppgifter på användar nivå](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med sväng

I följande exempel används verktyget sväng för att distribuera en. zip-fil. Ersätt plats hållarna `<username>` `<password>` `<zip_file_path>`,, och. `<app_name>` Skriv lösen ordet när du uppmanas till en sväng.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Den här begäran utlöser push-distribution från den överförda. zip-filen. Du kan granska aktuella och tidigare distributioner med hjälp `https://<app_name>.scm.azurewebsites.net/api/deployments` av slut punkten, som du ser i följande spiral exempel. Ersätt `<app_name>` igen med namnet på din app och `<deployment_user>` med användar namnet för dina distributions uppgifter.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) för att ladda upp zip-filen. Ersätt plats hållarna `<group-name>`, `<app-name>`och `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Den här begäran utlöser push-distribution från den överförda. zip-filen. 

Om du vill granska aktuella och tidigare distributioner kör du följande kommandon. Ersätt `<deployment-user>` `<app-name>` igen,-och-plats hållarna. `<deployment-password>`

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
