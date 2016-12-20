---
title: Skapa en Node.js-webbapp i Azure App Service | Microsoft Docs
description: "Lär dig hur du distribuerar ett Node.js-program till en webbapp i Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 85af23df-54af-4430-8d77-a1f97e2f5b10
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2537b0c4dcc71d02d25959a0b21d96bce7deaac6


---
# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Skapa en Node.js-webbapp i Azure App Service
> [!div class="op_single_selector"]
> * [.Net](web-sites-dotnet-get-started.md)
> * [Node.js](web-sites-nodejs-develop-deploy-mac.md)
> * [Java](web-sites-java-get-started.md)
> * [PHP – Git](web-sites-php-mysql-deploy-use-git.md)
> * [PHP – FTP](web-sites-php-mysql-deploy-use-ftp.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> 
> 

I den här kursen får du veta hur du skapar ett enkelt [Node.js](http://nodejs.org)-program och distribuerar det till en [webbapp](app-service-web-overview.md) i [Azure App Service](../app-service/app-service-value-prop-what-is.md) med hjälp av [Git](http://git-scm.com). Anvisningarna i den här kursen kan tillämpas på alla operativsystem som kan köra Node.js.

Du får lära dig:

* skapa en webbapp i Azure App Service med hjälp av Azure Portal.
* distribuera ett Node.js-program till webbappen genom push-överföring till webbappens Git-lagringsplats.

Det färdiga programmet skriver en kort hälsning i webbläsaren.

![Webbläsare med hälsningsmeddelande][helloworld-completed]

I [Node.js Developer Center](/develop/nodejs/) hittar du kurser och exempelkod för mer komplexa Node.js-program och andra artiklar om hur du använder Node.js i Azure.

> [!NOTE]
> För den här kursen behöver du ett Microsoft Azure-konto. Om du inte har ett konto kan du [aktivera Visual Studio-prenumerantförmåner](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) eller [registrera dig för en kostnadsfri utvärderingsversion](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort behövs och du gör inga åtaganden.
> 
> 

## <a name="create-a-web-app-and-enable-git-publishing"></a>Skapa en webbapp och aktivera Git-publicering
Följ de här anvisningarna för att skapa en webbapp i Azure App Service och aktivera Git-publicering. 

[Git](http://git-scm.com/) är ett kontrollsystem för distribuerade versioner som du kan använda till att distribuera din Azure-webbplats. Du lagrar koden du skriver för webbappen på en lokal Git-lagringsplats och du distribuerar koden till Azure genom att push-överföra den till en fjärransluten lagringsplats. Den här distributionsmetoden gäller för App Service-webbappar.  

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ikonen **+ NY** högst upp till vänster i Azure Portal.
3. Klicka på **Webb + mobilt** och sedan på **Webbapp**.
   
    ![][portal-quick-create]
4. Ange ett namn på webbappen i rutan **Webbapp**.
   
    Det här namnet måste vara unikt i domänen azurewebsites.net eftersom webbappens webbadress är {namn}.azurewebsites.net. Om namnet inte är unikt visas ett rött utropstecken i textrutan.
5. Välj en **prenumeration**.
6. Välj en **Resursgrupp** eller skapa en ny.
   
    Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Välj en **App Service-plan/plats** eller skapa en ny.
   
    Mer information om App Service-planer finns i [Översikt över Azure App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
8. Klicka på **Skapa**.
   
    ![][portal-quick-create2]
   
    Azure skapar snabbt den nya webbappen. Det går vanligen på mindre än en minut.
9. Klicka på **Webbappar > {din nya webbapp}**.
   
    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)
10. I bladet **Webbapp** klickar du på delen **Distribution**.
    
    ![][deployment-part]
11. I bladet **Kontinuerlig distribution** klickar du på **Välj källa**
12. Klicka på **Lokal Git-lagringsplats** och sedan på **OK**.
    
    ![][setup-git-publishing]
13. Ange autentiseringsuppgifter för distribution om du inte redan gjort det.
    
    a. I bladet Webbapp klickar du på **Inställningar > Autentiseringsuppgifter för distribution**.
    
    ![][deployment-credentials]
    
    b. Skapa ett användarnamn och lösenord. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)
14. I bladet Webbapp klickar du på **Inställningar** och sedan på **Egenskaper**.
    
    Du publicerar genom att push-överföra till en fjärransluten Git-lagringsplats. Webbadressen för lagringsplatsen anges under **Git-URL**. Du kommer att använda webbadressen senare under kursen.
    
    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Skapa och testa programmet lokalt
I det här avsnittet får du skapa en **server.js**-fil som innehåller en något justerad version av exemplet med hälsningsmeddelandet"Hello World" från [nodejs.org]. Koden lägger till process.env.PORT som port för inkommande kommunikation när den körs i en Azure-webbapp.

1. Skapa en katalog med namnet *helloworld*.
2. Använd en textredigerare till att skapa en ny fil med namnet **server.js** i katalogen *helloworld*.
3. Kopiera in följande kod i **server.js**-filen och spara filen:
   
        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);
4. Öppna kommandoraden och använd följande kommando för att starta webbappen lokalt.
   
        node server.js
5. Öppna webbläsaren och gå till http://localhost:1337. 
   
    En webbsida med hälsningsmeddelandet visas som i följande skärmbild.
   
    ![Webbläsare med hälsningsmeddelande][helloworld-localhost]

## <a name="publish-your-application"></a>Publicera programmet
1. Installera Git om du inte redan gjort det.
   
    Installationsanvisningar för din plattform finns på [Git-nedladdningssidan](http://git-scm.com/download).
2. Från kommandoraden ändrar du katalog till katalogen **helloworld** och anger följande kommando för att initiera en lokal Git-lagringsplats.
   
        git init
3. Använd följande kommandon för att lägga till filer på lagringsplatsen:
   
        git add .
        git commit -m "initial commit"
4. Använd nedanstående kommando till att lägga till en fjärransluten Git-lagringsplats för att push-överföra uppdateringar till webbappen du skapade tidigare:
   
        git remote add azure [URL for remote repository]
5. Pusha ändringarna till Azure med hjälp av följande kommando:
   
        git push azure master
   
    Du uppmanas att ange lösenordet du skapade tidigare. De utdata som genereras påminner om de i följande exempel.
   
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master
6. Du visar appen genom att klicka på knappen **Bläddra** i området **Webbapp** i Azure portal.
   
    ![Knappen Bläddra](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)
   
    ![Hälsningsmeddelande i Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Publicera ändringar i programmet
1. Öppna **server.js**-filen i en textredigerare och ändra 'Hello World\n' till 'Hello Azure\n'. 
2. Spara filen.
3. Från kommandoraden ändrar du katalog till katalogen **helloworld** och kör följande kommandon.
   
        git add .
        git commit -m "changing to hello azure"
        git push azure master
   
    Du uppmanas att ange lösenordet igen.
4. Uppdatera webbläsarfönstret som du navigerade till webbappens webbadress i.
   
    ![En webbsida med hälsningsmeddelandet "Hello Azure"][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Återställa en distribution
Från bladet **Webbapp** kan du klicka på **Inställningar > Kontinuerlig distribution** om du vill visa distributionshistoriken i bladet **Distributioner**. Om du behöver återställa till en tidigare distribution kan du välja den och sedan klicka på **Distribuera om** i bladet **Distributionsinformation**.

## <a name="next-steps"></a>Nästa steg
Du har distribuerat ett Node.js-program till en webbapp i Azure App Service. Läs mer om hur Webbappar i App Service kör Node.js-program i [Azure App Service Web Apps](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) och [ Ange en Node.js-version i ett Azure-program](../nodejs-specify-node-version-azure-apps.md).

Med Node.js får du tillgång till ett omfattande utbud moduler som kan användas av programmen. Läs mer om hur Web Apps fungerar med moduler i [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md).

Om du får problem med programmet när det har distribuerats till Azure kan du läsa [Felsöka ett Node.js-program i Azure App Service](web-sites-nodejs-debug.md) där du hittar information om hur du felsöker problemet.

I den här artikeln används Azure Portal till att skapa en webbapp. Du kan också använda [Azure-kommandoradsgränssnittet](../xplat-cli-install.md) eller [Azure PowerShell](/powershell/azureps-cmdlets-docs) till att utföra samma åtgärder.

Mer information om hur du utvecklar Node.js-program i Azure finns i [Node.js Developer Center](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png



<!--HONumber=Dec16_HO1-->


