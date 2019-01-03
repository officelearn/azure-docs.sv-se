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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: 2c547eba931e23b6ffc7cae176e19959d43bcf5e
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602502"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure Apptjänst

Den här guiden visar hur du distribuerar din kod till [Azure App Service](overview.md) från en Git-lagringsplats på din lokala dator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Att följa stegen i den här guiden:

* [Installera Git](https://www.git-scm.com/downloads).
* Underhålla en lokal Git-lagringsplats med kod som du vill distribuera.

Om du vill använda en exempellagringsplats om du vill följa med, kör du följande kommando i ditt lokala terminalfönster:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Distribuera från lokal Git med Kudu-versioner

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu build-servern är att använda Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu

Om du vill aktivera lokal Git-distribution för din app med Kudu build-servern kör [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Om du vill skapa en Git-aktiverad app i stället kör [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med den `--deployment-local-git` parametern.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Den `az webapp create` kommandot bör ge dig något som liknar följande utdata:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Distribuera projektet

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt  _\<url >_ med URL-Adressen för den fjärranslutna Git som du fick från [aktivera Git för din app](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Du kan se runtime-specifika automation i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js, och `pip install` för Python. 

Bläddra till din app för att kontrollera att innehållet distribueras.

## <a name="deploy-from-local-git-with-azure-devops-services-builds"></a>Distribuera från lokal Git med Azure DevOps-tjänsterna versioner

> [!NOTE]
> För App Service för att skapa nödvändiga Azure Pipelines i din organisation med Azure DevOps-tjänsterna, Azure-kontot måste ha rollen **ägare** i Azure-prenumerationen.
>

Om du vill aktivera lokal Git-distribution för din app med Kudu build-servern, navigerar du till din app i den [Azure-portalen](https://portal.azure.com).

I den vänstra navigeringen i din appsida klickar du på **Deployment Center** > **lokal Git** > **Fortsätt**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Klicka på **Azure DevOps Services kontinuerlig leverans** > **fortsätta**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

I den **konfigurera** sidan, konfigurera en ny Azure DevOps-tjänsterna organisation eller ange en befintlig organisation. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda en befintlig Azure DevOps-tjänsterna organisation som inte visas kan du behöva [länka Azure DevOps-tjänsterna organisationen till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/) för din App Service-plan kan du också se en **distribuera till mellanlagring** sidan. Välj om du vill aktivera distributionsplatser och klicka sedan på **Fortsätt**.

I den **sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

Det tar några minuter för Azure DevOps-tjänsterna organisationen ska bli klar. När det är klart, Kopiera URL: en för Git-lagringsplats i deployment center.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt  _\<url >_ med URL-Adressen som du fick från det sista steget.

```bash
git remote add vsts <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas av Git Credential Manager, logga in med din visualstudio.com-användare. Ytterligare autentiseringsmetoder finns [översikt över Azure DevOps-tjänsterna autentisering](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

När distributionen är klar, hittar du förloppet för bygget på `https://<vsts_account>.visualstudio.com/<project_name>/_build` och förloppet för distributionen på `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Bläddra till din app för att kontrollera att innehållet distribueras.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Felsökning av Kudu-distribution

Följande är vanliga fel eller problem när du använder Git för att publicera till en App Service-app i Azure:

---
**Symtom**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Orsak**: Det här felet kan inträffa om appen inte är igång.

**Lösning**: Starta appen i Azure-portalen. Git-distribution är inte tillgänglig när Webbappen har stoppats.

---
**Symtom**: `Couldn't resolve host 'hostname'`

**Orsak**: Det här felet kan inträffa om den adressinformation som anges när du skapar i azure remote var felaktig.

**Lösning**: Använd den `git remote -v` kommando för att lista alla fjärrkontroller, tillsammans med tillhörande URL: en. Kontrollera att URL: en för ”azure” remote är korrekt. Om det behövs kan du ta bort och återskapa den här remote använder rätt Webbadress.

---
**Symtom**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Orsak**: Det här felet kan inträffa om du inte anger en gren under `git push`, eller om du inte har angetts i `push.default` värde i `.gitconfig`.

**Lösning**: Kör `git push` igen och ange huvudgrenen. Exempel:

```bash
git push azure master
```

---
**Symtom**: `src refspec [branchname] does not match any.`

**Orsak**: Det här felet kan inträffa om du försöker skicka till en gren Master på azure fjärrdatabasen.

**Lösning**: Kör `git push` igen och ange huvudgrenen. Exempel:

```bash
git push azure master
```

---
**Symtom**: `RPC failed; result=22, HTTP code = 5xx.`

**Orsak**: Det här felet kan inträffa om du försöker skicka en stor git-lagringsplats via HTTPS.

**Lösning**: Ändra git-konfigurationen på den lokala datorn för att göra postBuffer större

```bash
git config --global http.postBuffer 524288000
```

---
**Symtom**: `Error - Changes committed to remote repository but your web app not updated.`

**Orsak**: Det här felet kan inträffa om du distribuerar en Node.js-app med en _package.json_ -fil som anger ytterligare moduler som krävs.

**Lösning**: Ytterligare meddelanden med 'npm fel ”! ska loggas innan det här felet och kan ge ytterligare sammanhang om felet. Följande är kända orsaker till felet och motsvarande 'npm fel ”! meddelande:

* **Felaktig package.json-fil**: npm fel! Det gick inte att läsa beroenden.
* **Ursprunglig modul som inte har en binär distribution för Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      ELLER
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Ytterligare resurser

* [Projektet Kudu-dokumentation](https://github.com/projectkudu/kudu/wiki)
* [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
* [Exempel: Skapa Webbapp och distribuera kod från en lokal Git-lagringsplats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Exempel: Skapa Webbapp och distribuera kod från en lokal Git-lagringsplats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
