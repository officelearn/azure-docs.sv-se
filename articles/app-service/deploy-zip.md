---
title: Distribuera kod med en ZIP- eller WAR-fil – Azure App Service | Microsoft Docs
description: Lär dig hur du distribuerar din app till Azure App Service med en ZIP-fil (eller en WAR-filen för Java-utvecklare).
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
ms.custom: seodec18
ms.openlocfilehash: 1bc8dc822622ee7b16b3e0a31e7b0b66ed7556e6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488413"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service med en ZIP- eller WAR-fil

Den här artikeln visar hur du använder en ZIP-filen eller WAR-filen för att distribuera din webbapp till [Azure App Service](overview.md). 

Den här ZIP-filen distributionen använder samma Kudu-tjänsten som driver kontinuerlig integration-baserade distributioner. Kudu stöder följande funktioner för distribution av ZIP-fil: 

- Borttagning av filer som kvar från en tidigare distribution.
- Alternativet för att aktivera skapandeprocessen standard, vilket innefattar paketåterställning.
- [Distributionsanpassning](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inklusive distribution skriptkörning.  
- Distributionsloggar. 
- Filen storleksgränsen på 2 048 MB.

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR-filen distributionen distribuerar din [WAR](https://wikipedia.org/wiki/WAR_(file_format)) filen till App Service för att köra Java-webbappen. Se [Distribuera WAR-filen](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här artikeln:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.

## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du har hämtat filer i en ZIP-fil, extrahera filerna först. Till exempel om du har hämtat en ZIP-fil från GitHub, du kan inte distribuera filen som – är. GitHub lägger till ytterligare kapslade kataloger, inte fungerar med App Service. 
>

Navigera till rotkatalogen för din app-projekt i ett lokalt terminalfönster. 

Den här katalogen ska innehålla startfil till din webbapp som _index.html_, _index.php_, och _app.js_. Den kan också innehålla management Paketfiler som _project.json_, _composer.json_, _package.json_, _bower.json_, och _requirements.txt_.

Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-filen med Azure CLI

Kontrollera att din Azure CLI version 2.0.21 eller senare. Att se vilken version du har kan köra `az --version` i terminalfönstret.

Distribuera den uppladdade ZIP-filen till din webbapp med hjälp av den [az webapp config-zip-källa distribution](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) kommando.  

I följande exempel distribueras den uppladdade ZIP-filen. När du använder en lokal installation av Azure CLI, ange sökvägen till din lokala ZIP-fil för `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen. Om någon ytterligare, anpassad versionsprocessen har konfigurerats så körs den också. Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-filen

För att distribuera WAR-filen till App Service kan du skicka en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/wardeploy. Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering. 

För grundläggande HTTP-autentisering måste du dina autentiseringsuppgifter för distribution av App Service. Om du vill se hur du anger dina autentiseringsuppgifter för distribution, se [ange och Återställ användarnivå autentiseringsuppgifter](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel används verktyget cURL för att distribuera WAR-filen. Ersätt platshållarna `<username>`, `<war_file_path>`, och `<app_name>`. När du uppmanas av cURL anger du lösenordet.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) att skicka en begäran som innehåller WAR-filen. Ersätt platshållarna `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, och `<app_name>`.

```powershell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Mer avancerade distributionsscenarier för, försök [distribuera till Azure med Git](deploy-local-git.md). Git-baserade distributionen till Azure gör det möjligt för versionskontroll, paketåterställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för distribution av Azure App Service](deploy-ftp.md)
