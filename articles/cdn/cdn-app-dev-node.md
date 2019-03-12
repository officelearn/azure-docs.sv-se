---
title: Kom igång med Azure CDN SDK för Node.js | Microsoft Docs
description: Lär dig hur du skriver Node.js-program för att hantera Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 48a84520a61d19968b467091871459e21898dd5e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548651"
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [NET](cdn-app-dev-net.md)
> 
> 

Du kan använda den [Azure CDN SDK för Node.js](https://www.npmjs.com/package/azure-arm-cdn) att automatisera skapandet och hanteringen av CDN-profiler och slutpunkter.  Den här självstudiekursen beskriver skapandet av en enkel Node.js-konsolprogram som visar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDN SDK för Node.js i detalj.

Den här kursen bör du redan ha [Node.js](https://www.nodejs.org) **4.x.x** eller senare installerat och konfigurerat.  Du kan använda valfri textredigerare som du vill skapa Node.js-programmet.  Om du vill skriva den här självstudien, som jag använde [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> Den [slutförda projekt från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) är tillgänglig för hämtning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Skapa projektet och Lägg till NPM-beroenden
Nu när vi har skapat en resursgrupp för vår CDN-profiler och våra Azure AD-programmet behörighet att hantera CDN-profiler och slutpunkter inom gruppen, kan vi börja skapa vårt program.

Skapa en mapp för att lagra ditt program.  Ange din aktuella plats till denna från en konsol med Node.js tools i din aktuella sökväg och starta projektet genom att köra:

    npm init

Sedan visas ett antal frågor att initiera ditt projekt.  För **startpunkt**, den här självstudien används *app.js*.  Du kan se min andra alternativen i följande exempel.

![NPM init utdata](./media/cdn-app-dev-node/cdn-npm-init.png)

Våra projekt nu har initierats med en *packages.json* fil.  Våra projekt kommer att använda vissa Azure-bibliotek i NPM-paket.  Vi använder Runtime för Azure-klient för Node.js (ms-rest-azure) och Azure CDN-klientbiblioteket för Node.js-azure-arm-CD: n.  Vi lägger till dem i projektet som beroenden.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

När paketen är klar installerar, den *package.json* filen bör likna det här exemplet (version siffror kan skilja sig):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Slutligen Använd din textredigerare och skapa en tom textfil och spara den i roten av våra projektmapp, till exempel som *app.js*.  Vi är nu redo att börja skriva kod.

## <a name="requires-constants-authentication-and-structure"></a>Kräver konstanter, autentisering och struktur
Med *app.js* öppna i vår redigeraren, vi grundstrukturen för vårt program som skrivits.

1. Lägg till den ”måste” för vår NPM-paket överst med följande:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Vi måste definiera vissa konstanter våra metoder används.  Lägg till följande.  Se till att ersätta platshållarna, inklusive den  **&lt;vinkelparenteser&gt;**, med dina egna värden efter behov.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Sedan vi skapa en instans av CDN-Hanteringsklient och ge den vår autentiseringsuppgifter.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Om du använder autentisering av enskilda användare, kommer de här två raderna se lite annorlunda.
   
   > [!IMPORTANT]
   > Använd bara det här kodexemplet om du väljer att använda enskilda användarautentisering i stället för ett huvudnamn för tjänsten.  Var noga med att skydda dina användarautentiseringsuppgifter för enskilda och håll dem hemliga.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se till att ersätta objekt i **&lt;vinkelparenteser&gt;** med rätt information.  För `<redirect URI>`, använda omdirigerings-URI som du angav när du registrerade programmet i Azure AD.
4. Vår Node.js-konsolprogram kommer att vissa kommandoradsparametrar.  Vi Kontrollera att minst en parameter skickades.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Som leder fram till den viktigaste delen av vårt program där vi förgrening för andra funktioner baserat på vilka parametrar skickades.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. På flera platser i vårt program måste du kontrollera att rätt antal parametrar skickades in och visa hjälp om de inte verkar vara korrekt.  Nu ska vi skapa funktioner för att göra detta.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Slutligen är de funktioner som vi ska använda på CDN-Hanteringsklient asynkron, så de behöver en metod för att anropa tillbaka när de är klar.  Vi gör så att du kan visa utdata från CDN-Hanteringsklient (om sådan finns) och avsluta programmet på ett smidigt sätt.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nu när grundstrukturen för vårt program skrivs ska vi skapa de funktioner som anropas baserat på vår parametrar.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slutpunkter
Låt oss börja med kod för att visa en lista över våra befintliga profiler och slutpunkter.  Kodkommentarer ger förväntade syntaxen så att vi vet där varje parameter går.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slutpunkter
Nu ska skriver vi funktioner för att skapa profiler och slutpunkter.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Rensa en slutpunkt
Anta att slutpunkten har skapats och en gemensam uppgift som vi kan utföra i vårt program Rensa innehåll i vår slutpunkt.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slutpunkter
Senaste funktionen. Vi inkluderar tar bort slutpunkterna och profiler.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Kör programmet
Vi kan nu köra vår Node.js-program med vår favorit felsökare eller på konsolen.

> [!TIP]
> Om du använder Visual Studio Code som din debugger, behöver du konfigurera din miljö för att skicka in kommandoradsparametrar.  Visual Studio Code sker på den **launch.json** fil.  Leta efter en egenskap med namnet **argument** och lägga till en matris med strängvärden för parametrarna, så att det ser ut ungefär så här: `"args": ["list", "profiles"]`.
> 
> 

Låt oss börja med att visa en lista över våra profiler.

![Visa lista med](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vi fick tillbaka en tom matris.  Eftersom vi inte har några profiler i resursgrupp, som förväntas.  Nu ska vi skapa en profil nu.

![Skapa profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Nu ska vi lägga till en slutpunkt.

![Skapa slutpunkt](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Slutligen ska vi ta bort vår profilen.

![Ta bort profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Nästa steg
Se de slutförda projektet från den här genomgången [hämta exemplet](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Om du vill visa referensen för Azure CDN SDK för Node.js, den [referens](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Du hittar ytterligare dokumentation om Azure SDK för Node.js genom att visa den [fullständig referens](https://azure.github.io/azure-sdk-for-node/).

Hantera dina CDN-resurser med [PowerShell](cdn-manage-powershell.md).

