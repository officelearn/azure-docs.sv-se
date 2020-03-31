---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769682"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Distribuera ZIP-fil med REST-API:er 

Du kan använda [REST-API:erna för distributionstjänsten](https://github.com/projectkudu/kudu/wiki/REST-API) för att distribuera ZIP-filen till din app i Azure. Om du vill distribuera skickar du en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/zipdeploy. POST-begäran måste innehålla ZIP-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering. Mer information finns i [.zip push deployment reference](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

För HTTP BASIC-autentiseringen behöver du distributionsuppgifter för App Service. Information om hur du anger distributionsuppgifter finns i [Ange och återställa autentiseringsuppgifter på användarnivå](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel används cURL-verktyget för att distribuera en ZIP-fil. Ersätt platshållarna `<deployment_user>` `<zip_file_path>`, `<app_name>`och . Skriv in lösenordet när du uppmanas att ange lösenordet.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Den här begäran utlöser push-distribution från den uppladdade ZIP-filen. Du kan granska aktuella och tidigare `https://<app_name>.scm.azurewebsites.net/api/deployments` distributioner med hjälp av slutpunkten, som visas i följande cURL-exempel. Ersätt återigen `<app_name>` med namnet på `<deployment_user>` din app och med användarnamnet för dina distributionsuppgifter.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publicera-AzWebapp](/powershell/module/az.websites/publish-azwebapp) ladda upp ZIP-filen. Ersätt platshållarna `<group-name>` `<app-name>`, `<zip-file-path>`och .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Den här begäran utlöser push-distribution från den uppladdade ZIP-filen. 

Om du vill granska aktuella och tidigare distributioner kör du följande kommandon. Återigen ersätter `<deployment-user>` `<deployment-password>`du `<app-name>` platshållarna , och plats.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
