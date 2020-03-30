---
title: Ansluta funktioner till Azure Storage med Visual Studio
description: Lär dig hur du lägger till en utdatabindning för att ansluta dina C#-klassbiblioteksfunktioner till en Azure Storage-kö med Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849215"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Ansluta funktioner till Azure Storage med Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du använder Visual Studio för att ansluta funktionen som du skapade i föregående [snabbstartsartikel] till Azure Storage. Utdatabindningen som du lägger till i den här funktionen skriver data från HTTP-begäran till ett meddelande i en Azure Queue storage-kö. 

De flesta bindningar kräver en lagrad anslutningssträng som Funktioner använder för att komma åt den bundna tjänsten. För att göra det enklare använder du det lagringskonto som du skapade med funktionsappen. Anslutningen till det här kontot lagras redan `AzureWebJobsStorage`i appinställningen .  

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln måste du: 

 - Fyll [i del 1 av snabbstarten i Visual Studio](./functions-create-first-function-vs-code.md). 

- Logga in på din Azure-prenumeration från Visual Studio.

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

I den [föregående snabbstartsartikeln](functions-create-first-function-vs-code.md)skapade du en funktionsapp i Azure tillsammans med det nödvändiga lagringskontot. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagringskö i samma konto. Om du vill ansluta till ditt Lagringskonto när du kör funktionen lokalt måste du hämta appinställningarna till filen *local.settings.json.* 

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. 

1. Under **Åtgärder**väljer du **Redigera Azure App Service Settings**. 

    ![Redigera programinställningarna](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Under **AzureWebJobsStorage**kopierar du **fjärrsträngvärdet** till **Lokal**och väljer sedan **OK**. 

Lagringsbindningen, `AzureWebJobsStorage` som använder inställningen för anslutningen, kan nu ansluta till din kölagring när du kör lokalt.

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en kölagringsutdatabindning behöver du tillägget Lagringsbindningar installerat innan du kör projektet. Med undantag för HTTP- och timerutlösare implementeras bindningar som tilläggspaket. 

1. Välj **NuGet Package Manager** > **Package Manager Console**på **Verktyg-menyn.** 

1. I konsolen kör du följande [kommandot Install-Package](/nuget/tools/ps-ref-install-package) för att installera lagringstilläggen:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nu kan du lägga till lagringsutdatabindningen i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats `name` kan du använda bindningen för att komma åt den som ett attribut i funktionssignaturen. Genom att använda en utdatabindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en köreferens eller skriva data. Funktionskörnings- och köutdatabindningen utför dessa uppgifter åt dig.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

En ny `outqueue` kö som heter skapas i ditt lagringskonto av funktionskörningen när utdatabindningen först används. Du ska använda Cloud Explorer för att verifiera att kön har skapats tillsammans med det nya meddelandet.

## <a name="examine-the-output-queue"></a>Granska utdatakö

1. Välj **Cloud Explorer**på Visual Studio på **Visa-menyn** .

1. Expandera din Azure-prenumeration och **lagringskonton i** **Cloud Explorer**och expandera sedan det lagringskonto som används av din funktion. Om du inte kommer ihåg lagringskontonamnet kontrollerar du `AzureWebJobsStorage` inställningen för anslutningssträngen i filen *local.settings.json.*  

1. Expandera noden **Köer** och dubbelklicka sedan på kön med namnet **utköa** för att visa innehållet i kön i Visual Studio. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure* så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

    ![Kömeddelande som visas i Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Kör funktionen igen, skicka en ny begäran och du ser ett nytt meddelande visas i kön.  

Nu är det dags att publicera om den uppdaterade funktionsappen till Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Distribuera om och verifiera den uppdaterade appen

1. Högerklicka på projektet i **Solution Explorer**och välj **Publicera**och välj sedan **Publicera** för att publicera projektet igen till Azure.

1. När distributionen är klar kan du återigen använda webbläsaren för att testa funktionen omdeviserad. Lägg till frågesträngen `&name=<yourname>` i URL:en som tidigare.

1. Visa [meddelandet i lagringskön](#examine-the-output-queue) igen för att kontrollera att utdatabindningen igen genererar ett nytt meddelande i kön.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat http-utlöst funktion för att skriva data till en lagringskö. Mer information om hur du utvecklar funktioner finns i [Utveckla Azure-funktioner med Visual Studio](functions-develop-vs.md).

Därefter bör du aktivera Application Insights-övervakning för din funktionsapp:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[föregående snabbstartsartikel]: functions-create-your-first-function-visual-studio.md
