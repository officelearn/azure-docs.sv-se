---
title: Skapa din första funktion i Azure med Visual Studio Code
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions-tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc, devcenter
ms.openlocfilehash: b89e6d75cd82a65dfbce29b949c4b7d463582bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230787"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Skapa din första funktion med Visual Studio Code

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

I den här artikeln får du lära dig hur du använder [Azure Functions-tillägg för Visual Studio Code] för att skapa och testa en ”Hello World”-funktion på din lokala dator med Microsoft Visual Studio Code. Du publicerar sedan funktionskoden till Azure från Visual Studio Code.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

The extension currently supports C#, JavaScript, Java, and Python functions. The steps in this article and the article that follows support only JavaScript and C# functions. To learn how to use Visual Studio Code to create and publish Python functions, see [Create and deploy serverless Azure Functions in Python with Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). To learn how to use Visual Studio Code to create and publish PowerShell functions, see [Create your first PowerShell function in Azure](functions-create-first-function-powershell.md). 

Tillägget finns för närvarande i en förhandsversion. Mer information finns på sidan om [Azure Functions-tillägg för Visual Studio Code].

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Install version 2.x of the [Azure Functions Core Tools](functions-run-local.md#v2).

* Installera de specifika kraven för ditt valda språk:

    | Språk | Krav |
    | -------- | --------- |
    | **C#** | [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS and Maintenance LTS versions (8.11.1 and 10.14.1 recommended).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Run the function in Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. This URL includes the function key, which is passed to the `code` query parameter. Lägg till frågesträngen `?name=<yourname>` i slutet av URL:en som tidigare och kör begäran.

    Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Funktionssvar i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. In the next article, you expand that function by adding an output binding. This binding writes the string from the HTTP request to a message in an Azure Queue Storage queue. The next article also shows you how to clean up these new Azure resources by removing the resource group you created.

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
