---
title: "Lokal Git-distribution till Azure Apptjänst"
description: "Lär dig hur du aktiverar lokal Git-distribution till Azure App Service."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
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

## <a name="prepare-your-repository"></a>Förbered din databas

Se till att Lagringsplatsens rot har rätt filer i projektet.

| Körning | Rot-directory-filer |
|-|-|
| ASP.NET (endast Windows) | _*.SLN_, _*.csproj_, eller _default.aspx_ |
| ASP.NET Core | _*.SLN_ eller _*.csproj_ |
| PHP | _index.php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, eller _package.json_ med ett start-skript |
| Python (endast Windows) | _\*.PY_, _requirements.txt_, eller _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, eller  _iisstart.htm_ |
| Webbjobb | _\<job_name > / Kör. \<tillägg >_ under _App\_Data/jobb/kontinuerlig_ (för kontinuerliga Webbjobb) eller _App\_Data/jobb/utlöst_ (för utlöses WebJobs). Mer information finns i [Kudu WebJobs-dokumentation](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funktioner | Se [kontinuerlig distribution för Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

För att anpassa din distribution, kan du inkludera en _.deployment_ filen i Lagringsplatsens rot. Mer information finns i [anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributionsskriptet](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se till att `git commit` alla ändringar som du vill distribuera.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Aktivera Git för din app

För att aktivera Git-distribution för en befintlig app i App Service kör [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) i molnet-gränssnittet.

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

## <a name="deploy-your-project"></a>Distribuera projektet

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt  _\<url >_ med URL: en för Git-fjärråtkomstprincipen som du har fått från [aktivera Git för din app](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Du kan se runtime-specifika automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js, och `pip install` för Python. 

När distributionen är klar, din app i Azure portal bör nu ha en post på din `git push` i den **distributionsalternativ** sidan.

![](./media/app-service-deploy-local-git/deployment_history.png)

Bläddra till appen för att kontrollera att innehållet distribueras.

## <a name="troubleshooting"></a>Felsökning

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
