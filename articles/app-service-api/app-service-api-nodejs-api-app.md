---
title: "Node.js API-app i Azure Apptjänst | Microsoft Docs"
description: "Lär dig hur du skapar en Node.js RESTful-API och distribuera det till en API-app i Azure Apptjänst."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: wpickett
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: get-started-article
ms.date: 05/26/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e8a1ac3df5225fdcfe2717c2cf50bfc5b7cfda36


---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Skapa en Node.js RESTful-API och distribuera den till en API-app i Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

I den här kursen får du veta hur du skapar ett enkelt [Node.js](http://nodejs.org)-API och distribuerar det till en [API-app](app-service-api-apps-why-best-platform.md) i [Azure Apptjänst](../app-service/app-service-value-prop-what-is.md) med hjälp av [Git](http://git-scm.com). Du kan använda alla operativsystem som kan köra Node.js och du  gör allt arbete med hjälp av kommandoradsverktyg, till exempel cmd.exe eller bash.

## <a name="prerequisites"></a>Krav
1. Microsoft Azure-konto ([öppna ett kostnadsfritt konto här](https://azure.microsoft.com/pricing/free-trial/))
2. [Node.js](http://nodejs.org) installerat (det här exemplet förutsätter att du har Node.js version 4.2.2)
3. [Git](https://git-scm.com/) installerat
4. [GitHub](https://github.com/)-konto

Med Apptjänst kan du distribuera din kod till en API-app på många olika sätt men den här kursen visar Git-metoden och förutsätter att du har grundläggande kunskaper om att arbeta med Git. Information om andra distributionsmetoder finns i [Distribuera din app till Azure Apptjänst](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Hämta exempelkoden
1. Öppna ett kommandoradsgränssnitt som kan köra Node.js- och Git-kommandon.
2. Navigera till en mapp som du kan använda för en lokal Git-lagringsplats och klona den [GitHub-lagringsplats som innehåller exempelkoden](https://github.com/Azure-Samples/app-service-api-node-contact-list).
   
        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
   
    Exempel-API:n ger två slutpunkter: en Hämta-begäran till `/contacts` returnerar en lista med namn och e-postadresser i JSON-format, medan `/contacts/{id}` bara returnerar den valda kontakten.

## <a name="scaffold-autogenerate-nodejs-code-based-on-swagger-metadata"></a>Autogenererad Node.js-kod baserat på Swagger-metadata
[Swagger](http://swagger.io/) är ett filformat för metadata som beskriver ett RESTful-API. Azure Apptjänst har [inbyggt stöd för Swagger-metadata](app-service-api-metadata.md). Det här avsnittet av kursen visar ett arbetsflöde för API-utveckling där du först skapar Swagger-metadata och använder dem för att autogenerera en serverkod för API:et. 

> [!NOTE]
> Du kan hoppa över det här avsnittet om du vill lära dig hur du automatiskt kan generera Node.js-kod från en Swagger-metadatafil. Om du bara vill distribuera exempelkod till en ny API-app, gå direkt till avsnittet [Skapa en API-app i Azure](#createapiapp).
> 
> 

### <a name="install-and-execute-swaggerize"></a>Installera och köra Swaggerize
1. Kör följande kommandon för att installera NPM-modulerna **yo** och **generator-swaggerize** globalt.
   
        npm install -g yo
        npm install -g generator-swaggerize
   
    Swaggerize är ett verktyg som genererar serverkod för ett API som beskrivs av en Swagger-metadatafil. Swagger-filen som du ska använda heter *api.json* och finns i mappen *Starta* för den lagringsplats du har klonat.
2. Navigera till mappen *Starta* och kör `yo swaggerize`-kommandot. Swaggerize ställer ett antal frågor.  För **vad projektet ska heta** anger du "Kontaktlista". För **sökväg till swagger-dokument** anger du "api.json" och för **Express, Hapi eller Restify** anger du "express".
   
        yo swaggerize
   
    ![Swaggerize kommandorad](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
   
    **Obs**! Om det uppstår ett fel i det här steget förklaras det i nästa steg  hur du åtgärdar det.
   
    Swaggerize skapar en mapp för programmet, autogenererar hanterare och konfigurationsfiler och genererar en **package.json**-fil. Expressvisningsmotorn används för att generera hjälpsidan för Swagger.  
3. Om `swaggerize`-kommandot misslyckas med ett fel för "oväntad token" eller "ogiltig avbrottssekvens" åtgärdar du orsaken till felet genom att redigera den genererade *package.json*-filen. I `regenerate`-raden under `scripts` ändrar du det omvända snedstreck som föregår *api.json* till snedstreck så att raden ser ut som i följande exempel:
   
         "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"
4. Navigera till mappen som innehåller den autogenererade koden (i det här fallet undermappen */start/Kontaktlista*).
5. Kör `npm install`.
   
        npm install
6. Installera NPM-modulen **jsonpath**. 
   
        npm install --save jsonpath
   
    ![Jsonpath-installation](media/app-service-api-nodejs-api-app/jsonpath-install.png)
7. Installera NPM-modulen **swaggerize ui**. 
   
        npm install --save swaggerize-ui
   
    ![Swaggerize Ui-installation](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Anpassa den autogenererade koden
1. Kopiera mappen **lib** från mappen **Starta** till mappen **Kontaktlista** som skapats av autogeneratorn. 
2. Ersätt koden i filen **handlers/contacts.js** med följande kod. 
   
    Den här koden använder JSON-data som lagras i **lib/contacts.json**-filen som hanteras av **lib/contactRepository.js**. Den nya contacts.js-koden svarar på HTTP-begäranden att hämta alla kontakter och returnera dem som en JSON-nyttolast. 
   
        'use strict';
   
        var repository = require('../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };
3. Ersätt koden i filen **handlers/contacts/{id}.js** med följande kod. 
   
        'use strict';
   
        var repository = require('../../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };
4. Ersätt koden i **server.js** med följande kod. 
   
    Ändringar som görs i filen server.js markeras med kommentarer så att du kan se ändringarna. 
   
        'use strict';
   
        var port = process.env.PORT || 8000; // first change
   
        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');
   
        var app = express();
   
        var server = http.createServer(app);
   
        app.use(bodyParser.json());
   
        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));
   
        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));
   
        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Testa med API som körs lokalt
1. Aktivera servern med den körbara filen för Node.js-kommandoraden. 
   
        node server.js
2. När du bläddrar till **http://localhost:8000/contacts** ser du JSON-utdatan från kontaktlistan (eller så uppmanas du att hämta den, beroende på din webbläsare). 
   
    ![Api-anrop för alla kontakter](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)
3. När du bläddrar till **2-http://localhost:8000/contacts** ser du kontakten som representeras av detta id-värde.
   
    ![Api-anrop för specifik kontakt ](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)
4. Swagger JSON-data hanteras med **/swagger**-slutpunkten:
   
    ![Swagger Json för kontakter](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)
5. Användargränssnittet för Swagger hanteras via **/docs**-slutpunkten. Du kan använda omfattande HTML-klientfunktionerna i användargränssnittet för Swagger för att testa ditt API.
   
    ![Användargränssnittet för Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a name="a-idcreateapiappa-create-a-new-api-app"></a><a id="createapiapp"></a>Skapa en ny API-app
I det här avsnittet använder du Azure-portalen för att skapa en ny API-app i Azure. Den här API-appen representerar de beräkningsresurser som Azure tillhandahåller för att köra din kod. Du kommer att distribuera din kod till den nya API-appen i senare avsnitt.

1. Bläddra till [Azure-portalen](https://portal.azure.com/). 
2. Klicka på **Ny > Webb + mobilt > API-app**. 
   
    ![Ny API-app i portalen](media/app-service-api-nodejs-api-app/new-api-app-portal.png)
3. Ange ett **appnamn** som är unikt i domänen *azurewebsites.net*, till exempel NodejsAPIApp plus ett tal som gör det unikt. 
   
    Om namnet till exempel är `NodejsAPIApp` blir URL:en `nodejsapiapp.azurewebsites.net`.
   
    Om du anger ett namn som någon annan redan har använt visas ett rött utropstecken till höger.
4. I listrutan **Resursgrupp** klickar du på **Ny**. I **Namn på ny resursgrupp** anger du sedan "NodejsAPIAppGroup" eller ett annat namn. 
   
    En [resursgrupp](../azure-resource-manager/resource-group-overview.md) är en samling Azure-resurser, t.ex. API-appar, databaser och virtuella datorer. För denna kurs är det bäst att skapa en ny resursgrupp eftersom det gör att du med bara ett steg enkelt kan ta bort alla Azure-resurser som du skapar under kursen.
5. Klicka på **Apptjänstplan/plats** och klicka sedan på **Skapa ny**
   
    ![Skapa apptjänstplan](./media/app-service-api-nodejs-api-app/newappserviceplan.png)
   
    I följande steg får du skapa en apptjänstplan för den nya resursgruppen. I en apptjänstplan anges beräkningsresurserna som API-appen körs på. Om du till exempel väljer den kostnadsfria nivån körs API-appen på delade virtuella datorer medan den körs på dedikerade virtuella datorer för vissa betalnivåer. Information om apptjänstplaner finns i [Översikt över Apptjänstplaner](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
6. I bladet **Apptjänstplan** anger du "NodejsAPIAppPlan" eller ett annat namn om du föredrar det.
7. I listrutan **Plats** väljer du den plats som är närmast dig.

   
    Den här inställningen anger vilket Azure-datacenter appen ska köras i. I den här kursen kan du välja en region utan att det gör någon märkbar skillnad. Men för en produktionsapp är det bra om servern finns så nära klienterna som använder den som möjligt för att minimera [fördröjningen](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).
8. Klicka på **Prisnivå > Visa alla > F1 kostnadsfri**.
   
    För den här kursens syfte räcker det att välja den kostnadsfria prisnivån.
   
    ![Välj den kostnadsfria prisnivån](./media/app-service-api-nodejs-api-app/selectfreetier.png)
9. I bladet **Apptjänstplan** klickar du på **OK**.
10. I bladet **API-app** klickar du på **Skapa**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Ställ in din nya API-app för Git-distribution
Du kommer att distribuera din kod till API-appen genom att skicka incheckningar till en Git-lagringsplats i Azure Apptjänst. I det här avsnittet av kursen skapar du de autentiseringsuppgifter och den Git-lagringsplats i Azure som du kommer använda för distribution.  

1. När du har skapat din API-app klickar du på **Apptjänster > {din API app}** från portalens startsida. 
   
    Portalen visar bladen **API-App** och **Inställningar**.
   
    ![Portalbladen API-app och Inställningar](media/app-service-api-nodejs-api-app/portalapiappblade.png)
2. I bladet **Inställningar** går du till avsnittet **Publicering**  och klickar sedan på **Autentiseringsuppgifter för distribution**.
3. I bladet **Ställ in autentiseringsuppgifter för distribution** anger du ett användarnamn och lösenord. Klicka sedan på **Spara**.
   
    Dessa autentiseringsuppgifter ska du använda för att publicera din Node.js-kod till API-appen. 
   
    ![Autentiseringsuppgifter för distribution](media/app-service-api-nodejs-api-app/deployment-credentials.png)
4. I bladet **Inställningar** klickar du på **Distributionskälla > Välj källa > Lokal Git-lagringsplats**. Klicka sedan på **OK**.
   
    ![Skapa Git Repo](media/app-service-api-nodejs-api-app/create-git-repo.png)
5. När Git-lagringsplatsen har skapats ändras bladet så att det visar dina aktiva distributioner. Eftersom lagringsplatsen är ny finns det inga aktiva distributioner i listan. 
   
    ![Inga aktiva distributioner](media/app-service-api-nodejs-api-app/no-active-deployments.png)
6. Kopiera URL:en för Git-lagringsplatsen. Gör detta genom att navigera till bladet för din nya API-app och titta på avsnittet **Essentials** på bladet. Lägg märke till **URL för Git-klonen** i avsnittet **Essentials**. När du hovrar över denna URL visas en ikon till höger som kommer att kopiera URL:en till Urklipp. Klicka på ikonen om du vill kopiera URL:en.
   
    ![Hämta Git-URL:en från portalen](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)
   
    **Obs**! Du behöver URL:en för Git-klonen i nästa avsnitt så se till att spara den någonstans.

Nu när du har en API-app med en Git-lagringsplats som den kan säkerhetskopieras till kan du skicka kod till lagringsplatsen för att distribuera koden till API-appen. 

## <a name="deploy-your-api-code-to-azure"></a>Distribuera din API-kod till Azure
I det här avsnittet skapar du en lokal Git-lagringsplats som innehåller din serverkod för API:et. Därefter skickar du koden från den lagringsplatsen till lagringsplatsen i Azure som du skapade tidigare.

1. Kopiera `ContactList`-mappen till en plats som du kan använda för en ny lokal Git-lagringsplats. Om du har gjort den första delen av kursen kopierar du `ContactList` från  `start`-mappen. Annars kopierar du `ContactList` från `end`-mappen.
2. Navigera till den nya mappen i kommandoradsverktyget och kör sedan följande kommando för att skapa en ny lokal Git-lagringsplats. 
   
        git init
   
     ![Ny lokal Git Repo](media/app-service-api-nodejs-api-app/new-local-git-repo.png)
3. Kör följande kommando för att lägga till en fjärransluten Git för API-appens lagringsplats. 
   
        git remote add azure YOUR_GIT_CLONE_URL_HERE
   
    **Obs**! Ersätt strängen "YOUR_GIT_CLONE_URL_HERE" med din egen URL för Git-klon som du kopierade tidigare. 
4. Kör följande kommandon för att skapa en incheckning som innehåller hela din kod. 
   
        git add .
        git commit -m "initial revision"
   
    ![Utdata för Git-incheckning](media/app-service-api-nodejs-api-app/git-commit-output.png)
5. Kör kommandot för att skicka koden till Azure. När du uppmanas att ange ett lösenord anger du det som du skapade tidigare i Azure-portalen.
   
        git push azure master
   
    Detta utlöser en distribution till API-appen.  
6. I webbläsaren går du tillbaka till bladet **Distributioner** för API-appen så ser du att distributionen görs. 
   
    ![Distribution sker](media/app-service-api-nodejs-api-app/deployment-happening.png)
   
    Samtidigt återspeglar kommandoradsgränssnittet statusen för distributionen medan den sker. 
   
    ![Node Js-distribution sker](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)
   
    När distributionen är klar är återspeglar bladet **Distributioner** att kodändringarna i API-appen hat distribuerats. 

## <a name="test-with-the-api-running-in-azure"></a>Testa med det API som körs i Azure
1. Kopiera den **URL** som finns i avsnittet **Essentials** i bladet för din API-app. 
   
    ![Distributionen har slutförts](media/app-service-api-nodejs-api-app/deployment-completed.png)
2. Använd en REST API-klient, till exempel Postman eller Fiddler (eller webbläsaren), och ange URL:en till API-anropet för dina kontakter, vilket är `/contacts`-slutpunkten för API-appen. URL:en blir `https://{your API app name}.azurewebsites.net/contacts`
   
    När du skickar en GET-begäran till den här slutpunkten får du JSON-utdata för API-appen.
   
    ![Api för Postman](media/app-service-api-nodejs-api-app/postman-hitting-api.png)
3. I en webbläsare går du till `/docs`-slutpunkten för att testa användargränssnittet för Swagger UI medan det körs i Azure.

Nu när du har kontinuerlig leverans kan du göra kodändringar och distribuera dem till Azure genom att bara skicka incheckningar till Azure Git-lagringsplatsen.

## <a name="next-steps"></a>Nästa steg
Vid det här laget har du skapat en API-app och distribuerat Node.js API-kod till den. Nästa självstudiekurs visar hur du [använder API Apps från JavaScript-klienter med hjälp av CORS](app-service-api-cors-consume-javascript.md).




<!--HONumber=Nov16_HO2-->


