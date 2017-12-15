---
title: "Node.js API-app i Azure Apptjänst | Microsoft Docs"
description: "Lär dig hur du skapar en Node.js RESTful-API och distribuera det till en API-app i Azure Apptjänst."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc, devcenter
ms.openlocfilehash: 2170ac7df3b894c8d19b432abdcfef5c7fd75ff4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Skapa en Node.js RESTful-API och distribuera den till en API-app i Azure

I den här snabbstarten visas hur du skapar ett [Express](http://expressjs.com/)-ramverk med REST API för Node.js från en [Swagger](http://swagger.io/)-definition och distribuerar det på Azure. Du skapar appen med hjälp av kommandoradsverktyg, konfigurerar resurser med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) och distribuerar appen med Git.  När du är klar har du ett fungerande exempel-REST-API som körs på Azure.

## <a name="prerequisites"></a>Krav

* [Git](https://git-scm.com/)
* [Node.js och NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Kör följande kommando i ett terminalfönster för att klona exemplet till den lokala datorn.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Ändra till den katalog som innehåller exempelkoden.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Installera [Swaggerize](https://www.npmjs.com/package/swaggerize-express) på din lokala dator. Swaggerize är ett verktyg som genererar Node.js-kod för REST-API från en Swagger-definition.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Generera Node.js-kod 

Det här avsnittet av kursen visar ett arbetsflöde för API-utveckling där du först skapar Swagger-metadata och använder dem för att autogenerera en serverkod för API:et. 

Gå till katalogen för mappen *Start* och kör `yo swaggerize`. Swaggerize skapar ett Node.js-projekt för ditt API från Swagger-definitionen i *api.json*.

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Använd *ContactList* när du uppmanas ange projektnamn.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>Anpassa projektkoden

1. Kopiera mappen *lib* till mappen *Kontaktlista* som skapats av `yo swaggerize` och ändra sedan katalogen till *Kontaktlista*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Installera NPM-modulerna `jsonpath` och `swaggerize-ui`. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Ersätt koden i *handlers/contacts.js* med följande kod: 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Den här koden använder JSON-data som lagras i *lib/contacts.json* som hanteras av *lib/contactRepository.js*. Den nya *contacts.js*-koden returnerar alla kontakter i lagringsplatsen som en JSON-nyttolast. 

4. Ersätt koden i filen **handlers/contacts/{id}.js** med följande kod:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Med den här koden kan du använda en sökvägsvariabel för att endast returnera kontakten med det angivna ID:t.

5. Ersätt koden i **server.js** med följande kod:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Den här koden gör några små ändringar så att den fungerar med Azure App Service och gör ett interaktivt webbgränssnitt tillgängligt för ditt API.

### <a name="test-the-api-locally"></a>Testa API lokalt

1. Starta Node.js-appen
    ```bash
    npm start
    ```
    
2. Bläddra till http://localhost:8000/contacts för att se JSON för hela kontaktlistan.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Bläddra till http://localhost:8000/contacts/2 för att se kontakten med en `id` för två.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Testa API:et med Swagger-webbgränssnittet på http://localhost:8000/docs.
   
    ![Swagger-webbgränssnitt](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> Skapa en API-app

I det här avsnittet använder du Azure CLI 2.0 för att skapa de resurser som behövs när Azure App Service ska vara värd för ditt API. 

1.  Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/?view=azure-cli-latest#az_login) och följ anvisningarna på skärmen.

    ```azurecli-interactive
    az login
    ```

2. Om du har flera Azure-prenumerationer kan du ändra standardprenumerationen till den du vill använda.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Distribuera API med Git

Distribuera din kod till API-appen genom att skicka incheckningar från din lokala Git-lagringsplats till Azure App Service.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Initiera en ny lagringsplats i katalogen *Kontaktlista*. 

    ```bash
    git init .
    ```

3. Exkludera katalogen *node_modules* som skapats av npm tidigare i självstudien från Git. Skapa en ny `.gitignore`-fil i den aktuella katalogen och lägg till följande text på en ny rad var som helst i filen.

    ```
    node_modules/
    ```
    Bekräfta att mappen `node_modules` ignoreras med `git status`.

4. Checka in ändringarna till lagringsplatsen.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Testa API:et i Azure

1. Öppna en webbläsare och gå till http://app_name.azurewebsites.net/contacts. Samma JSON returneras som när du gjorde begäran lokalt tidigare i självstudien.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Gå till `http://app_name.azurewebsites.net/docs`-slutpunkten i en webbläsare för att testa Swagger-användargränssnittet som körs i Azure.

    ![Swagger Ii](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    Du kan nu distribuera uppdateringar till exempel-API:et till Azure genom att helt enkelt push-överföra incheckningar till Azure Git-lagringsplatsen.

## <a name="clean-up"></a>Rensa

Kör följande Azure CLI-kommando för att rensa resurserna som skapades av den här snabbstarten:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>Nästa steg 
> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)

