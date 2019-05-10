---
title: Skapa din första PowerShell-funktion med Azure Functions
description: Lär dig hur du skapar din första PowerShell-funktion i Azure med hjälp av Visual Studio Code.
services: functions
keywords: ''
author: joeyaiello
manager: jeconnoc
ms.author: jaiello, glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.devlang: powershell
ms.openlocfilehash: 24640c9013f7a5b81cd5b1b6f45de49c5baad0e6
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473276"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Skapa din första PowerShell-funktion i Azure (förhandsversion)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Den här snabbstartsartikeln vägleder dig igenom hur du skapar din första [serverlös](https://azure.com/serverless) PowerShell-funktion med Visual Studio Code.

![Azure Functions-kod i ett projekt i Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Du använder den [Azure Functions-tillägg för Visual Studio Code] att skapa en PowerShell-funktion lokalt och distribuerat den till en ny funktionsapp i Azure. Tillägget finns för närvarande i en förhandsversion. Mer information finns på sidan om [Azure Functions-tillägg för Visual Studio Code].

> [!NOTE]  
> PowerShell-stöd för den [Azure Functions-tillägget][azure functions-tillägg för visual studio code] är inaktiverad som standard. Aktivera stöd för PowerShell är ett av stegen i den här artikeln.

Följande steg kan användas på macOS, Windows och Linux-baserade operativsystem.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

* Installera [PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)

* Installera [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Installera [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Installera [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (krävs av Azure Functions Core Tools och tillgänglig på alla plattformar som stöds).

* Installera version 2.x av den [Azure Functions Core Tools](functions-run-local.md#v2).

* Du behöver också en aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. 

1. I Visual Studio Code, väljer du den Azure logotypen som ska visas i **Azure: Functions** område och välj sedan ikonen Skapa nytt projekt.

    ![Skapa ett funktionsapprojekt](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Välj en plats för din Functions-Projektarbetsyta och välj **Välj**.

    > [!NOTE]
    > Den här artikeln har utformats för att genomföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Välj den **Powershell (förhandsversion)** som språk för ditt funktionsappsprojekt och sedan **Azure Functions v2**.

1. Välj **HTTP-utlösare** som mall för din första funktion använder `HTTPTrigger` som funktionen namnet och välj en nivå för auktorisering av **funktionen**.

    > [!NOTE]
    > Den **funktionen** säkerhetsnivån kräver en [funktionsnyckel](functions-bindings-http-webhook.md#authorization-keys) värdet när du anropar funktionen slutpunkten i Azure. Detta gör det svårare för vem som helst att anropa din funktion.

1. Välj **Lägg till på arbetsyta** när du uppmanas att göra det.

Visual Studio Code skapar approjektet för PowerShell-funktionen i en ny arbetsyta. Det här projektet innehåller den [host.json](functions-host-json.md) och [local.settings.json](functions-run-local.md#local-settings-file) konfigurationsfiler som gäller för alla funktion i projektet. Detta [PowerShell projekt](functions-reference-powershell.md#folder-structure) är samma som en funktionsapp som körs i Azure.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Azure Functions Core Tools kan integreras med Visual Studio Code så att du kan köra och felsöka ett Azure Functions-projekt lokalt.  

1. Om du vill felsöka funktionen Infoga ett anrop till den [ `Wait-Debugger` ] cmdlet i Funktionskoden innan du vill koppla felsökaren, tryck på F5 för att starta funktionsappsprojekt och koppla felsökaren. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Lägg till frågesträngen `?name=<yourname>` i den här URL: en och använder sedan `Invoke-RestMethod` att utföra begäran, enligt följande:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Du kan också köra GET-begäran från en webbläsare.

    När du anropar slutpunkten HttpTrigger utan att skicka en `name` parameter som frågeparameter eller i meddelandetexten, returnerar funktionen ett 500-fel. När du granskar koden i run.ps1 kan se du att det här felet är avsiktligt.

1. Tryck på SKIFT+F5 för att stoppa felsökningen.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

> [!NOTE]
> Kom ihåg att ta bort alla anrop till `Wait-Debugger` innan du publicerar dina funktioner till Azure. 

> [!NOTE]
> Skapa en Funktionsapp i Azure frågar endast efter namnet på funktionen. Ange azureFunctions.advancedCreation till true för att bli ombedd att ange alla andra värden.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Kör funktionen i Azure

Kontrollera att din publicerade funktion som körs i Azure genom att köra följande PowerShell-kommando ersätter den `Uri` parameter med URL: en för funktionen HTTPTrigger från föregående steg. Lägg till frågesträngen som tidigare `&name=<yourname>` till URL: en, som i följande exempel:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa en PowerShell-funktionsapp med en enkel HTTP-utlöst funktion. Du kanske också vill Läs mer om [felsökning en PowerShell-funktion lokalt](functions-debug-powershell-local.md) med hjälp av Azure Functions Core Tools. Kolla in den [utvecklarguide för Azure Functions PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
