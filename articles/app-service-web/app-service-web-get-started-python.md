---
title: "Skapa din första Python-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera din första Hello World-app med Python i App Service-webbappen på bara några minuter."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: f60e1188d1eb8baf8c6d5e77e2ff91a449351e1e
ms.lasthandoff: 04/04/2017


---
# <a name="create-a-python-application-on-web-app"></a>Skapa ett Python-program i en webbapp

I den här kursen visar vi hur du utvecklar en Python-app och distribuerar den till Azure. Vi kommer att köra appen med en Linux-baserad Azure App Service och skapa och konfigurera en ny webbapp i den med hjälp av Azure CLI. Sedan kommer vi att använda git för att distribuera Python-appen till Azure.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. Det tar normalt bara 5 minuter att slutföra alla steg.

## <a name="before-you-begin"></a>Innan du börjar

Innan du kör det här exemplet måste du installera följande på den lokala datorn:

1. [Hämta och installera git](https://git-scm.com/)
1. [Hämta och installera Python](https://Python.net)
1. Hämta och installera [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>Hämta exemplet

Klona databasen för Hello World-exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> Du kan också [hämta exemplet](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip) som en zip-fil och extrahera den.

Ändra till den katalog som innehåller exempelkoden.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt genom att öppna ett terminalfönster och använda `Python`-kommandoraden för exemplet för att starta den inbyggda Python-webbservern.

```bash
Python -S localhost:8080
```

Öppna webbläsaren och navigera till exemplet.

```bash
http://localhost:8080
```

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

## <a name="log-in-to-azure"></a>Logga in på Azure

Nu ska vi använda Azure CLI 2.0 i ett terminalfönster för att skapa de resurser som behövs för att använda Azure som värd för Python-appen. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

För FTP och lokal Git måste du ha en distributionsanvändare konfigurerad på servern för att autentisera din distribution. Du behöver bara skapa distributionsanvändaren en gång. Anteckna användarnamnet och lösenordet eftersom du kommer att använda dem i ett steg nedan.

> [!NOTE]
> En distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp.
> `username` och `password` är på kontonivå, vilket innebär att de skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration. **Dessa autentiseringsuppgifter behöver bara skapas en gång**.
>

Använd kommandot [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) för att skapa dina autentiseringsuppgifter på kontonivå.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Skapa en Azure App Service

Skapa en Linux-baserad App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> En App Service-plan representerar den samling fysiska resurser som dina appar finns i. Alla program som har tilldelats en App Service-plan delar de resurser som definierats av planen. Det innebär att du kan minska kostnaderna när du har flera appar i en plan.
>
> App Service-planer definierar följande:
> * Region (Europa, norra; USA, östra; Asien, sydöstra)
> * Instansstorlek (liten, medel, stor)
> * Skalningsantal (en, två eller tre instanser osv.)
> * SKU (Kostnadsfri, Delad, Basic, Standard, Premium)
>

I följande exempel skapas en App Service-plan på Linux-arbetare med namnet `quickStartPlan` och prisnivån **Standard**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Nu när en App Service-plan har skapats kan du skapa en webbapp i `quickStartPlan` App Service-planen. Med webbappen får vi ett utrymme för att distribuera vår kod och en URL så att vi kan visa den distribuerade appen. Använd kommandot [az appservice web create](/cli/azure/appservice/web#create) för att skapa webbappen.

I kommandot nedan anger du ditt unika appnamn i platshållaren <app_name>. <app_name> används som standard-DNS-webbplats för webbappen. Därför måste namnet vara unikt i förhållande till alla appar på Azure. Du kan senare mappa en anpassad DNS-post till webbappen innan du exponerar den för användarna.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

När webbappen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Gå till webbplatsen för att se webbappen du precis skapade.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

Nu har vi skapat en ny tom webbapp på Azure. Och nu ska vi konfigurera webbappen för användning av Python och distribuera vår app till den.

## <a name="configure-to-use-python"></a>Konfiguration för användning av Python

Använd kommandot [az appservice web config update](/cli/azure/app-service/web/config#update) för att konfigurera webbappen för användning av Python version `7.0.x`.

> [!TIP]
> När Python-versionen konfigureras på det här sättet används en standardbehållare som tillhandahålls av plattformen. Om du vill använda en egen behållare kan du läsa CLI-referensen för kommandot [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --name <app_name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

Du kan distribuera till din webbapp på flera olika sätt, t.ex. FTP, lokal Git samt GitHub, Visual Studio Team Services och Bitbucket.

Använd kommandot [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) för att konfigurera lokal git-åtkomst till webbappen.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Kopiera utdata från terminalen. De kommer att användas i nästa steg.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <paste-previous-command-output-here>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet du angav tidigare. Det behövs för att skapa distributionsanvändaren.

```azurecli
git push azure master
```

Under distributionen meddelar Azure App Service förloppet till Git.

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'main.py'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Nu körs sidan som visar Hello World-meddelandet som en Azure App Service-webbapp med vår Python-kod.



## <a name="updating-and-deploying-the-code"></a>Uppdatera och distribuera koden

Öppna filen `main.py` i Python-appen med ett lokalt textredigeringsprogram och gör små ändringar i texten i strängen bredvid `echo`:

```python
echo "Hello Azure!";
```

Spara ändringarna på git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle gå till appen och klickar på knappen för att uppdatera.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till Azure Portal och titta på webbappen du nyss skapade.

Logga in på [https://portal.azure.com](https://portal.azure.com).

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-python/Python-docs-hello-world-app-service-list.png)

Nu visas webbappens _blad_ (en portalsida som öppnas vågrätt).

Sidan **Översikt** visas som standard på webbappens blad. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på bladet kan du se olika konfigurationssidor som du kan öppna.

![App Service-blad på Azure Portal](media/app-service-web-get-started-python/Python-docs-hello-world-app-service-detail.png)

Flikarna på bladet innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:

* Mappa ett anpassat DNS-namn
* Bind ett anpassat SSL-certifikat
* Konfigurera kontinuerlig distribution
* Skala upp
* Lägg till användarautentisering

**Grattis!** Du har distribuerat din första Python-app till App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [CLI-skript för Web Apps](app-service-cli-samples.md).
