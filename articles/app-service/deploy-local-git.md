---
title: Distribuera från lokal Git-lagringsplats – Azure App Service
description: Lär dig hur du aktiverar lokal Git-distribution till Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836922"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure App Service

Den här guiden visar hur du distribuerar din app till [Azure App Service](overview.md) från en Git-lagringsplats på din lokala dator.

## <a name="prerequisites"></a>Förutsättningar

Att följa stegen i den här guiden:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installera Git](https://www.git-scm.com/downloads).
  
- Har en lokal Git-lagringsplats med kod som du vill distribuera. Kör följande kommando för att hämta en exempellagringsplats i ditt lokala terminalfönster:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Distribuera med Kudu build-servern

Det enklaste sättet att aktivera lokal Git-distribution för din app med App Service Kudu build-servern är att använda Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Hämta URL: en för distribution

För att få URL: en för att aktivera lokal Git-distribution för en befintlig app, köra [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) i Cloud Shell. Ersätt \<appens namn-> och \<-namn > med namnen på din app och dess Azure-resursgrupp.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Eller, om du vill skapa en ny Git-aktiverad app kör [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med den `--deployment-local-git` parametern. Ersätt \<appens namn->, \<-namn >, och \<planens namn > med namn för din nya Git-app, dess Azure-resursgrupp och dess Azure App Service-plan.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Kommandot returnerar en URL liknande: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Använd den här URL: en för att distribuera din app i nästa steg.

Du kan också aktivera lokal Git med hjälp av appnivå autentiseringsuppgifter istället för att använda denna kontonivå-URL. Azure App Service genererar automatiskt dessa autentiseringsuppgifter för varje app. 

Hämta autentiseringsuppgifter för appen genom att köra följande kommando i Cloud Shell. Ersätt \<appens namn-> och \<-namn > med appens namn och Azure resursgruppens namn.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Använd den URL som returnerar för att distribuera din app i nästa steg.

### <a name="deploy-the-web-app"></a>Distribuera webbappen

1. Öppna ett lokalt terminalfönster till din lokala Git-lagringsplats och Lägg till en Azure-fjärrdatabas. I följande kommando, ersätter \<url > med distributionens användarspecifika URL eller appspecifika URL som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Skicka till Azure-fjärrdatabasen med `git push azure master`. 
   
1. I den **Git Credential Manager** fönstret, ange din [distribution användarlösenord](#configure-a-deployment-user), istället för ditt Azure-inloggning lösenord.
   
1. Granska utdata. Du kan se runtime-specifika automation, till exempel MSBuild för ASP.NET, `npm install` för Node.js, och `pip install` för Python. 
   
1. Bläddra till din app i Azure portal för att kontrollera att innehållet distribueras.

## <a name="deploy-with-azure-pipelines-builds"></a>Distribuera med Azure Pipelines versioner

Om ditt konto har behörigheterna som krävs, kan du ställa in Azure Pipelines (förhandsversion) att aktivera lokal Git-distribution för din app. 

- Azure-kontot måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Azure-kontot måste ha den **ägare** roll i din Azure-prenumeration.

- Du måste vara en administratör i Azure DevOps-projektet som du vill använda.

Aktivera lokal Git-distribution för din app med Azure Pipelines (förhandsversion):

1. Gå till din app-sida för Azure App Service i den [Azure-portalen](https://portal.azure.com), och välj **Deployment Center** på den vänstra menyn.
   
1. På den **Deployment Center** väljer **lokal Git**, och välj sedan **Fortsätt**. 
   
   ![Välj lokal Git och välj sedan Fortsätt](media/app-service-deploy-local-git/portal-enable.png)
   
1. På den **Build-provider** väljer **Azure Pipelines (förhandsversion)** , och välj sedan **Fortsätt**. 
   
   ![Välj Azure Pipelines (förhandsversion) och därefter på Fortsätt.](media/app-service-deploy-local-git/pipeline-builds.png)

1. På den **konfigurera** konfigurera en ny Azure DevOps-organisation eller anger en befintlig organisation, och välj sedan **Fortsätt**.
   
   > [!NOTE]
   > Om din befintliga Azure DevOps-organisation inte visas kan du behöva länka den till din Azure-prenumeration. Mer information finns i [definiera din version CD-pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Beroende på App Service-planen [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/), du kan se en **distribuera till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](deploy-staging-slots.md), och välj sedan **Fortsätt**.
   
1. På den **sammanfattning** , granskar du inställningarna och välj sedan **Slutför**.
   
1. När Azure pipelinen är klart, Kopiera URL: en för Git-lagringsplats från den **Deployment Center** sida som ska användas i nästa steg. 
   
   ![Kopiera URL: en för Git-lagringsplats](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Lägg till en Azure-fjärrdatabas till din lokala Git-lagringsplats i ditt lokala terminalfönster. I det här kommandot ersätter \<url > med URL: en för Git-lagringsplats som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Skicka till Azure-fjärrdatabasen med `git push azure master`. 
   
1. På den **Git Credential Manager** sidan, logga in med ditt användarnamn på visualstudio.com. Andra autentiseringsmetoder, se [översikt över Azure DevOps-tjänsterna autentisering](/vsts/git/auth-overview?view=vsts).
   
1. När distributionen är klar kan du visa förloppet för bygget på `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`, och förloppet för distributionen på `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Bläddra till din app i Azure portal för att kontrollera att innehållet distribueras.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Felsöka distribution

Du kan se följande vanliga felmeddelanden när du använder Git för att publicera till en App Service-app i Azure:

|Message|Orsak|Lösning
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Appen är inte igång.|Starta appen i Azure-portalen. Git-distribution är inte tillgängligt när webbappen har stoppats.|
|`Couldn't resolve host 'hostname'`|Adressinformationen för ”azure” remote är felaktig.|Använd den `git remote -v` kommando för att lista alla fjärrkontroller, tillsammans med tillhörande URL: en. Kontrollera att URL: en för ”azure” remote är korrekt. Om det behövs kan du ta bort och återskapa den här remote använder rätt Webbadress.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Du inte angav en gren under `git push`, eller om du inte har angett den `push.default` värde i `.gitconfig`.|Kör `git push` igen och ange huvudgrenen: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Du försökte skicka till en gren Master på azure fjärrdatabasen.|Kör `git push` igen och ange huvudgrenen: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Det här felet kan inträffa om du försöker skicka en stor git-lagringsplats via HTTPS.|Ändra git-konfigurationen på den lokala datorn för att göra den `postBuffer` större. Till exempel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Du har distribuerat en Node.js-app med en _package.json_ -fil som anger ytterligare moduler som krävs.|Granska den `npm ERR!` felmeddelanden innan det här felet för mer kontext om felet. Följande är kända orsaker till felet och motsvarande `npm ERR!` meddelanden:<br /><br />**Felaktig package.json-fil**: `npm ERR! Couldn't read dependencies.`<br /><br />**Ursprunglig modul inte har en binär distribution för Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />eller <br />' npm fel! [modulename@version] förinstallera: \make || gmake\`|

## <a name="additional-resources"></a>Ytterligare resurser

- [Projektet Kudu-dokumentation](https://github.com/projectkudu/kudu/wiki)
- [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
