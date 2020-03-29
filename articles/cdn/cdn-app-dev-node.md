---
title: Komma igång med Azure CDN SDK för nod.js | Microsoft-dokument
description: Lär dig hur du skriver Node.js-program för att hantera Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594131"
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Du kan använda [Azure CDN SDK för Node.js för](https://www.npmjs.com/package/azure-arm-cdn) att automatisera skapandet och hanteringen av CDN-profiler och slutpunkter.  Den här självstudien går igenom skapandet av ett enkelt Node.js-konsolprogram som demonstrerar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDN SDK för Node.js i detalj.

För att slutföra den här självstudien bör du redan ha [Node.js](https://www.nodejs.org) **4.x.x** eller högre installerat och konfigurerat.  Du kan använda vilken textredigerare du vill skapa node.js-programmet.  För att skriva den här guiden använde jag [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> Det [slutförda projektet från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) är tillgängligt för nedladdning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Skapa ditt projekt och lägg till NPM-beroenden
Nu när vi har skapat en resursgrupp för våra CDN-profiler och gett vår Azure AD-programbehörighet för att hantera CDN-profiler och slutpunkter i den gruppen, kan vi börja skapa vårt program.

Skapa en mapp för att lagra programmet.  Från en konsol med nod.js-verktygen i den aktuella sökvägen ställer du in din aktuella plats på den nya mappen och initierar projektet genom att köra:

    npm init

Du kommer då att presenteras en rad frågor för att initiera ditt projekt.  För **startpunkt**använder den här självstudien *app.js*.  Du kan se mina andra val i följande exempel.

![NPM-init-utgång](./media/cdn-app-dev-node/cdn-npm-init.png)

Vårt projekt är nu initierat med en *packages.json* fil.  Vårt projekt kommer att använda vissa Azure-bibliotek som finns i NPM-paket.  Vi använder Azure Client Runtime for Node.js (ms-rest-azure) och Azure CDN Client Library for Node.js (azure-arm-cd).  Låt oss lägga till dem i projektet som beroenden.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

När paketen har installerats bör *filen package.json* se ut ungefär som det här exemplet (versionsnummer kan skilja sig åt):

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

Slutligen, med hjälp av din textredigerare, skapa en tom textfil och spara den i roten i vår projektmapp som *app.js*.  Vi är nu redo att börja skriva kod.

## <a name="requires-constants-authentication-and-structure"></a>Kräver, konstanter, autentisering och struktur
Med *app.js* öppna i vår redaktör, låt oss få den grundläggande strukturen i vårt program skrivet.

1. Lägg till "kräver" för våra NPM-paket högst upp med följande:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Vi måste definiera några konstanter våra metoder kommer att använda.  Lägg till följande.  Var noga med att ersätta platshållarna, inklusive ** &lt;vinkelparenteserna,&gt;** med dina egna värden efter behov.
   
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
3. Därefter instansierar vi CDN-hanteringsklienten och ger den våra referenser.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Om du använder individuell användarautentisering ser dessa två rader ut något annorlunda ut.
   
   > [!IMPORTANT]
   > Använd bara det här kodexemplet om du väljer att ha individuell användarautentisering i stället för ett huvudnamn för tjänsten.  Var noga med att skydda dina individuella användaruppgifter och hålla dem hemliga.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Var noga med att ersätta objekten i ** &lt;vinkelparenteser&gt; ** med rätt information.  För `<redirect URI>`använder du den omdirigera URI som du angav när du registrerade programmet i Azure AD.
4. Vår Node.js konsol ansökan kommer att ta några kommandoradsparametrar.  Låt oss bekräfta att minst en parameter har skickats.
   
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
5. Det leder oss till huvuddelen av vårt program, där vi förgrena oss till andra funktioner baserat på vilka parametrar som passerades.
   
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
6. På flera ställen i vårt program måste vi se till att rätt antal parametrar skickades in och visa lite hjälp om de inte ser korrekta ut.  Låt oss skapa funktioner för att göra det.
   
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
7. Slutligen är de funktioner som vi kommer att använda på CDN-hanteringsklienten asynkrona, så de behöver en metod för att ringa tillbaka när de är klara.  Låt oss göra en som kan visa utdata från CDN-hanteringsklienten (om någon) och avsluta programmet graciöst.
   
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

Nu när den grundläggande strukturen i vårt program är skriven, bör vi skapa de funktioner som kallas baserat på våra parametrar.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slutpunkter
Låt oss börja med kod för att lista våra befintliga profiler och slutpunkter.  Min kod kommentarer ger den förväntade syntaxen så att vi vet var varje parameter går.

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
Därefter skriver vi funktionerna för att skapa profiler och slutpunkter.

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
Förutsatt att slutpunkten har skapats, en gemensam uppgift som vi kanske vill utföra i vårt program är att rensa innehåll i vår slutpunkt.

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
Den sista funktionen kommer vi att inkludera tar bort slutpunkter och profiler.

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

## <a name="running-the-program"></a>Köra programmet
Vi kan nu köra vårt Node.js-program med hjälp av vår favoritfelsökare eller på konsolen.

> [!TIP]
> Om du använder Visual Studio-kod som felsökning måste du konfigurera din miljö så att den skickas i kommandoradsparametrarna.  Visual Studio Code gör detta i **launch.json-filen.**  Leta efter en egenskap med namnet **args** och lägg till en matris med `"args": ["list", "profiles"]`strängvärden för dina parametrar, så att den ser ut ungefär så här: .
> 
> 

Låt oss börja med att lista våra profiler.

![Lista profiler](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vi har en tom samling tillbaka.  Eftersom vi inte har några profiler i vår resursgrupp, det förväntas.  Nu ska vi skapa en profil.

![Skapa profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Nu ska vi lägga till en slutpunkt.

![Skapa slutpunkt](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Låt oss slutligen ta bort vår profil.

![Ta bort profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Efterföljande moment
Om du vill se det slutförda projektet från den här genomgången [hämtar du exemplet](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Om du vill visa referensen för Azure CDN SDK för Node.js läser du [referensen](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Om du vill hitta ytterligare dokumentation om Azure SDK för nod.js visar du den [fullständiga referensen](https://azure.github.io/azure-sdk-for-node/).

Hantera CDN-resurser med [PowerShell](cdn-manage-powershell.md).

