---
title: "Distribuera din första Node.js -webbapp via Azure på fem minuter (CLI 2.0 Preview) | Microsoft Docs"
description: "Distribuera en Node.js-exempelapp och se hur enkelt det är att köra webbappar i App Service. Börja snabbt utveckla på riktigt och se resultatet direkt."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 06a8dfbac31024cb44fd38bcf9c4a4ea79dbc968


---
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Distribuera din första Node.js-webbapp via Azure på fem minuter (CLI 2.0 Preview)

> [!div class="op_single_selector"]
> * [Första HTML-webbplats](app-service-web-get-started-html.md)
> * [Första .NET-app](app-service-web-get-started-dotnet.md)
> * [Första PHP-app](app-service-web-get-started-php.md)
> * [Första Node.js-app](app-service-web-get-started-nodejs.md)
> * [Första Python-app](app-service-web-get-started-python.md)
> * [Första Java-app](app-service-web-get-started-java.md)
> 
> 

I de här självstudierna lär du dig hur du distribuerar din första webbapp med Node.js via [Azure App Service](../app-service/app-service-value-prop-what-is.md).
Du kan använda App Service för att skapa webbappar, [serverdelar för mobilappar](/documentation/learning-paths/appservice-mobileapps/) och [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du kommer att: 

* Skapa en webbapp i Azure App Service.
* Distribuera Node.js-exempelkod.
* Se hur din kod körs live i produktionen.
* Uppdatera webbappen på samma sätt som du [skickar Git-skrivningar](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](app-service-web-get-started-nodejs-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering
- [Azure CLI 2.0 (förhandsversion)](app-service-web-get-started-nodejs.md) –vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="prerequisites"></a>Krav
* [Git](http://www.git-scm.com/downloads).
* [Förhandsversion av Azure CLI 2.0](/cli/azure/install-az-cli2).
* Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="deploy-a-nodejs-web-app"></a>Distribuera en Node.js-webbapp
1. Öppna en ny Windows-kommandotolk, ett PowerShell-fönster, Linux-gränssnittet eller en OS X-terminal. Kör `git --version` och `azure --version` för att kontrollera att Git och Azure CLI är installerade på datorn.
   
    ![Testa installationen av CLI-verktygen för din första webbapp i Azure](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    Om du inte har installerat verktygen använder du nedladdningslänkarna under [Krav](#Prerequisites).
2. Logga in i Azure så här:
   
        az login
   
    Fortsätt inloggningen genom att följa anvisningarna i hjälpmeddelandet.
   
    ![Logga in i Azure för att skapa din första webbapp](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. Ange distributionsanvändare för App Service. Du distribuerar kod med dessa autentiseringsuppgifterna senare.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Skapa en ny [resursgrupp](../azure-resource-manager/resource-group-overview.md). För denna första App Service-självstudie behöver du egentligen inte veta vad det är.

        az group create --location "<location>" --name my-first-app-group

    För att se vilka möjliga värden du kan använda för `<location>`, använd CLI-kommandot `az appservice list-locations`.

3. Skapa en ny, ”kostnadsfri” [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). För denna första App Service-självstudie är det bra att veta att du inte kommer att debiteras för webbappar i den här planen.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Skapa en ny webbapp med ett unikt namn i `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Därefter får du Node.js-exempelkoden som du vill distribuera. Ändra till en arbetskatalog (`CD`) och klona exempelappen så här:
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

5. Ändra till lagringsplatsen för din exempelapp.
   
        cd app-service-web-nodejs-get-started
5. Konfigurera lokal Git-distribution för din App Service-webbapp med följande kommando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Du får JSON-utdata som den här, vilket innebär att Git-lagringsplatsen är konfigurerad:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Lägg till URL:en i JSON som en fjärransluten Git för din lokala lagringsplats (kallas `azure` för enkelhets skull).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Distribuera din exempelkod till Azure-appen på samma sätt som du skickar annan kod med Git. Ange lösenordet som du konfigurerade tidigare när du blir uppmanad att göra det.
   
        git push azure master
   
    ![Skicka koden till din första webbapp i Azure](./media/app-service-web-get-started-languages/node-git-push.png)
   
    `git push` placerar inte bara kod i Azure, utan utlöser även distributionsuppgifter i distributionsmotorn. 
    Om du har ett package.json i projektroten (databasen) återställer distributionsskriptet det nödvändiga paketet åt dig. 

Grattis, du har distribuerat din app till Azure App Service.

## <a name="see-your-app-running-live"></a>Köra appen live
Du kan köra appen live i Azure genom att köra det här kommandot från valfri katalog i databasen:

    az appservice web browse -n <app-name> --resource-group my-first-app-group

## <a name="make-updates-to-your-app"></a>Göra appuppdateringar
Nu kan du använda Git och skicka skrivningar från projektroten (databasen) när som helst för att uppdatera liveplatsen. Du gör på samma sätt som när du distribuerade din kod första gången. Exempelvis kör du bara följande kommandon från projektroten (databasen) varje gång du vill skicka in en ny ändring som du har testat lokalt:

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Nästa steg
[Skapa, konfigurera och distribuera en Node.js Express-webbapp via Azure](app-service-web-nodejs-get-started.md). I de här självstudierna får du grundläggande kunskaper i hur du kör Node.js-webbappar i Azure, till exempel:

* Skapa och konfigurera appar i Azure från PowerShell/Bash.
* Ange Node.js-version.
* Använda en startfil som inte finns i rotkatalogen för programmet.
* Automatisera med NPM.
* Hämta fel- och utdataloggar.

Eller gör mer med din första webbapp. Exempel:

* Prova [andra sätt att distribuera din kod till Azure](web-sites-deploy.md). Markera till exempel **GitHub** istället för **Lokal Git-lagringsplats** i **Distributionsalternativ** för att distribuera från någon av dina GitHub-lagringsplatser.
* Ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar. Mer information finns i [Lägga till funktioner till din första webbapp](app-service-web-get-started-2.md).




<!--HONumber=Jan17_HO3-->


