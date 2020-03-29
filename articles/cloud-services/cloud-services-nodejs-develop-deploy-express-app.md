---
title: Skapa och distribuera en Node.js Express-app till Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Skapa och distribuera ett Express.js-program i Node.js till Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360760"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Skapa och distribuera ett Node.js-webbprogram med Express på en Azure Cloud Services

Nod.js innehåller en minimal uppsättning funktioner i kärnkörningen.
Utvecklare använder ofta moduler från tredje part för att tillhandahålla ytterligare funktioner när de utvecklar ett Node.js-program. I den här självstudien skapar [Express](https://github.com/expressjs/express) du ett nytt program med expressmodulen, som tillhandahåller ett MVC-ramverk för att skapa node.js webbprogram.

En skärmdump av den färdiga ansökan är nedan:

![En webbläsare som visar Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Skapa ett molntjänstprojekt
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Utför följande steg för att skapa ett nytt molntjänstprojekt med namnet "expressapp":

1. Sök efter **Windows PowerShell**på **Start-menyn** eller **Startskärmen**. Högerklicka slutligen på **Windows PowerShell** och välj **Kör som administratör**.
   
    ![Ikon för Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Ändra kataloger till **katalogen\\c: nod** och ange sedan följande kommandon för att skapa en ny lösning med namnet **expressapp** och en webbroll med namnet **WebRole1:**
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Som standard använder **Add-AzureNodeWebRole** en äldre version av Node.js. **Set-AzureServiceProjectRole-satsen** ovan instruerar Azure att använda v0.10.21 av noden.  Observera att parametrarna är skiftlägeskänsliga.  Du kan kontrollera att rätt version av Node.js har valts genom att kontrollera **egenskapen engines** i **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Installera Express
1. Installera Express-generatorn genom att utfärda följande kommando:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Utdata från npm-kommandot bör se ut ungefär som resultatet nedan. 
   
    ![Windows PowerShell som visar utdata från kommandot npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Ändra kataloger till **WebRole1-katalogen** och använd kommandot Express för att generera ett nytt program:
   
        PS C:\node\expressapp\WebRole1> express
   
    Du uppmanas att skriva över ditt tidigare program. Ange **y** eller **ja** för att fortsätta. Express genererar filen app.js och en mappstruktur för att skapa ditt program.
   
    ![Utdata från expresskommandot](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Om du vill installera ytterligare beroenden som definierats i filen package.json anger du följande kommando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Utdata från kommandot npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Använd följande kommando för att kopiera **lagerplats/www-filen** till **server.js**. Detta är så att molntjänsten kan hitta startpunkten för det här programmet.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   När det här kommandot är klart bör du ha en **server.js-fil** i WebRole1-katalogen.
5. Ändra **server.js** för att ta bort ett av '.'-tecknen från följande rad.
   
       var app = require('../app');
   
   Efter att ha gjort denna ändring bör raden visas på följande sätt.
   
       var app = require('./app');
   
   Den här ändringen krävs eftersom vi flyttade filen (tidigare **bin/www**)) till samma katalog som appfilen som krävs. När du har gjort den här ändringen sparar du **filen server.js.**
6. Använd följande kommando för att köra programmet i Azure-emulatorn:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![En webbsida som innehåller välkommen att uttrycka.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Ändra vyn
Nu ändra vyn för att visa meddelandet "Välkommen till Express i Azure".

1. Ange följande kommando för att öppna filen index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Innehållet i filen index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade är standardvymotorn som används av Express-program. Mer information om Jade-vymotorn finns i [http://jade-lang.com][http://jade-lang.com].
2. Ändra den sista textraden genom att lägga till **i Azure**.
   
   ![Filen index.jade, lyder den sista raden: p Välkommen till \#{title} i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Spara filen och avsluta Anteckningar.
4. Uppdatera din webbläsare och du kommer att se dina ändringar.
   
   ![Ett webbläsarfönster, sidan innehåller Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

När du har testat programmet använder du **cmdleten Stop-AzureEmulator** för att stoppa emulatorn.

## <a name="publishing-the-application-to-azure"></a>Publicera programmet till Azure
I Azure PowerShell-fönstret använder du cmdleten **Publish-AzureServiceProject** för att distribuera programmet till en molntjänst

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

När distributionen är klar öppnas webbläsaren och visar webbsidan.

![En webbläsare som visar Express-sidan. URL:en anger att den nu finns på Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





