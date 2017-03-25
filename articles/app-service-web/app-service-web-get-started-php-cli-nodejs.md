---
title: "Distribuera din första PHP-webbapp via Azure på fem minuter | Microsoft Docs"
description: "Distribuera en PHP-exempelapp och se hur enkelt det är att köra webbappar i App Service. Börja snabbt utveckla på riktigt och se resultatet direkt."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 21acd587-b772-4d89-be06-9a7429c33c7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3db31549b02fbcb72ce2b0c5da32d7800f8287f3
ms.lasthandoff: 03/21/2017


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes"></a>Distribuera din första PHP-webbapp via Azure på fem minuter
[!INCLUDE [app-service-web-selector-get-started-nodejs-cli](../../includes/app-service-web-selector-get-started-nodejs-cli.md)]  

I de här självstudierna lär du dig hur du distribuerar din första PHP-webbapp via [Azure App Service](../app-service/app-service-value-prop-what-is.md).
Du kan använda App Service för att skapa webbappar, [serverdelar för mobilappar](/documentation/learning-paths/appservice-mobileapps/) och [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du kommer att: 

* Skapa en webbapp i Azure App Service.
* Distribuera PHP-exempelkod.
* Se hur din kod körs live i produktionen.
* Uppdatera webbappen på samma sätt som du [skickar Git-skrivningar](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](app-service-web-get-started-php-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering
- [Azure CLI 2.0](app-service-web-get-started-php.md) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="prerequisites"></a>Krav
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI](../cli-install-nodejs.md).
* Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="deploy-a-php-web-app"></a>Distribuera en PHP-webbapp
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
4. Ändra till en arbetskatalog (`CD`) och klona PHP-exempelappen så här:
   
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git
5. Ändra till lagringsplatsen för din exempelapp. Exempel:
   
        cd app-service-web-php-get-started
6. Skapa App Service-appresursen i Azure med ett unikt appnamn och distributionsanvändaren du konfigurerade tidigare. Ange numret för den region som du vill använda när du blir uppmanad att göra det.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Skapa Azure-resursen för din första webbapp i Azure](./media/app-service-web-get-started-languages/php-site-create.png)
   
    Nu skapas din app i Azure. Dessutom Git-initieras din aktuella katalog och ansluts till den nya App Service-appen som ett Git-fjärrobjekt.
    Du kan bläddra till app-URL:en (http://&lt;appnamn>.azurewebsites.net) och visa HTML-standardsidan, men nu ska vi i stället se till att få dit din kod.
7. Distribuera din exempelkod till Azure-appen på samma sätt som du skickar annan kod med Git. Ange lösenordet som du konfigurerade tidigare när du blir uppmanad att göra det.
   
        git push azure master
   
    ![Skicka koden till din första webbapp i Azure](./media/app-service-web-get-started-languages/php-git-push.png)
   
    `git push` placerar inte bara kod i Azure, utan utlöser även distributionsuppgifter i distributionsmotorn. Du kan också  [aktivera tillägget Composer](web-sites-php-mysql-deploy-use-git.md#composer) om du vill att composer.json-filer i din PHP-app ska bearbetas automatiskt.

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
[Skapa, konfigurera och distribuera en Laravel-webbapp via Azure](app-service-web-php-get-started-cli-nodejs.md). I de här självstudierna får du grundläggande kunskaper i hur du kör PHP-webbappar i Azure, till exempel:

* Skapa och konfigurera appar i Azure från PowerShell/Bash.
* Ange PHP-version.
* Använda en startfil som inte finns i rotkatalogen för programmet.
* Aktivera Composer-automatisering.
* Få åtkomst till miljöspecifika variabler.
* Felsöka vanliga fel.

Eller gör mer med din första webbapp. Exempel:

* Prova [andra sätt att distribuera din kod till Azure](web-sites-deploy.md). Markera till exempel **GitHub** istället för **Lokal Git-lagringsplats** i **Distributionsalternativ** för att distribuera från någon av dina GitHub-lagringsplatser.
* Ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar. Mer information finns i [Lägga till funktioner till din första webbapp](app-service-web-get-started-2.md).


