---
title: Lokal Git-distribution till Azure Apptjänst
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
ms.openlocfilehash: a614dadae40fcfc28eba85e5943f60a38653224b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233911"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure Apptjänst

Den här instruktioner visar hur du distribuera din kod till [Azure App Service](app-service-web-overview.md) från en Git-lagringsplats på den lokala datorn.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Att följa stegen i den här instruktioner:

* [Installera Git](http://www.git-scm.com/downloads).
* Underhålla en lokal Git-lagringsplats med kod som du vill distribuera.

Kör följande kommando för att använda en exempel-databas för att följa instruktionerna i din lokala terminalfönster:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Distribuera från lokala Git med Kudu-versioner

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu build-server är att använda molnet Shell.

### <a name="create-a-deployment-user"></a>Skapa en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu

Om du vill aktivera lokal Git-distribution för din app med Kudu build-servern kör [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) i molnet-gränssnittet.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Om du vill skapa en Git-aktiverad app i stället köra [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) i molnet Shell med den `--deployment-local-git` parameter.

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

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt  _\<url >_ med URL: en för Git-fjärråtkomstprincipen som du har fått från [aktivera Git för din app](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Du kan se runtime-specifika automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js, och `pip install` för Python. 

Bläddra till appen för att kontrollera att innehållet distribueras.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Distribuera från lokala Git med VSTS versioner

> [!NOTE]
> För App Service att skapa den nödvändiga versionen och frigöra definitioner i VSTS kontot är ditt Azure-konto måste ha rollen för **ägare** i din Azure-prenumeration.
>

Om du vill aktivera lokal Git-distribution för din app med Kudu build-servern, navigerar du till din app i den [Azure-portalen](https://portal.azure.com).

I det vänstra navigeringsfönstret på appsidan klickar du på **Deployment Center** > **lokala Git** > **Fortsätt**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Klicka på **VSTS kontinuerlig leverans** > **fortsätta**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

I den **konfigurera** sidan, konfigurera ett nytt konto i VSTS eller ange ett befintligt konto. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda ett befintligt VSTS-konto som inte visas måste du [länka VSTS-konto till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](/pricing/details/app-service/plans/) av din programtjänstplan kan du också se en **till mellanlagring** sidan. Välj om du vill aktivera distributionsplatser och klicka sedan på **Fortsätt**.

I den **sammanfattning** , kontrollera alternativen och klickar på **Slutför**.

Det tar några minuter för kontot VSTS ska bli klar. När den är klar, Kopiera URL: en för Git-lagringsplats i deployment center.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt  _\<url >_ med URL-Adressen som du har fått från det sista steget.

```bash
git remote add vsts <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas av Git Autentiseringshanteraren, logga in med din visualstudio.com användare. Ytterligare autentiseringsmetoder finns [VSTS autentisering översikt](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

När distributionen är klar hittar du bygga förloppet på `https://<vsts_account>.visualstudio.com/<project_name>/_build` och distribution förlopp på `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Bläddra till appen för att kontrollera att innehållet distribueras.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Felsökning av Kudu-distribution

Här följer vanliga fel eller problem när du använder Git för att publicera till en Apptjänst-app i Azure:

---
**Symtom**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Orsak**: det här felet kan inträffa om appen inte är igång.

**Lösning**: starta appen i Azure-portalen. Git-distribution är inte tillgänglig när Webbappen har stoppats.

---
**Symtom**: `Couldn't resolve host 'hostname'`

**Orsak**: det här felet kan inträffa om den adressinformation som anges när du skapar i azure remote var felaktig.

**Lösning**: Använd den `git remote -v` kommando för att lista alla fjärrkontroller tillsammans med den associerade URL: en. Kontrollera att URL: en för ”azure” remote är korrekt. Om det behövs, ta bort och återskapa det här remote med rätt URL.

---
**Symtom**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Orsak**: det här felet kan inträffa om du inte anger en gren under `git push`, eller om du inte har angett den `push.default` värde i `.gitconfig`.

**Lösning**: kör `git push` igen och ange mastergrenen. Exempel:

```bash
git push azure master
```

---
**Symtom**: `src refspec [branchname] does not match any.`

**Orsak**: det här felet kan inträffa om du försöker att skicka till en gren Master på azure remote.

**Lösning**: kör `git push` igen och ange mastergrenen. Exempel:

```bash
git push azure master
```

---
**Symtom**: `RPC failed; result=22, HTTP code = 5xx.`

**Orsak**: det här felet kan inträffa om du försöker att skicka en stor git-lagringsplats via HTTPS.

**Lösning**: ändra git-konfigurationen på den lokala datorn för att göra postBuffer större

```bash
git config --global http.postBuffer 524288000
```

---
**Symtom**: `Error - Changes committed to remote repository but your web app not updated.`

**Orsak**: det här felet kan inträffa om du distribuerar ett Node.js-app med en _package.json_ -fil som anger ytterligare moduler som krävs.

**Lösning**: ytterligare meddelanden med 'npm fel ”! ska loggas innan det här felet och kan ge ytterligare sammanhang om felet. Följande är kända orsaker till felet och den motsvarande 'npm fel ”! meddelande:

* **Felaktig package.json filen**: npm fel! Det gick inte att läsa beroenden.
* **Ursprunglig modul som inte har en binär distribution för Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      ELLER
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Ytterligare resurser

* [Projektet Kudu-dokumentation](https://github.com/projectkudu/kudu/wiki)
* [Kontinuerlig distribution till Azure App Service](app-service-continuous-deployment.md)
* [Exempel: Skapa Webbapp och distribuera kod från en lokal Git-lagringsplats (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Exempel: Skapa Webbapp och distribuera kod från en lokal Git-lagringsplats (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
