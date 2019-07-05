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
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fcf9f1d6420dbbde359d386bc3b67a0866aca30d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444598"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Skapa din första funktion med Visual Studio Code

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

I den här artikeln får du lära dig hur du använder [Azure Functions-tillägg för Visual Studio Code] för att skapa och testa en ”Hello World”-funktion på din lokala dator med Microsoft Visual Studio Code. Du publicerar sedan funktionskoden till Azure från Visual Studio Code.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Tillägget stöder för närvarande C#, JavaScript och Java fungerar med Python-stöd för närvarande i förhandsversion. Stegen i den här artikeln och artikeln som följer stöder endast JavaScript och C# funktioner. Läs hur du använder Visual Studio Code för att skapa och publicera Python-funktioner i [distribuera Python till Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Läs hur du använder Visual Studio Code för att skapa och publicera PowerShell-funktioner i [skapa din första PowerShell-funktion i Azure](functions-create-first-function-powershell.md). 

Tillägget finns för närvarande i en förhandsversion. Mer information finns på sidan om [Azure Functions-tillägg för Visual Studio Code].

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Installera version 2.x av den [Azure Functions Core Tools](functions-run-local.md#v2).

* Installera de specifika kraven för ditt valda språk:

    | Språk | Krav |
    | -------- | --------- |
    | **C#** | [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Aktiva LTS och underhåll LTS versioner (8.11.1 och 10.14.1 rekommenderas).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Lägg till frågesträngen `?name=<yourname>` i slutet av URL:en som tidigare och kör begäran.

    Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Funktionssvar i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du funktionen genom att lägga till en utdatabindning. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. Nästa artikel visar också hur du rensar dessa nya Azure-resurser genom att ta bort resursgruppen som du skapade.

> [!div class="nextstepaction"]
> [Lägg till en Azure Storage-kö-bindning till din funktion](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
