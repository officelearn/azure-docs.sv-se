---
title: Distribuera din app till Azure App Service med en ZIP- eller WAR-fil | Microsoft Docs
description: Lär dig mer om att distribuera din app till Azure App Service med en ZIP-fil (eller en WAR-fil för Java-utvecklare).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: a3178d5cb09087a243a51e20567895d03ce1f7fb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234142"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service med en ZIP- eller WAR-fil

Den här artikeln visar hur du använder en ZIP-fil eller en WAR-filen för att distribuera ditt webbprogram till [Azure App Service](app-service-web-overview.md). 

Den här ZIP-filen distributionen använder samma Kudu-tjänst som stänger kontinuerlig integration-baserade distributioner. Kudu stöder följande funktioner för distribution av ZIP-filen: 

- Borttagning av filer som kvar från en tidigare distribution.
- Alternativet för att aktivera build processen, som innehåller paketet återställning.
- [Distributionsanpassning](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inklusive skriptkörning för distribution.  
- Distributionsloggar. 

Mer information finns i [Kudu dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Distributionen av WAR-filen distribuerar din [WAR](https://wikipedia.org/wiki/WAR_(file_format)) filen till App Service för att köra ditt webbprogram för Java. Se [Distribuera WAR-filen](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Så här slutför stegen i den här artikeln:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.

## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du har hämtat filer i en ZIP-fil, extrahera filerna först. Till exempel om du har hämtat en ZIP-fil från GitHub, du kan inte distribuera filen som-är. GitHub lägger till ytterligare kapslade kataloger, inte fungerar med App Service. 
>

Navigera till rotkatalogen för din app-projekt i en lokal terminalfönster. 

Den här katalogen ska innehålla filen post till ditt webbprogram som _index.html_, _index.php_, och _app.js_. Den kan också innehålla management paketfilerna som _project.json_, _composer.json_, _package.json_, _bower.json_, och _requirements.txt_.

Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-filen med Azure CLI

Kontrollera att Azure CLI-versionen är 2.0.21 eller senare. Att se vilken version som du kan köra `az --version` i din terminalfönster.

Distribuera den överförda ZIP-filen till ditt webbprogram med hjälp av den [az webapp distribution källa config zip-](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) kommando.  

I följande exempel distribuerar du överfört ZIP-filen. När du använder en lokal installation av Azure CLI, ange sökvägen till din lokala ZIP-filen för `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen. Om någon ytterligare, anpassad versionsprocessen har konfigurerats så körs den också. Mer information finns i [Kudu dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-filen

Skicka en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/wardeploy för att distribuera WAR-filen till App Service. Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering. 

Du måste dina autentiseringsuppgifter för distribution av App Service för grundläggande HTTP-autentisering. Information om hur du anger dina autentiseringsuppgifter för distribution finns [ange och återställa användarnivå autentiseringsuppgifter](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel använder cURL-verktyget för att distribuera en .war-fil. Ersätt platshållarna `<username>`, `<war_file_path>`, och `<app_name>`. När du uppmanas av cURL, Skriv in lösenordet.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) att skicka en begäran som innehåller filen .war. Ersätt platshållarna `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, och `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Mer avancerade scenarier för distribution, försök [distribution till Azure med Git](app-service-deploy-local-git.md). Git-baserad distribution till Azure kan versionskontroll, paketet återställning, MSBuild och mycket mer.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för distribution av Azure App Service](app-service-deploy-ftp.md)
