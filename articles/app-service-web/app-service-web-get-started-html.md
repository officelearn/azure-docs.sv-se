<properties 
    pageTitle="Distribuera din första webbapp till Azure på fem minuter | Microsoft Azure" 
    description="Distribuera en exempelapp och se hur enkelt det är att köra webbappar i App Service. Börja snabbt utveckla på riktigt och se resultatet direkt." 
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
    ms.date="09/16/2016" 
    ms.author="cephalin"
/>
    
# Distribuera din första webbapp till Azure på fem minuter

I den här kursen lär du dig hur du distribuerar en enkel webbapp med HTML+CSS via [Azure App Service](../app-service/app-service-value-prop-what-is.md).
Du kan använda App Service för att skapa webbappar, [serverdelar för mobilappar](/documentation/learning-paths/appservice-mobileapps/) och [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du kommer att: 

- Skapa en webbapp i Azure App Service.
- Distribuera HTML och CSS till den.
- Se hur dina sidor körs live i produktionen.
- Uppdatera innehållet på samma sätt som du [skickar Git-skrivningar](https://git-scm.com/docs/git-push).

## Krav

- [Installera Git](http://www.git-scm.com/downloads). Kontrollera att installationen har utförts genom att köra `git --version` från en ny Windows-kommandotolk, PowerShell-fönster, Linux-gränssnittet eller OS X-terminal.
- Skaffa ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Du kan [prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.

<a name="create"></a>
## Skapa en webbapp

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

2. I den vänstra menyn klickar du på **Ny** > **Webb + Mobilt** > **Webbapp**.

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. Använd följande inställningar för din nya app i bladet där appen skapas:

    - **Appnamn**: Skriv ett unikt namn.
    - **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.
    - **App Service-plan/plats**: Klicka om du vill konfigurera och klicka sedan på **Skapa ny** för att ställa in namn, plats och prisnivå för App Service-planen. Använd gärna den **Kostnadsfria** prisnivån.

    När du är klar bör bladet där du skapar din app se ut så här:

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. Klicka på **Skapa** längst ned. Du kan klicka på ikonen **Meddelande** överst för att se förloppet.

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. Du bör se detta meddelande när distributionen är klar. Klicka på meddelandet för att öppna bladet för din distribution.

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. I bladet **Distributionen lyckades** klickar du på länken **Resurs** för att öppna bladet för din nya webbapp.

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## Distribuera innehåll till din webbapp

Nu ska vi distribuera innehåll till Azure med Git.

5. Rulla ner till **Distributionsalternativ** i bladet webbapp eller sök efter dem, och klicka sedan på dem. 

    ![](./media/app-service-web-get-started-languages/deploy-web-app-deployment-options.png)

6. Klicka på **Välj källa** > **Lokal Git-lagringsplats** > **OK**.

7. Gå tillbaka till webbappsbladet och klicka på **Autentiseringsuppgifter för distribution**.

8. Ange dina autentiseringsuppgifter för distribution och klicka på **Spara**.

7. Gå tillbaka till bladet webbapp, rulla ner till **Egenskaper** eller sök efter dem och klicka sedan på dem. Klicka på knappen **Kopiera** bredvid **Git-URL**.

    ![](./media/app-service-web-get-started-languages/deploy-web-app-properties.png)

    Nu är du redo att distribuera ditt innehåll med Git.

1. Ändra till en arbetskatalog i din kommandoradsterminal (`CD`) och klona exempelappen så här:

        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

    ![Klona exempelkoden för din första webbapp i Azure](./media/app-service-web-get-started-languages/html-git-clone.png)

2. Ändra till lagringsplatsen för din exempelapp. Exempel: 

        cd app-service-web-html-get-started

3. Konfigurera din fjärranslutna Git för din Azure-app med dess Git-URL som du kopierade från portalen några steg tidigare.

        git remote add azure <giturlfromportal>

4. Distribuera din exempelkod till din Azure-app på samma sätt som du skickar annan kod med Git:

        git push azure master

    ![Skicka koden till din första webbapp i Azure](./media/app-service-web-get-started-languages/html-git-push.png)    

Klart! Nu körs din kod live i Azure. Navigera till http://*&lt;appname >*.azurewebsites.net i din webbläsare för att se hur det fungerar. 

## Göra appuppdateringar

Nu kan du använda Git och skicka skrivningar från projektroten (databasen) när som helst för att uppdatera liveplatsen. Du gör på samma sätt som när du distribuerade ditt innehåll första gången. Exempelvis kör du bara följande kommandon från projektroten (databasen) varje gång du vill skicka in en ny ändring som du har testat lokalt:

    git add .
    git commit -m "<your_message>"
    git push azure master

## Nästa steg

Hitta de rekommenderade utvecklings- och distributionsanvisningarna för ditt språkramverk:

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [PHP](app-service-web-php-get-started.md)
- [Node.js](app-service-web-nodejs-get-started.md)
- [Python](web-sites-python-ptvs-django-mysql.md)
- [Java](web-sites-java-get-started.md)

Eller gör mer med din första webbapp. Exempel:

- Prova [andra sätt att distribuera din kod till Azure](../app-service-web/web-sites-deploy.md). Markera till exempel **GitHub** istället för **Lokal Git-lagringsplats** i **Distributionsalternativ** för att distribuera från någon av dina GitHub-lagringsplatser.
- Ta din Azure-app till nästa nivå. Autentisera användarna. Skala den på begäran. Konfigurera prestandavarningar. Allt med några få klickningar. Mer information finns i [Lägga till funktioner till din första webbapp](app-service-web-get-started-2.md).




<!---HONumber=Sep16_HO4-->


