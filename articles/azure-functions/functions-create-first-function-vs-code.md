---
title: Skapa din första funktion i Azure med Visual Studio Code
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions-tillägget i Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: cbe4dbd2ae741f4225cfdc628c31508956cbb95c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59490541"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Skapa din första funktion med Visual Studio Code

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

I den här artikeln får du lära dig hur du använder [Azure Functions-tillägg för Visual Studio Code] för att skapa och testa en ”Hello World”-funktion på din lokala dator med Microsoft Visual Studio Code. Du publicerar sedan funktionskoden till Azure från Visual Studio Code.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

För närvarande har tillägget fullständigt stöd för C#-, JavaScript- och Java-funktioner. Python stöds för närvarande som en förhandsversion. Stegen i den här artikeln kan variera beroende på ditt val av språk för Azure Functions-projektet. Tillägget finns för närvarande i en förhandsversion. Mer information finns på sidan om [Azure Functions-tillägg för Visual Studio Code].

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms). Den här artikeln har utvecklats och testats på en enhet med macOS (High Sierra).

* Installera version 2.x av [Azure Functions Core Tools](functions-run-local.md#v2), som fortfarande finns i förhandsversion.

* Installera de specifika kraven för ditt valda språk:

    | Språk | Anknytning |
    | -------- | --------- |
    | **C#** | [C# för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET Core CLI-verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [Felsökningsprogram för Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node 8.0+](https://nodejs.org/)  |

    \* Krävs också av Core Tools.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>Skapa en HTTP-utlöst funktion

1. I **Azure: Functions** väljer du ikonen Skapa funktion.

    ![Skapa en funktion](./media/functions-create-first-function-vs-code/create-function.png)

1. Välj mappen med funktionsappsprojektet och välj funktionsmallen **HTTP-utlösare**.

    ![Välj mallen HTTP-utlösare](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. Skriv `HTTPTrigger` som funktionens namn och tryck på Retur. Välj sedan **Anonym** autentisering.

    ![Välj anonym autentisering](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    En funktion skapas på ditt valda språk med hjälp av mallen för en HTTP-utlöst funktion.

    ![Mall för HTTP-utlöst funktion i Visual Studio Code](./media/functions-create-first-function-vs-code/new-function-full.png)

Du kan lägga till bindningar för indata och utdata i funktionen genom att ändra function.json-filen. Mer information finns i [Utlösare och bindningar i Azure Functions](functions-triggers-bindings.md).

Nu när du har skapat ditt funktionsprojekt och en HTTP-utlöst funktion kan du testa den på en lokal dator.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.  

1. Ange en brytpunkt i funktionskoden och tryck på F5 för att starta funktionsappsprojektet om du vill testa funktionen. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Körningen pausas när brytpunkten nås.

    ![Funktion som når brytpunkt i Visual Studio Code](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. När du fortsätter körningen visas svaret på GET-begäran i webbläsaren nedan:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. Tryck på SKIFT+F5 för att stoppa felsökningen.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Lägg till frågesträngen `?name=<yourname>` i slutet av URL:en som tidigare och kör begäran.

    Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Funktionssvar i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. Du kanske också vill Läs mer om [lokal testning och felsökning från Terminal eller kommandotolk](functions-run-local.md) med hjälp av Azure Functions Core Tools.

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
