---
title: Skapa din första beständiga funktion i Azure med hjälp av C#
description: Skapa och publicera en beständig Azure-funktion med hjälp av Visual Studio.
author: jeffhollan
ms.topic: quickstart
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3ead9eefd6f0d4c504cc7711ea4e03facf8edc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231513"
---
# <a name="create-your-first-durable-function-in-c"></a>Skapa din första beständiga funktion i C\#

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In this article, you learn how to use the Visual Studio 2019 to locally create and test a "hello world" durable function.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure. These tools are available as part of the Azure development workload in Visual Studio 2019.

![Köra beständiga funktioner i Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* Install [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Kontrollera att även arbetsbelastningen **Azure-utveckling** är installerad. Visual Studio 2017 also supports Durable Functions development, but the UI and steps differ.

* Kontrollera att [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) är installerad och körs.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

Med Azure Functions-mallen skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. Välj **Nytt** > **Projekt** från **Arkiv**-menyn i Visual Studio.

1. In the **Add a new project** dialog, search for `functions`, choose the **Azure Functions** template, and select **Next**. 

    ![Dialogrutan Nytt projekt för att skapa en funktion i Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Type a **Project name** for your project, and select **OK**. The project name must be valid as a C# namespace, so don't use underscores, hyphens, or any other nonalphanumeric characters.

1. In **Create a new Azure Functions Application**, use the settings specified in the table that follows the image.

    ![Create a new Azure Functions Application dialog in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Inställning      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure Functions 2.0 <br />(.NET Core) | Creates a function project that uses the version 2.0 runtime of Azure Functions, which supports .NET Core. Azure Functions 1.0 supports the .NET Framework. Läs mer i informationen om att [köra rätt körningsversion av Azure Functions](../functions-versions.md).   |
    | **Mall** | Tom | Detta skapar en tom funktionsapp. |
    | **Lagringskonto**  | Lagringsemulator | Det krävs ett lagringskonto för tillståndshanteringen för den beständiga funktionen. |

4. Select **Create** to create an empty function project. Det här projektet har grundläggande konfigurationsfiler som behövs för att köra dina funktioner.

## <a name="add-functions-to-the-app"></a>Lägga till funktioner i appen

Följande steg använder en mall för att skapa varaktig funktionskod.

1. Högerklicka på projektet i Visual Studio och välj **Lägg till** > **Ny Azure-funktion**.

    ![Lägga till ny funktion](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Verify **Azure Function** is selected from the add menu, type a name for your C# file, and then select **Add**.

1. Select the **Durable Functions Orchestration** template and then select **Ok**

    ![Välja mall för beständig](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> This template currently creates a durable function using an older 1.x version of the extension. See the [Durable Functions Versions](durable-functions-versions.md) article for information about how to upgrade to the newer 2.x versions of Durable Functions.

En ny beständig funktion läggs till i appen.  Öppna den nya .cs-filen för att visa innehållet. Det här beständiga funktionen är ett enkelt funktionslänkningsexempel med följande metoder:  

| Metod | FunctionName | Beskrivning |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hanterar varaktig orkestrering. I det här fallet startar orkestreringen, den skapar en lista och lägger till resultatet av tre funktionsanrop i listan.  När de tre funktionsanropen har slutförts returnerar den listan. |
| **`SayHello`** | `<file-name>_Hello` | Funktionen returnerar ett ”hello”. It is the function that contains the business logic that is being orchestrated. |
| **`HttpStart`** | `<file-name>_HttpStart` | En [HTTP-utlöst funktion](../functions-bindings-http-webhook.md) som startar en instans av orkestreraren och returnerar ett statuskontrollsvar. |

Nu när du har skapat ditt funktionsprojekt och en beständig funktion kan du testa den på en lokal dator.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.

1. Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält och kör begäran. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren:

    ![Svar för funktion-localhost i webbläsaren](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Svaret är det första resultatet från HTTP-funktionen, som anger att den beständiga orkestreringen har startats korrekt.  Det är inte ännu slutresultatet av orkestreringen.  Svaret innehåller några användbara URL:er.  För tillfället kör vi en fråga om orkestreringens status.

4. Kopiera URL-värdet för `statusQueryGetUri`, klistra in det i webbläsarens adressfält och kör begäran.

    Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör så småningom få ett svar som liknar följande.  This output shows us the instance has completed, and includes the outputs or results of the durable function.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Tryck på **Skift + F5** för att stoppa felsökningen.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Du kan skapa en funktionsapp direkt från Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en.

    Den URL som anropar HTTP-utlösaren för den beständiga funktionen ska ha följande format:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio för att skapa och publicera en beständig C#-funktionsapp.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)