---
title: 'Snabbstart: Skapa din första funktion i Azure med Visual Studio'
description: I den här snabbstarten får du lära dig hur du skapar och publicerar en HTTP-utlösare Azure-funktion med hjälp av Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: dadb705e8b756418be40d738aa81b3e77961aead
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657520"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Snabbstart: Skapa din första funktion i Azure med Visual Studio

Med Azure Functions kan du köra koden i en serverlös miljö utan att först behöva skapa en virtuell dator eller publicera ett webbprogram.

I den här snabbstarten får du lära dig hur du använder Visual Studio 2019 för att skapa och testa en HTTP-utlösappapp för "hello world", som du sedan publicerar i Azure. 

![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Den här snabbstarten är utformad för Visual Studio 2019. 

## <a name="prerequisites"></a>Krav

Slutför den här självstudien genom att först installera [Visual Studio 2019](https://azure.microsoft.com/downloads/). Se till att du väljer **Azure-utvecklingsarbetsbelastning** under installationen. Om du vill skapa ett Azure Functions-projekt med Visual Studio 2017 i stället måste du först installera de [senaste Azure Functions-verktygen](functions-develop-vs.md#check-your-tools-version).

![Installera Visual Studio med Azure-utvecklingsarbetsbelastningen](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio skapar ett projekt och en klass som innehåller standardkod för http-utlösarfunktionstypen. Metodattributet `FunctionName` anger namnet på funktionen, `Function1`som som standard är . Attributet `HttpTrigger` anger att funktionen utlöses av en HTTP-begäran. Den formaterade exempelkoden skickar ett HTTP-svar som innehåller ett värde från förfrågan eller frågesträngen.

Utöka funktionens funktioner med in- och utdatabindningar genom att använda lämpliga attribut på metoden. Mer information finns i avsnittet [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Utlösare och bindningar) i [Azure Functions C# developer reference](functions-dotnet-class-library.md) (Azure Functions C#-referens för utvecklare).

Nu när du har skapat funktionsprojektet och en HTTP-utlösarfunktion kan du testa det på den lokala datorn.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio integreras med Azure Functions Core Tools så att du kan testa dina funktioner lokalt med hjälp av den fullständiga Azure Functions-körningen.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Innan du kan publicera projektet måste du ha en funktionsapp i din Azure-prenumeration. Visual Studio-publicering skapar en funktionsapp för dig första gången du publicerar projektet.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera bas-URL:en för funktionsappen från sidan **Publicera** profil. Ersätt `localhost:port` den del av WEBBADRESSEN som du använde för att testa funktionen lokalt med den nya bas-URL:en. Lägg till frågesträngen `?name=<YOUR_NAME>` i den här URL:en och kör begäran.

    URL:en som anropar HTTP-utlösaren är i följande format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren på den fjärrfråkomsbegäran som returneras av funktionen:

    ![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Visual Studio för att skapa och publicera en C#-funktionsapp i Azure med en enkel HTTP-utlösarfunktion. 

Gå vidare till nästa artikel om du vill lära dig hur du lägger till en Azure Storage-köbindning i din funktion:
> [!div class="nextstepaction"]
> [Lägga till en Azure Storage-köbindning i din funktion](functions-add-output-binding-storage-queue-vs.md)

