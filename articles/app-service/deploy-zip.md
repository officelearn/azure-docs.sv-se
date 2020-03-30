---
title: Distribuera kod med en ZIP- eller WAR-fil
description: Lär dig hur du distribuerar din app till Azure App Service med en ZIP-fil (eller en WAR-fil för Java-utvecklare).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945147"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service med en ZIP- eller WAR-fil

Den här artikeln visar hur du använder en ZIP-fil eller WAR-fil för att distribuera din webbapp till [Azure App Service](overview.md). 

Den här ZIP-fildistributionen använder samma Kudu-tjänst som driver kontinuerliga integrationsbaserade distributioner. Kudu stöder följande funktioner för ZIP-fildistribution: 

- Radering av filer som blivit över från en tidigare distribution.
- Alternativ för att aktivera standardbyggprocessen, som inkluderar paketåterställning.
- Anpassning av distribution, inklusive att köra distributionsskript.  
- Distributionsloggar. 
- En filstorleksgräns på 2048 MB.

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

War-fildistributionen distribuerar din [WAR-fil](https://wikipedia.org/wiki/WAR_(file_format)) till App Service för att köra din Java-webbapp. Se [Distribuera WAR-fil](#deploy-war-file).

## <a name="prerequisites"></a>Krav

Om du vill slutföra stegen i den här artikeln [skapar du en App Service-app](/azure/app-service/)eller använder en app som du har skapat för en annan självstudiekurs.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Slutpunkten ovan fungerar inte för Linux App Services just nu. Överväg att använda FTP eller [ZIP-distributions-API:et](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) i stället.

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-fil med Azure CLI

Distribuera den uppladdade ZIP-filen till webbappen med kommandot [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

I följande exempel distribueras ZIP-filen som du laddade upp. När du använder en lokal installation av Azure CLI `--src`anger du sökvägen till den lokala ZIP-filen för .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen.

Som standard förutsätter distributionsmotorn att en ZIP-fil är redo att köras som den är och inte kör någon build-automatisering. Om du vill aktivera samma build automation som `SCM_DO_BUILD_DURING_DEPLOYMENT` i en [Git-distribution](deploy-local-git.md)anger du appinställningen genom att köra följande kommando i Cloud [Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-fil

Om du vill distribuera en WAR-fil `https://<app-name>.scm.azurewebsites.net/api/wardeploy`till App Service skickar du en POST-begäran till . Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering.

Använd `/api/wardeploy` alltid när du distribuerar WAR-filer. Det här API:et utökar filen WAR och placerar den på den delade filenheten. om du använder andra api:er för distribution kan det leda till inkonsekvent beteende. 

För HTTP BASIC-autentiseringen behöver du distributionsuppgifter för App Service. Information om hur du anger distributionsuppgifter finns i [Ange och återställa autentiseringsuppgifter på användarnivå](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel används cURL-verktyget för att distribuera en .war-fil. Ersätt platshållarna `<username>` `<war-file-path>`, `<app-name>`och . Skriv in lösenordet när du uppmanas att ange lösenordet.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publicera-AzWebapp](/powershell/module/az.websites/publish-azwebapp) ladda upp .war-filen. Ersätt platshållarna `<group-name>` `<app-name>`, `<war-file-path>`och .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Mer avancerade distributionsscenarier kan du prova [att distribuera till Azure med Git](deploy-local-git.md). Git-baserad distribution till Azure möjliggör versionskontroll, paketåterställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för Distribution av Azure App-tjänst](deploy-ftp.md)
