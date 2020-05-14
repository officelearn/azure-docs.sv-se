---
title: 'Snabb start: skapa din första funktion i Azure med Visual Studio'
description: I den här snabb starten får du lära dig hur du skapar och publicerar en HTTP-utlösare Azure-funktion med hjälp av Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: a4549bd2947332d7140f4f440a5344f417430554
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122756"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Snabb start: skapa din första funktion i Azure med Visual Studio

Med Azure Functions kan du köra din kod i en miljö utan server utan att först behöva skapa en virtuell dator eller publicera ett webb program.

I den här snabb starten får du lära dig hur du använder Visual Studio 2019 för att lokalt skapa och testa en "Hello World" HTTP trigger C#-Function-app, som du sedan publicerar till Azure. 

![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Den här snabb starten är utformad för Visual Studio 2019. 

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen måste du först installera [Visual Studio 2019](https://azure.microsoft.com/downloads/). Se till att du väljer arbets belastningen **Azure Development** under installationen. Om du vill skapa ett Azure Functions-projekt med hjälp av Visual Studio 2017 i stället måste du först installera de [senaste Azure Functions verktygen](functions-develop-vs.md#check-your-tools-version).

![Installera Visual Studio med arbets belastningen Azure Development](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/dotnet/) innan du börjar.

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio skapar ett projekt och en klass som innehåller en exempel kod för funktionen HTTP-utlösare. `FunctionName`Attributet Method anger namnet på funktionen, som som standard är `Function1` . `HttpTrigger`Attributet anger att funktionen utlöses av en HTTP-begäran. Den formaterade exempelkoden skickar ett HTTP-svar som innehåller ett värde från förfrågan eller frågesträngen.

Utöka funktionerna i din funktion med indata och utgående bindningar genom att tillämpa lämpliga attribut för-metoden. Mer information finns i avsnittet [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Utlösare och bindningar) i [Azure Functions C# developer reference](functions-dotnet-class-library.md) (Azure Functions C#-referens för utvecklare).

Nu när du har skapat ditt funktions projekt och en HTTP-utlösare kan du testa den på den lokala datorn.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio integreras med Azure Functions Core Tools så att du kan testa dina funktioner lokalt med den fullständiga Azure Functions-körningen.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Innan du kan publicera ditt projekt måste du ha en Function-app i din Azure-prenumeration. Visual Studio-publicering skapar en Function-app för första gången du publicerar projektet.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera bas-URL: en för Function-appen från sidan **publicera** profil. Ersätt den `localhost:port` del av URL: en som du använde för att testa funktionen lokalt med den nya bas-URL: en. Lägg till frågesträngen `?name=<YOUR_NAME>` till denna URL och kör begäran.

    Den URL som anropar funktionen HTTP-utlösare har följande format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren till den fjärranslutna GET-begäran som returnerades av funktionen:

    ![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Visual Studio för att skapa och publicera en C# Function-app i Azure med en enkel HTTP-utlösnings funktion. 

Gå vidare till nästa artikel om du vill lära dig hur du lägger till en Azure Storage Queue-bindning till din funktion:
> [!div class="nextstepaction"]
> [Lägg till en Azure Storage Queue-bindning till din funktion](functions-add-output-binding-storage-queue-vs.md)

