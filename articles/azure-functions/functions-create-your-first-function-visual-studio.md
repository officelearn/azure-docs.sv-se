---
title: Skapa din första funktion i Azure med Visual Studio
description: Skapa och publicera en HTTP-utlöst Azure Function med Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 6d09453db0fdf8c09ad6d90c63b27f48508302be
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230714"
---
# <a name="create-your-first-function-using-visual-studio"></a>Skapa din första funktion med Visual Studio

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

In this article, you learn how to use Visual Studio 2019 to locally create and test a "hello world" function and then publish it to Azure. This quickstart is designed for Visual Studio 2019. When creating a Functions project using Visual Studio 2017, you must first install the [latest Azure Functions tools](functions-develop-vs.md#check-your-tools-version).

![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Krav

To complete this tutorial, you must first install [Visual Studio 2019](https://azure.microsoft.com/downloads/). Kontrollera att även arbetsbelastningen **Azure-utveckling** är installerad.

![Install Visual Studio with the Azure development workload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio creates a project and class that contains boilerplate code for the HTTP trigger function type. The `FunctionName` attribute on the method sets the name of the function, which by default is `HttpTrigger`. The `HttpTrigger` attribute specifies that the function is triggered by an HTTP request. Den formaterade exempelkoden skickar ett HTTP-svar som innehåller ett värde från förfrågan eller frågesträngen.

You can expand the capabilities of your function using input and output bindings by applying the appropriate attributes to the method. Mer information finns i avsnittet [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Utlösare och bindningar) i [Azure Functions C# developer reference](functions-dotnet-class-library.md) (Azure Functions C#-referens för utvecklare).

Nu när du har skapat ditt funktionsprojekt och en HTTP-utlöst funktion kan du testa den på en lokal dator.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio integrates with Azure Functions Core Tools so that you can test your functions locally using the full Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Visual Studio publishing creates a function app for you the first time you publish your project.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en. Lägg till frågesträngen `?name=<YOUR_NAME>` i URL:en som tidigare och kör din begäran.

    Den URL som anropar din HTTP-utlösta funktion ska ha följande format:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren:

    ![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Nästa steg

You have used Visual Studio to create and publish a C# function app in Azure with a simple HTTP triggered function. To learn more about developing functions as .NET class libraries, see [Azure Functions C# developer reference](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs.md)
