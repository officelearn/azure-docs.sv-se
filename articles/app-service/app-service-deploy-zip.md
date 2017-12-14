---
title: Distribuera din app till Azure App Service med en ZIP-fil | Microsoft Docs
description: "Lär dig mer om att distribuera din app till Azure App Service med en ZIP-fil."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: 9838f0810f4827df3eb4f9407d4d4fbc1ad0ff4d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Distribuera din app till Azure App Service med en ZIP-fil

Den här artikeln visar hur du använder en ZIP-fil för att distribuera ditt webbprogram till [Azure App Service](app-service-web-overview.md). 

Den här ZIP-filen distributionen använder samma Kudu-tjänst som stänger kontinuerlig integration-baserade distributioner. Kudu stöder följande funktioner för distribution av ZIP-filen: 

- Borttagning av filer som kan komma från en tidigare distribution.
- Alternativet för att aktivera build processen, som innehåller paketet återställning.
- [Distributionsanpassning](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inklusive skriptkörning för distribution.  
- Distributionsloggar. 

Mer information finns i [Kudu dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>Krav

Så här slutför stegen i den här artikeln:

* [Skapa en Apptjänst-app](/azure/app-service/), eller använda en app som du skapade för en annan kursen.

## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil med projekt

>[!NOTE]
> Om du har hämtat filer i en ZIP-fil, extrahera filerna först. Till exempel om du har hämtat en ZIP-fil från GitHub, du kan inte distribuera filen som-är. GitHub lägger till ytterligare kapslade kataloger, inte fungerar med App Service. 
>

Navigera till rotkatalogen för din app-projekt i en lokal terminalfönster. 

Den här katalogen ska innehålla filen post till ditt webbprogram som _index.html_, _index.php_, och _app.js_. Den kan också innehålla management paketfilerna som _project.json_, _composer.json_, _package.json_, _bower.json_, och _requirements.txt_.

Skapa ett ZIP-arkiv av allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Ladda upp ZIP-filen till molnet Shell

Hoppa över det här steget om du väljer att köra Azure CLI från din lokala terminalen i stället.

Följ stegen här för att ladda upp ZIP-filen till molnet Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Mer information finns i [spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Distribuera en ZIP-fil

Distribuera den överförda ZIP-filen till ditt webbprogram med hjälp av den [az webapp distribution källa config zip-](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) kommando. Om du inte väljer att använda molnet Shell, kontrollera din Azure CLI-versionen är 2.0.21 eller senare. Att se vilken version som du kan köra `az --version` i fönstret för lokala terminal. 

I följande exempel distribuerar du överfört ZIP-filen. När du använder en lokal installation av Azure CLI, ange sökvägen till din lokala ZIP-filen för `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till programmappen standard Apptjänst (`\home\site\wwwroot`) och startar om appen. Om någon ytterligare anpassade build-processen är konfigurerad, körs det också. Mer information finns i [Kudu dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Om du vill visa listan över distributioner för den här appen måste du använda REST API: er (se nästa avsnitt). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Nästa steg

Mer avancerade scenarier för distribution, försök [distribution till Azure med Git](app-service-deploy-local-git.md). Git-baserad distribution till Azure kan versionskontroll, paketet återställning, MSBuild och mycket mer.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för distribution av Azure App Service](app-service-deploy-ftp.md)
