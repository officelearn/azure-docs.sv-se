<properties 
    pageTitle="Distribuera din första webbapp till Azure på fem minuter" 
    description="Distribuera en exempelapp i några få steg och se hur enkelt det är att köra webbappar i App Service. Börja utveckla på riktigt inom fem minuter och se resultatet direkt." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin" 
    manager="wpickett" 
    editor="" 
/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="05/12/2016" 
    ms.author="cephalin"
/>
    
# Distribuera din första webbapp till Azure på fem minuter

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen lär du dig hur du distribuerar din första webbapp till [Azure App Service](../app-service/app-service-value-prop-what-is.md). Med App Service kan du skapa webbappar, [serverdelar för mobilappar](/documentation/learning-paths/appservice-mobileapps/) och [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Med minimal ansträngning från din sida ska du: 

- Distribuera en exempelwebbapp (välj mellan ASP.NET, PHP, Node.js, Java och Python).
- Se hur appen körs live på bara några sekunder.
- Uppdatera webbappen på samma sätt som du [skickar Git-skrivningar](https://git-scm.com/docs/git-push).

Vi ska också ta en första titt på [Azure Portal](https://portal.azure.com) och de funktioner som är tillgängliga där. 

## Krav

- [Installera Git](http://www.git-scm.com/downloads). 
- [Installera Azure CLI](../xplat-cli-install.md). 
- Skaffa ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Se hur en webbapp fungerar i praktiken. [Testa App Service](http://go.microsoft.com/fwlink/?LinkId=523751) direkt och skapa en kortvarig startapp – inget kreditkort behövs, inga åtaganden.

## Distribuera en webbapp

Nu ska vi distribuera en webbapp till Azure App Service. 

1. Öppna en ny Windows-kommandotolk, ett PowerShell-fönster, Linux-gränssnittet eller en OS X-terminal. Kör `git --version` och `azure --version` för att verifiera att Git och Azure CLI är installerade på datorn. 

    ![Testa installationen av CLI-verktygen för din första webbapp i Azure](./media/app-service-web-get-started/1-test-tools.png)

    Om du inte har installerat verktygen använder du nedladdningslänkarna under [Krav](#Prerequisites).

1. `CD` till en arbetskatalog och klona exempelappen så här:

        git clone <github_sample_url>

    ![Klona exempelkoden för din första webbapp i Azure](./media/app-service-web-get-started/2-clone-sample.png)

    För *&lt;github_sample_url>* använder du någon av följande webbadresser, beroende vilket ramverk du föredrar: 

    - HTML+CSS+JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. `CD` till lagringsplatsen för din exempelapp. Exempel: 

        cd app-service-web-html-get-started

3. Logga in i Azure så här:

        azure login
    
    Fortsätt inloggningen genom att följa anvisningarna i hjälpmeddelandet.
    
    ![Logga in i Azure för att skapa din första webbapp](./media/app-service-web-get-started/3-azure-login.png)

4. Skapa appresursen för App Service i Azure med ett unikt appnamn med nästa kommando. När du uppmanas att göra det anger du numret för den region som du vill använda.

        azure site create --git <app_name>
    
    ![Skapa Azure-resursen för din första webbapp i Azure](./media/app-service-web-get-started/4-create-site.png)
    
    >[AZURE.NOTE] Om du aldrig har konfigurerat distributionsuppgifter för din Azure-prenumeration uppmanas du att skapa dem. Dessa autentiseringsuppgifter, inte dina Azure-kontoautentiseringsuppgifter, används endast av App Service för Git-distributioner och FTP-inloggningar. 
    
    Nu skapas din app i Azure. Dessutom Git-initieras din aktuella katalog och ansluts till den nya App Service-appen som ett Git-fjärrobjekt.
    Du kan bläddra till app-URL:en (http://&lt;appnamn>. azurewebsites.net) och visa HTML-standardsidan, men vi ska i stället se till att få dit din kod.

4. Distribuera din exempelkod till den nya App Service-appen på samma sätt som du skickar annan kod med Git:

        git push azure master 

    ![Skicka koden till din första webbapp i Azure](./media/app-service-web-get-started/5-push-code.png)    
    
    Om du använde något av språkramverken ser resultatet inte ut som ovan. Detta beror på att `git push` inte bara placerar kod i Azure, men även utlöser distributionsuppgifter i distributionsmotorn. Om det finns en package.json- (Node.js) eller requirements.txt-fil (Python) i projektroten (databasen) eller om det finns en packages.config-fil i ASP.NET-projektet så återställer distributionsskriptet de nödvändiga paketen åt dig. Du kan också [aktivera tillägget Composer](web-sites-php-mysql-deploy-use-git.md#composer) om du vill att composer.json-filer i din PHP-app ska bearbetas automatiskt.

Grattis, du har distribuerat din app till Azure App Service. 

## Köra appen live

Du kan köra appen live i Azure genom att köra det här kommandot från valfri katalog i databasen:

    azure site browse

## Göra appuppdateringar

Nu kan du använda Git och skicka skrivningar från projektroten (databasen) när som helst för att uppdatera liveplatsen. Du gör på samma sätt som när du distribuerade appen till Azure för första gången. Exempelvis kör du bara följande kommandon från projektroten (databasen) varje gång du vill skicka in en ny ändring som du har testat lokalt:
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Visa appen på Azure Portal

Nu ska vi gå till Azure Portal och se vad du har skapat:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett Microsoft-konto med din Azure-prenumeration.

2. Klicka på **App Services** i fältet till vänster.

3. Klicka på appen som du precis har skapat för att öppna appens sida på portalen (kallas ett [blad](../azure-portal-overview.md)). Bladet **Inställningar** öppnas också som standard för att underlätta för dig.

    ![Din första webbapp på Azure Portal](./media/app-service-web-get-started/portal-view.png) 

Bladet för din App Service-app på portalen innehåller en omfattande uppsättning inställningar och verktyg som du kan använda för att konfigurera, övervaka, skydda och felsöka appen. Ägna en stund åt att bekanta dig med det här gränssnittet genom att utföra några enkla uppgifter (uppgiftens nummer motsvarar numret i skärmbilden):

1. stoppa appen
2. starta om appen
3. klicka på länken **Resursgrupp** om du vill visa alla resurser som har distribuerats i resursgruppen
4. klicka på **Inställningar** > **Egenskaper** om du vill visa annan information om appen
5. klicka på **Verktyg** om du vill komma åt användbara verktyg för övervakning och felsökning  

## Nästa steg

- Ta din Azure-app till nästa nivå. Skydda den med autentisering. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar. Mer information finns i [Lägga till funktioner till din första webbapp](app-service-web-get-started-2.md).
- Det finns andra sätt att distribuera webbappar till Azure förutom Git och Azure CLI (se [Distribuera din app till Azure App Service](../app-service-web/web-sites-deploy.md)).
Hitta lämpliga utvecklings- och distributionssteg för ditt språkramverk genom att välja relevant ramverk överst i artikeln.



<!--HONumber=Jun16_HO2-->


