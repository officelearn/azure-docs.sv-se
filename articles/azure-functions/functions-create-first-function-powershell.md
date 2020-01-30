---
title: Skapa din första PowerShell-funktion i Azure
description: Lär dig hur du skapar din första PowerShell-funktion i Azure med Visual Studio Code.
author: joeyaiello
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.openlocfilehash: b1a1caf985e9693e261684c1edb21184071ebfc8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845916"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Skapa din första PowerShell-funktion i Azure

I den här snabb starten beskrivs hur du skapar din första [Server](https://azure.com/serverless) utan PowerShell-funktion med Visual Studio Code.

![Azure Functions kod i ett Visual Studio Code-projekt](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Du kan använda [Azure Functions-tillägg för Visual Studio Code] för att skapa en PowerShell-funktion lokalt och sedan distribuera den till en ny function-app i Azure. Tillägget finns för närvarande i en förhandsversion. Mer information finns på sidan om [Azure Functions-tillägg för Visual Studio Code].

Följande steg stöds i macOS-, Windows-och Linux-baserade operativ system.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* Installera [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Installera [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Installera [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Installera [.net Core SDK 2.2 +](https://www.microsoft.com/net/download) (krävs av Azure Functions Core tools och är tillgängligt på alla plattformar som stöds).

* Installera version 2. x av [Azure Functions Core tools](functions-run-local.md#v2).

* Du behöver också en aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Välj Azure-logotypen i Visual Studio Code så att området **Azure: Funktioner** visas och välj sedan ikonen Skapa nytt projekt.

    ![Skapa ett funktionsapprojekt](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Välj en plats för dina funktioner projekt arbets yta och välj **Välj**.

    > [!NOTE]
    > Den här artikeln har utformats för att genomföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Välj **PowerShell** som språk för ditt Function app-projekt och sedan **Azure Functions v2**.

1. Välj **http-utlösare** som mall för din första funktion, Använd `HTTPTrigger` som funktions namn och välj en autentiseringsnivå för **funktionen**.

    > [!NOTE]
    > För **funktions** nivån för autentisering krävs ett [funktions nyckel](functions-bindings-http-webhook.md#authorization-keys) värde vid anrop av funktions slut punkten i Azure. Detta gör det svårare för bara vem som helst att anropa din funktion.

1. Välj **Lägg till på arbetsyta** när du uppmanas att göra det.

Visual Studio Code skapar PowerShell Function-exempelprojektet i en ny arbets yta. Det här projektet innehåller konfigurationsfilerna [Host. JSON](functions-host-json.md) och [Local. Settings. JSON](functions-run-local.md#local-settings-file) , som gäller för alla funktioner i projektet. Det här [PowerShell-projektet](functions-reference-powershell.md#folder-structure) är detsamma som en Function-app som körs i Azure.

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Kör funktionen i Azure

Verifiera att den publicerade funktionen körs i Azure genom att köra följande PowerShell-kommando och ersätta `Uri`-parametern med URL: en för funktionen HTTPTrigger från föregående steg. Som tidigare lägger du till frågesträngen `&name=<yourname>` till URL: en, som i följande exempel:

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

Du har använt Visual Studio Code för att skapa en PowerShell Function-app med en enkel HTTP-utlöst funktion. Du kanske också vill lära dig mer om hur du [felsöker en PowerShell-funktion lokalt](functions-debug-powershell-local.md) med hjälp av Azure Functions Core tools. Kolla in [Azure Functions PowerShell Developer Guide](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
