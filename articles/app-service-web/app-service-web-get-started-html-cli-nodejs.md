---
title: "Distribuera din första webbapp till Azure på fem minuter | Microsoft Docs"
description: "Distribuera en exempelapp och se hur enkelt det är att köra webbappar i App Service. Börja snabbt utveckla på riktigt och se resultatet direkt."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 27c50be7-421a-47c9-8279-506519e404a4
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 05e61d2fc751c4239aef4b10ad897765c59fe928
ms.openlocfilehash: 4e2d846ef3ce6e78d5789c7b8e1b8228617837b6


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>Distribuera din första webbapp till Azure på fem minuter

> [!div class="op_single_selector"]
> * [Första HTML-webbplats](app-service-web-get-started-html-cli-nodejs.md)
> * [Första .NET-app](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Första PHP-app](app-service-web-get-started-php-cli-nodejs.md)
> * [Första Node.js-app](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Första Python-app](app-service-web-get-started-python-cli-nodejs.md)
> * [Första Java-app](app-service-web-get-started-java.md)
> 
> 

I den här kursen lär du dig hur du distribuerar en enkel webbapp med HTML+CSS via [Azure App Service](../app-service/app-service-value-prop-what-is.md).
Du kan använda App Service för att skapa webbappar, [serverdelar för mobilappar](/documentation/learning-paths/appservice-mobileapps/) och [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du kommer att: 

* Skapa en webbapp i Azure App Service.
* Distribuera HTML och CSS till den.
* Se hur dina sidor körs live i produktionen.
* Uppdatera innehållet på samma sätt som du [skickar Git-skrivningar](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](app-service-web-get-started-html-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering
- [Azure CLI 2.0 (förhandsversion)](app-service-web-get-started-html.md) –vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="prerequisites"></a>Krav
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI](../xplat-cli-install.md).
* Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Du kan [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="deploy-a-simple-html-site"></a>Distribuera en enkel HTML-webbplats
1. Öppna en ny Windows-kommandotolk, ett PowerShell-fönster, Linux-gränssnittet eller en OS X-terminal. Kör `git --version` och `azure --version` för att kontrollera att Git och Azure CLI är installerade på datorn.
   
    ![Testa installationen av CLI-verktygen för din första webbapp i Azure](./media/app-service-web-get-started/1-test-tools.png)
   
    Om du inte har installerat verktygen använder du nedladdningslänkarna under [Krav](#Prerequisites).
2. Logga in i Azure så här:
   
        azure login
   
    Fortsätt inloggningen genom att följa anvisningarna i hjälpmeddelandet.
   
    ![Logga in i Azure för att skapa din första webbapp](./media/app-service-web-get-started/3-azure-login.png)

3. Ändra Azure CLI till ASM-läge och ställ sedan in distributionsanvändaren för App Service. Du distribuerar kod med autentiseringsuppgifterna senare.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. Ändra till en arbetskatalog (`CD`) och klona HTML-webbplatsexemplet så här:
   
        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

5. Ändra till lagringsplatsen för din exempelapp. 
   
        cd app-service-web-html-get-started

6. Skapa App Service-appresursen i Azure med ett unikt appnamn och distributionsanvändaren du konfigurerade tidigare. Ange numret för den region som du vill använda när du blir uppmanad att göra det.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Skapa Azure-resursen för din första webbapp i Azure](./media/app-service-web-get-started/4-create-site.png)
   
    Nu skapas din app i Azure. Dessutom Git-initieras din aktuella katalog och ansluts till den nya App Service-appen som ett Git-fjärrobjekt.
    Du kan bläddra till app-URL:en (http://&lt;appnamn>.azurewebsites.net) och visa HTML-standardsidan, men nu ska vi i stället se till att få dit din kod.
7. Distribuera din exempelkod till Azure-appen på samma sätt som du skickar annan kod med Git. Ange lösenordet som du konfigurerade tidigare när du blir uppmanad att göra det.
   
        git push azure master
   
    ![Skicka koden till din första webbapp i Azure](./media/app-service-web-get-started/5-push-code.png)
   
    Om du använde något av språkramverken ser resultatet annorlunda ut. Detta beror på att `git push` inte bara placerar kod i Azure, men även utlöser distributionsuppgifter i distributionsmotorn. Om det finns en package.json- (Node.js) eller requirements.txt-fil (Python) i projektroten (lagerplatsen) eller om det finns en packages.config-fil i ASP.NET-projektet så återställer distributionsskriptet de nödvändiga paketen åt dig. Du kan också [aktivera tillägget Composer](web-sites-php-mysql-deploy-use-git.md#composer) om du vill att composer.json-filer i din PHP-app ska bearbetas automatiskt.

Grattis, du har distribuerat din app till Azure App Service.

## <a name="see-your-app-running-live"></a>Köra appen live
Du kan köra appen live i Azure genom att köra det här kommandot från valfri katalog i databasen:

    azure site browse

## <a name="make-updates-to-your-app"></a>Göra appuppdateringar
Nu kan du använda Git och skicka skrivningar från projektroten (databasen) när som helst för att uppdatera liveplatsen. Du gör på samma sätt som när du distribuerade din kod första gången. Exempelvis kör du bara följande kommandon från projektroten (databasen) varje gång du vill skicka in en ny ändring som du har testat lokalt:

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Nästa steg
Hitta de rekommenderade utvecklings- och distributionsanvisningarna för ditt språkramverk:

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started-cli-nodejs.md)
* [Node.js](app-service-web-nodejs-get-started-cli-nodejs.md)
* [Python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

Eller gör mer med din första webbapp. Exempel:

* Prova [andra sätt att distribuera din kod till Azure](web-sites-deploy.md). Markera till exempel **GitHub** istället för **Lokal Git-lagringsplats** i **Distributionsalternativ** för att distribuera från någon av dina GitHub-lagringsplatser.
* Ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar. Mer information finns i [Lägga till funktioner till din första webbapp](app-service-web-get-started-2.md).




<!--HONumber=Jan17_HO1-->


