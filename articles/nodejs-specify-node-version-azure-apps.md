---
title: Ange en Node.js-Version
description: "Lär dig att ange version för Node.js som används av Azure-webbplatser och molntjänster"
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Ange en Node.js-version i ett Azure-program
När värd för ett Node.js-program, kanske du vill kontrollera att ditt program använder en viss version av Node.js. Det finns flera sätt att göra detta för program som finns på Azure.

## <a name="default-versions"></a>Standardversioner
Node.js-versioner som tillhandahålls av Azure uppdateras kontinuerligt. Om inget annat anges standardversionen som anges i den `WEBSITE_NODE_DEFAULT_VERSION` miljövariabeln kommer att användas. Följ stegen i följande avsnitt i den här artikeln om du vill åsidosätta det här värdet

> [!NOTE]
> Om du är värd för ditt program i Azure Cloud Service (webb eller arbetare roll), och det är första gången du har distribuerat programmet, Azure kommer att försöka använda samma version av Node.js som du har installerat på din utvecklingsmiljö om det matchar ett t han standardversioner på Azure.
>
>

## <a name="versioning-with-packagejson"></a>Versionshantering med package.json
Du kan ange vilken version av Node.js som ska användas genom att lägga till följande för att din **package.json** fil:

    "engines":{"node":version}

Där *version* är att använda specifika versionsnumret. Du kan ange mer komplexa villkor för versionen, exempelvis:

    "engines":{"node": "0.6.22 || 0.8.x"}

Eftersom 0.6.22 inte är en av versionerna som är tillgängliga i värdmiljön, används den senaste versionen av 0,8 serie som är tillgängliga kommer att i stället - 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Versionshantering webbplatser med App-inställningar
Om du är värd för programmet på en webbplats, kan du ange miljövariabeln **WEBSITE_NODE_DEFAULT_VERSION** till den önskade versionen.

## <a name="versioning-cloud-services-with-powershell"></a>Versionshantering molntjänster med PowerShell
Om du är värd för programmet i en molntjänst och distribuerar program med hjälp av Azure PowerShell, kan du åsidosätta Node.js-standardversion med hjälp av den **Set AzureServiceProjectRole** PowerShell-cmdlet. Exempel:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Observera att parametrarna i ovanstående är skiftlägeskänsliga.  Du kan kontrollera att rätt version av Node.js har markerats som kontrollerar den **motorer** egenskap i din roll **package.json**.

Du kan också använda den **Get-AzureServiceProjectRoleRuntime** att hämta en lista över Node.js-versioner som är tillgängliga för program som finns som en tjänst i molnet.  Kontrollera alltid versionen av Node.js projektet beroende är i den här listan.

## <a name="using-a-custom-version-with-azure-websites"></a>Använda en anpassad version med Azure Websites
Medan Azure innehåller flera standardversioner av Node.js, kanske du vill använda en version som inte har angetts som standard. Om du är värd för programmet som en Azure-webbplats, du kan göra detta med hjälp av den **iisnode.yml** fil. Följande steg igenom processen med att använda en anpassad version av Node.Js med en Azure-webbplats:

1. Skapa en ny katalog och skapa sedan en **server.js** fil i katalogen. Den **server.js** -filen ska innehålla följande:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Node.js-version som används när du bläddrar till webbplatsen visas.
2. Skapa en ny webbplats och notera namnet på platsen. Till exempel på följande sätt i [Azure kommandoradsverktyg] att skapa en ny Azure-webbplats med namnet **minwebbplats**, och sedan aktivera en Git-lagringsplats för webbplatsen.

        azure site create mywebsite --git
3. Skapa en ny katalog med namnet **bin** som underordnad till den katalog som innehåller den **server.js** fil.
4. Hämta versionen av **node.exe** (Windows-version) som du vill använda med ditt program. Till exempel på följande sätt **curl** att hämta versionen 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Spara den **node.exe** filen till den **bin** mapp som skapats tidigare.
5. Skapa en **iisnode.yml** fil i samma katalog som den **server.js** filen och Lägg sedan till följande innehåll till den **iisnode.yml** fil:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Standardsökvägen är den **node.exe** fil i projektet kommer att finnas när du har publicerat programmet till Azure-webbplatsen.
6. Publicera programmet. Till exempel sedan jag tidigare skapade en ny webbplats med parametern – git kommer följande kommandon Lägg till programmets filer till min lokala Git-lagringsplats och skickar dem sedan till lagringsplatsen webbplats:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Öppna webbplatsen i en webbläsare när programmet har publicerats. Du bör se ett meddelande om ”Hello från Azure körs nod-version: v0.8.1”.

## <a name="next-steps"></a>Nästa steg
Nu när du förstår hur ange version för Node.js som används av ditt program, Läs mer om hur du [fungerar med moduler], [skapa och distribuera en Node.js-webbplats](app-service/app-service-web-get-started-nodejs.md), och [hur du använder Azure Kommandoradsverktyg för Mac- och Linux].

Mer information finns i [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/).

[Använda Azures kommandoradsverktyg för Mac och Linux]:cli-install-nodejs.md
[Azure kommandoradsverktyg]:cli-install-nodejs.md
[Arbeta med moduler]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
