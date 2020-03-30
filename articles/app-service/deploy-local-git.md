---
title: Distribuera från lokala Git-repo
description: Lär dig hur du aktiverar lokal Git-distribution till Azure App Service. Ett av de enklaste sätten att distribuera kod från din lokala dator.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153000"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure App Service

Den här programguiden visar hur du distribuerar din app till [Azure App Service](overview.md) från en Git-databas på din lokala dator.

## <a name="prerequisites"></a>Krav

Så här följer du stegen i den här guiden:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installera Git](https://www.git-scm.com/downloads).
  
- Har en lokal Git-databas med kod som du vill distribuera. Om du vill hämta en exempeldatabas kör du följande kommando i det lokala terminalfönstret:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Distribuera med Kudu byggserver

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu App Service-byggservern är att använda Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Hämta distributions-URL:en

Om du vill hämta URL:en för att [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) aktivera lokal Git-distribution för en befintlig app körs du i Cloud Shell. Ersätt \<appnamn> och \<gruppnamn> med namnen på din app och dess Azure-resursgrupp.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Om du använder en linux-app-service-plan måste du lägga till den här parametern: --runtime python|3.7


Om du vill skapa en ny Git-aktiverad app körs du [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med parametern. `--deployment-local-git` Ersätt \<appnamn>, \<gruppnamn> och \<plannamn> med namnen på din nya Git-app, dess Azure-resursgrupp och dess Azure App Service-plan.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Båda kommandona returnerar `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`en URL som: . Använd den här webbadressen för att distribuera appen i nästa steg.

I stället för att använda den här webbadressen på kontonivå kan du också aktivera lokal Git med hjälp av autentiseringsuppgifter på appnivå. Azure App Service genererar automatiskt dessa autentiseringsuppgifter för varje app. 

Hämta appautentiseringsuppgifterna genom att köra följande kommando i Cloud Shell. Ersätt \<appnamn> och \<gruppnamn> med appens namn och Azure-resursgruppnamn.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Använd webbadressen som returneras för att distribuera appen i nästa steg.

### <a name="deploy-the-web-app"></a>Distribuera webbappen

1. Öppna ett lokalt terminalfönster i din lokala Git-databas och lägg till en Azure-fjärrkontroll. I följande kommando \<ersätter du url-> med den användarspecifik URL för distributionen eller den appspecifika URL:en som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Tryck till Azure-fjärrkontrollen med `git push azure master`. 
   
1. I fönstret **Git-autentiseringshanteraren** anger du [lösenordet för distributionsanvändare](#configure-a-deployment-user), inte ditt Azure-inloggningslösenord.
   
1. Granska utdata. Du kan se körningsspecifik automatisering, till exempel MSBuild `npm install` för ASP.NET, för `pip install` Node.js och Python. 
   
1. Bläddra till din app i Azure-portalen för att verifiera att innehållet distribueras.

## <a name="deploy-with-azure-pipelines-builds"></a>Distribuera med Azure Pipelines-versioner

Om ditt konto har de behörigheter som krävs kan du konfigurera Azure Pipelines (Preview) för att aktivera lokal Git-distribution för din app. 

- Ditt Azure-konto måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Ditt Azure-konto måste ha **ägarrollen** i din Azure-prenumeration.

- Du måste vara administratör i Azure DevOps-projektet som du vill använda.

Så här aktiverar du lokal Git-distribution för din app med Azure Pipelines (förhandsversion):

1. Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com). 

1. Välj din Azure App Service-app och välj **Distributionscenter** på den vänstra menyn.
   
1. På sidan **Distributionscenter** väljer du **Lokal Git**och väljer sedan **Fortsätt**. 
   
   ![Välj Lokal Git och välj sedan Fortsätt](media/app-service-deploy-local-git/portal-enable.png)
   
1. På sidan **Byggprovider** väljer du **Azure Pipelines (förhandsversion)** och väljer sedan **Fortsätt**. 
   
   ![Välj Azure Pipelines (förhandsversion) och välj sedan Fortsätt.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Konfigurera en ny Azure DevOps-organisation på sidan **Konfigurera** eller ange en befintlig organisation och välj sedan **Fortsätt**.
   
   > [!NOTE]
   > Om din befintliga Azure DevOps-organisation inte finns med i listan kan du behöva länka den till din Azure-prenumeration. Mer information finns i [Definiera cd-versionspipelinen](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Beroende på [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/)för appserviceplanen kan du se sidan **Distribuera till mellanlagring.** Välj om [distributionsplatser](deploy-staging-slots.md)ska aktiveras och välj sedan **Fortsätt**.
   
1. Granska inställningarna på sidan **Sammanfattning** och välj sedan **Slutför**.
   
1. När Azure Pipeline är klar kopierar du Git-databas-URL:en från sidan **Distributionscenter** som ska användas i nästa steg. 
   
   ![Kopiera Git-databasens URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Lägg till en Azure-fjärrkontroll i din lokala Git-databas i det lokala terminalfönstret. I kommandot ersätter du \<url-> med url:en för Git-databasen som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Tryck till Azure-fjärrkontrollen med `git push azure master`. 
   
1. Logga in med ditt visualstudio.com användarnamn på **sidan Git-autentiseringshanteraren.** Mer information om andra autentiseringsmetoder finns i [autentiseringsöversikten för Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. När distributionen är klar visar `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`du byggstatusen `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`vid och distributionsstatusen vid .
   
1. Bläddra till din app i Azure-portalen för att verifiera att innehållet distribueras.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Felsöka distribution

Följande vanliga felmeddelanden visas när du använder Git för att publicera till en App Service-app i Azure:

|Meddelande|Orsak|Lösning
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Appen är inte igång.|Starta appen i Azure-portalen. Git-distributionen är inte tillgänglig när webbappen stoppas.|
|`Couldn't resolve host 'hostname'`|Adressinformationen för den "azurblå" fjärrkontrollen är felaktig.|Använd `git remote -v` kommandot för att lista alla fjärrkontroller, tillsammans med den associerade URL:en. Kontrollera att URL:en för den "azurblå" fjärrkontrollen är korrekt. Ta bort och återskapa fjärrkontrollen med rätt URL om det behövs.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Du har inte angett `git push`någon gren under eller `push.default` så `.gitconfig`har du inte angett värdet i .|Kör `git push` igen och ange huvudgrenen: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Du försökte skicka till en annan gren än huvud på fjärrkontrollen "azure".|Kör `git push` igen och ange huvudgrenen: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Det här felet kan inträffa om du försöker skicka en stor git-databas över HTTPS.|Ändra git-konfigurationen på den `postBuffer` lokala datorn för att göra den större. Till exempel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Du har distribuerat en Node.js-app med en _package.json-fil_ som anger ytterligare obligatoriska moduler.|Granska `npm ERR!` felmeddelandena innan det här felet finns för mer kontext om felet. Följande är de kända orsakerna till det `npm ERR!` här felet och motsvarande meddelanden:<br /><br />**Missbildad package.json fil:**`npm ERR! Couldn't read dependencies.`<br /><br />**Den inbyggda modulen har ingen binär distribution för Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />eller <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ytterligare resurser

- [Projekt Kudu dokumentation](https://github.com/projectkudu/kudu/wiki)
- [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-databas (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-databas (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
