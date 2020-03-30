---
title: Skapa din första beständiga funktion i Azure med hjälp av C#
description: Skapa och publicera en azure durable-funktion med Visual Studio eller Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132800"
---
# <a name="create-your-first-durable-function-in-c"></a>Skapa din första beständiga funktion i C\#

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

::: zone pivot="code-editor-vscode"

I den här artikeln får du lära dig hur du använder Visual Studio Code för att lokalt skapa och testa en "hello world" varaktig funktion.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure. Dessa verktyg är tillgängliga som en del av tillägget VS Code [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Köra beständiga funktioner i Azure](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Installera följande VS-kodtillägg:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Kontrollera att du har den senaste versionen av [Azure Functions Core Tools](../functions-run-local.md).

* Varaktiga funktioner kräver ett Azure-lagringskonto. Du behöver en Azure-prenumeration.

* Kontrollera att du har version 3.1 eller en senare version av [.NET Core SDK](https://dotnet.microsoft.com/download) installerad.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt. 

1. Öppna kommandopaletten genom att trycka på F1 (eller Ctrl/Cmd+Skift+P) i Visual Studio-kod. Sök efter och välj `Azure Functions: Create New Project...`i kommandopaletten .

    ![Skapa ett funktionsprojekt](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Välj en tom mappplats för projektet och välj **Välj**.

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj ett språk för funktionsappprojektet | C# | Skapa ett lokalt C#Functions-projekt. |
    | Välj en version | Azure-funktioner v3 | Du ser bara det här alternativet när kärnverktygen inte redan är installerade. I det här fallet installeras Core Tools första gången du kör appen. |
    | Välj en mall för projektets första funktion | Hoppa över för tillfället | |
    | Välj hur du vill öppna projektet | Öppna i aktuellt fönster | Öppnar VS-kod igen i den valda mappen. |

Visual Studio Code installerar Azure Functions Core Tools om det behövs. Det skapar också ett funktionsappprojekt i en mapp. Det här projektet innehåller konfigurationsfilerna [host.json](../functions-host-json.md) och [local.settings.json.](../functions-run-local.md#local-settings-file)

## <a name="add-functions-to-the-app"></a>Lägga till funktioner i appen

Följande steg använder en mall för att skapa varaktig funktionskod.

1. Sök efter och välj `Azure Functions: Create Function...`i kommandopaletten .

1. Ange följande information efter anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Hållbara funktionerOrchestration | Skapa en orkestrering med varaktiga funktioner |
    | Ange ett funktionsnamn | HejOrchestration | Namn på den klass där funktioner skapas |
    | Ange ett namnområde | Företag.Funktion | Namnområde för den genererade klassen |

1. När VS-kod uppmanar dig att välja ett lagringskonto väljer du **Välj lagringskonto**. Ange följande information för att skapa ett nytt lagringskonto i Azure efter anvisningarna.

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en prenumeration | *namnet på din prenumeration* | Välj din Azure-prenumeration |
    | Välj ett lagringskonto | Skapa ett nytt lagringskonto |  |
    | Ange namnet på det nya lagringskontot | *unikt namn* | Namn på lagringskontot som ska skapas |
    | Välj en resursgrupp | *unikt namn* | Namn på den resursgrupp som ska skapas |
    | Välja en plats | *regionen* | Välj en region nära dig |

En klass som innehåller de nya funktionerna läggs till i projektet. VS-kod lägger också till anslutningssträngen för lagringskonto [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) i *local.settings.json* och en referens till NuGet-paketet i *CSproj-projektfilen.*

Öppna den nya *HelloOrchestration.cs* filen för att visa innehållet. Det här beständiga funktionen är ett enkelt funktionslänkningsexempel med följande metoder:  

| Metod | FunctionName | Beskrivning |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Hanterar varaktig orkestrering. I det här fallet startar orkestreringen, den skapar en lista och lägger till resultatet av tre funktionsanrop i listan.  När de tre funktionsanropen har slutförts returnerar den listan. |
| **`SayHello`** | `HelloOrchestration_Hello` | Funktionen returnerar ett ”hello”. Det är funktionen som innehåller affärslogiken som orkestreras. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | En [HTTP-utlöst funktion](../functions-bindings-http-webhook.md) som startar en instans av orkestreraren och returnerar ett statuskontrollsvar. |

Nu när du har skapat ditt funktionsprojekt och en beständig funktion kan du testa den på en lokal dator.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio Code.

1. Om du vill testa funktionen anger `SayHello` du en brytpunkt i aktivitetsfunktionskoden och trycker på F5 för att starta funktionsappprojektet. Utdata från Core Tools visas på panelen **Terminal**.

    > [!NOTE]
    > Mer information om felsökning finns i diagnostiken för [varaktiga funktioner.](durable-functions-diagnostics.md#debugging)

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Med ett verktyg som [Brevbärare](https://www.getpostman.com/) eller [cURL](https://curl.haxx.se/)skickar du en HTTP POST-begäran till URL-slutpunkten.

   Svaret är det första resultatet från HTTP-funktionen, som anger att den beständiga orkestreringen har startats korrekt. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

1. Kopiera URL-värdet `statusQueryGetUri` för och klistra in det i webbläsarens adressfält och kör begäran. Alternativt kan du också fortsätta att använda Postman för att utfärda GET-begäran.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör få ett eventuellt svar, vilket visar oss instansen har slutförts, och inkluderar utgångar eller resultat av den varaktiga funktionen. Det ser ut som: 

    ```json
    {
        "name": "HelloOrchestration",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Om du vill sluta felsöka trycker du på **Skift + F5** i VS-kod.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa och publicera en C# durable function app.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

I den här artikeln får du lära dig hur du visual studio 2019 för att lokalt skapa och testa en "hello world" hållbar funktion.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure. Dessa verktyg är tillgängliga som en del av Azure-utvecklingsarbetsbelastningen i Visual Studio 2019.

![Köra beständiga funktioner i Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Installera [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Kontrollera att även arbetsbelastningen **Azure-utveckling** är installerad. Visual Studio 2017 stöder också utveckling av varaktiga funktioner, men användargränssnittet och stegen skiljer sig åt.

* Kontrollera att [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) är installerad och körs.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

Med Azure Functions-mallen skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Välj **Nytt** > **projekt** på **Arkiv-menyn** i Visual Studio.

1. Sök efter i dialogrutan Skapa `functions`ett nytt **projekt** , välj mallen Azure **Functions** och välj **Nästa**. 

    ![Dialogrutan Nytt projekt för att skapa en funktion i Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Skriv ett **projektnamn** för projektet och välj **OK**. Projektnamnet måste vara giltigt som ett C#-namnområde, så använd inte understreck, bindestreck eller andra icke-numeriska tecken.

1. I **Skapa ett nytt Azure Functions Application**använder du de inställningar som anges i tabellen som följer avbildningen.

    ![Skapa en ny Azure Functions Application-dialog i Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Inställning      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure-funktioner 3.0 <br />(.NET Core) | Skapar ett funktionsprojekt som använder körningen version 3.0 av Azure Functions, som stöder .NET Core 3.1. Läs mer i informationen om att [köra rätt körningsversion av Azure Functions](../functions-versions.md).   |
    | **Mall** | Tom | Detta skapar en tom funktionsapp. |
    | **Lagringskonto**  | Lagringsemulator | Det krävs ett lagringskonto för tillståndshanteringen för den beständiga funktionen. |

4. Välj **Skapa** om du vill skapa ett tomt funktionsprojekt. Det här projektet har grundläggande konfigurationsfiler som behövs för att köra dina funktioner.

## <a name="add-functions-to-the-app"></a>Lägga till funktioner i appen

Följande steg använder en mall för att skapa varaktig funktionskod.

1. Högerklicka på projektet i Visual Studio och välj **Lägg till** > **ny Azure-funktion**.

    ![Lägga till ny funktion](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Kontrollera **att Azure-funktion** är markerad på tilläggsmenyn, skriv ett namn på C#-filen och välj sedan **Lägg till**.

1. Välj mallen **Orchestration för varaktiga funktioner** och välj sedan **Ok**

    ![Välja mall för beständig](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

En ny beständig funktion läggs till i appen.  Öppna den nya .cs-filen för att visa innehållet. Det här beständiga funktionen är ett enkelt funktionslänkningsexempel med följande metoder:  

| Metod | FunctionName | Beskrivning |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hanterar varaktig orkestrering. I det här fallet startar orkestreringen, den skapar en lista och lägger till resultatet av tre funktionsanrop i listan.  När de tre funktionsanropen har slutförts returnerar den listan. |
| **`SayHello`** | `<file-name>_Hello` | Funktionen returnerar ett ”hello”. Det är funktionen som innehåller affärslogiken som orkestreras. |
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

    Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör så småningom få ett svar som liknar följande.  Detta resultat visar oss instansen har slutförts, och inkluderar utgångar eller resultat av den varaktiga funktionen.

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

5. Om du vill sluta felsöka trycker du på **Skift + F5**.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Du kan skapa en funktionsapp direkt från Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en.

    Den URL som anropar HTTP-utlösaren för den beständiga funktionen ska ha följande format:

        https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio för att skapa och publicera en beständig C#-funktionsapp.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)

::: zone-end
