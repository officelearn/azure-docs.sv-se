---
title: Distribuera kod med en ZIP-eller WAR-fil
description: Lär dig hur du distribuerar din app för att Azure App Service med en ZIP-fil (eller en WAR-fil för Java-utvecklare).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 3569c6a066b09daa0c24975b9de840a844b6ba2c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670231"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service med en ZIP-eller WAR-fil

Den här artikeln visar hur du använder en ZIP-fil eller WAR-fil för att distribuera din webbapp till [Azure App Service](overview.md). 

Den här ZIP-fildistributionen använder samma kudu-tjänst som driver kontinuerlig integrerings-baserade distributioner. Kudu stöder följande funktioner för ZIP-fil distribution: 

- Borttagning av filer som är kvar från en tidigare distribution.
- Alternativ för att aktivera standard Bygg processen, vilket inkluderar paket återställning.
- [Distributions anpassning](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inklusive körning av distributions skript.  
- Distributions loggar. 
- En fil storleks gräns på 2048 MB.

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR-fildistributionen distribuerar [War](https://wikipedia.org/wiki/WAR_(file_format)) -filen till App Service för att köra Java-webbappen. Se [distribuera War-fil](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.

## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du har laddat ned filerna i en ZIP-fil extraherar du filerna först. Om du till exempel har hämtat en ZIP-fil från GitHub kan du inte distribuera filen i befintligt skick. GitHub lägger till ytterligare kapslade kataloger som inte fungerar med App Service. 
>

I ett lokalt terminalfönster navigerar du till rot katalogen för ditt Apps-projekt. 

Den här katalogen ska innehålla post filen till din webbapp, till exempel _index. html_, _index. php_och _app. js_. Det kan också innehålla paket hanterings filer som _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_och _Requirements. txt_.

Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Ovanstående slut punkt fungerar inte för Linux-App Services för tillfället. Överväg att använda FTP eller [zip Deploy-API](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) i stället.

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-fil med Azure CLI

Se till att din Azure CLI-version är 2.0.21 eller senare. Du kan se vilken version du har genom att köra kommandot `az --version` i terminalfönstret.

Distribuera den uppladdade ZIP-filen till din webbapp med hjälp av [AZ webapp Deployment source config-zip-](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) kommandot.  

I följande exempel distribueras ZIP-filen som du laddade upp. När du använder en lokal installation av Azure CLI anger du sökvägen till den lokala ZIP-filen för `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen.

Som standard förutsätter distributions motorn att en ZIP-fil är klar att köras i befintligt skick och inte kör någon build-automatisering. Om du vill aktivera samma build-automatisering som i en [Git-distribution](deploy-local-git.md)anger du `SCM_DO_BUILD_DURING_DEPLOYMENT` app-inställningen genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-fil

Om du vill distribuera en WAR-fil till App Service skickar du en POST-begäran till `https://<app_name>.scm.azurewebsites.net/api/wardeploy`. Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering.

För den grundläggande HTTP-autentiseringen behöver du autentiseringsuppgifterna för App Service distribution. Information om hur du anger autentiseringsuppgifter för distributionen finns i [Ange och återställa autentiseringsuppgifter på användar nivå](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med sväng

I följande exempel används verktyget sväng för att distribuera en War-fil. Ersätt plats hållarna `<username>`, `<war-file-path>`och `<app-name>`. Skriv lösen ordet när du uppmanas till en sväng.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) för att ladda upp War-filen. Ersätt plats hållarna `<group-name>`, `<app-name>`och `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Försök [att distribuera till Azure med git](deploy-local-git.md)för mer avancerade distributions scenarier. Git-baserad distribution till Azure möjliggör versions kontroll, paket återställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för Azure App Service distribution](deploy-ftp.md)
