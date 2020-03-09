---
title: Utveckla Azure Functions med Visual Studio
description: Lär dig hur du utvecklar och testar Azure Functions med hjälp av Azure Functions verktyg för Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357093"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure Functions med Visual Studio  

Med Visual Studio kan du utveckla, testa och distribuera C# klass biblioteks funktioner till Azure. Om den här upplevelsen är din första med Azure Functions kan du läsa mer i [Introduktion till Azure Functions](functions-overview.md).

Visual Studio ger följande fördelar när du utvecklar dina funktioner: 

* Redigera, skapa och kör funktioner på din lokala utvecklings dator. 
* Publicera Azure Functions-projektet direkt till Azure och skapa Azure-resurser efter behov. 
* Använd C# attribut för att deklarera funktions bindningar direkt i C# koden.
* Utveckla och distribuera förkompilerade C# funktioner. Föruppfyllda funktioner ger en bättre kall start med prestanda än C# skriptbaserade funktioner. 
* Koda dina funktioner i C# samtidigt som du har alla fördelar med Visual Studio-utveckling. 

Den här artikeln innehåller information om hur du använder Visual Studio för C# att utveckla klass biblioteks funktioner och publicera dem i Azure. Innan du läser den här artikeln bör du slutföra [snabb starten för Functions för Visual Studio](functions-create-your-first-function-visual-studio.md). 

Om inget annat anges är procedurer och exempel som visas för Visual Studio 2019. 

## <a name="prerequisites"></a>Förutsättningar

Azure Functions verktyg ingår i Azure Development-arbetsbelastningen för Visual Studio från och med Visual Studio 2017. Se till att du inkluderar arbets belastningen **Azure Development** i Visual Studio-installationen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage konto, skapas i prenumerationen under publicerings processen.

> [!NOTE]
> I Visual Studio 2017 installerar Azure Development-arbetsbelastningen Azure Functions-verktyg som ett separat tillägg. När du uppdaterar din Visual Studio 2017 bör du också kontrol lera att du använder den [senaste versionen](#check-your-tools-version) av Azure Functionss verktygen. I följande avsnitt visas hur du kontrollerar och (vid behov) uppdaterar du Azure Functions tools-tillägget i Visual Studio 2017. 
>
> Hoppa över det här avsnittet när du använder Visual Studio 2019.

### <a name="check-your-tools-version"></a>Kontrol lera din verktygs version i Visual Studio 2017

1. Från **verktyg** -menyn väljer du **tillägg och uppdateringar**. Expandera **installerade** > - **verktyg** och välj **Azure Functions-och webb jobb verktyg**.

    ![Kontrol lera version av funktions verktyg](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Observera den installerade **versionen**. Du kan jämföra den här versionen med den senaste versionen som listas [i viktig information](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Om din version är äldre uppdatera dina verktyg i Visual Studio som du ser i följande avsnitt.

### <a name="update-your-tools-in-visual-studio-2017"></a>Uppdatera dina verktyg i Visual Studio 2017

1. I dialog **rutan tillägg och uppdateringar** expanderar **du uppdateringar** > **Visual Studio Marketplace**, väljer **Azure Functions och webb jobb verktyg** och väljer **Uppdatera**.

    ![Uppdatera funktions verktyg versionen](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. När verktyg uppdateringen laddas ned, stänger du Visual Studio till utlösaren verktygen uppdatera med VSIX installationsprogrammet.

1. I installations programmet väljer du **OK** för att starta och sedan **ändra** för att uppdatera verktygen. 

1. När uppdateringen är klar väljer du **Stäng** och startar om Visual Studio.

> [!NOTE]  
> I Visual Studio 2019 och senare uppdateras tillägget Azure Functions verktyg som en del av Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Projekt mal len skapar ett C# projekt, installerar `Microsoft.NET.Sdk.Functions` NuGet-paketet och anger mål ramverket. Det nya projektet har följande filer:

* **Host. JSON**: gör att du kan konfigurera funktions värden. Dessa inställningar gäller både när de körs lokalt och i Azure. Mer information finns i [Host. JSON-referens](functions-host-json.md).

* **Local. Settings. JSON**: hanterar inställningar som används när du kör funktioner lokalt. De här inställningarna används inte när de körs i Azure. Mer information finns i [filen med lokala inställningar](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom filen Local. Settings. JSON kan innehålla hemligheter måste du utesluta den från projekt käll kontrollen. Inställningen **Kopiera till utdata-katalog** för den här filen bör alltid vara **kopia om**den är nyare. 

Mer information finns i [funktions klass biblioteks projekt](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Inställningar i Local. Settings. JSON laddas inte upp automatiskt när du publicerar projektet. För att se till att dessa inställningar också finns i din Function-app i Azure måste du ladda upp dem när du har publicerat projektet. Mer information finns i [funktions inställningarna för appen](#function-app-settings).

Värden i **ConnectionString** publiceras aldrig.

Värdena för funktionen app-inställningar kan också läsas i koden som miljövariabler. Mer information finns i [miljövariabler](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Functions-körningen använder ett Azure Storage-konto internt. För alla utlösare typer än HTTP och Webhooks måste du ange **Values. AzureWebJobsStorage** -nyckeln till en giltig anslutnings sträng för Azure Storage konto. Din Function-app kan också använda [Azure Storage-emulatorn](../storage/common/storage-use-emulator.md) för anslutnings inställningen **AzureWebJobsStorage** som krävs av projektet. Om du vill använda emulatorn ställer du in värdet för **AzureWebJobsStorage** på `UseDevelopmentStorage=true`. Ändra den här inställningen till en faktisk lagrings konto anslutnings sträng före distributionen.

Så här anger du anslutnings strängen för lagrings kontot:

1. Öppna **Cloud Explorer**i Visual Studio, expandera **lagrings konto** > **ditt lagrings konto**och kopiera sedan värdet för **primär anslutnings sträng** i fliken **Egenskaper** .

2. Öppna filen Local. Settings. json i projektet och ange värdet för **AzureWebJobsStorage** -nyckeln till den anslutnings sträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i **matrisen** Arrays för alla andra anslutningar som krävs av dina funktioner. 

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

I C# klass biblioteks funktioner definieras de bindningar som används av funktionen genom att tillämpa attribut i koden. När du skapar funktions utlösare från de angivna mallarna tillämpas utlösarens attribut. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure Function**, Skriv ett **namn** för klassen och klicka på **Lägg till**.

2. Välj utlösaren, ange bindnings egenskaper och klicka på **skapa**. I följande exempel visas inställningarna när du skapar en kö lagrings funktion som utlöses. 

    ![Skapa en funktion som utlöses av kön](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Detta utlösare exempel använder en anslutnings sträng med en nyckel med namnet **QueueStorage**. Den här inställningen för anslutnings strängen måste definieras i den [lokala. Settings. JSON-filen](functions-run-local.md#local-settings-file).

3. Granska den nyligen tillagda klassen. Du ser en statisk **körnings** metod som attributas med attributet **functionname** . Det här attributet anger att metoden är start punkten för funktionen.

    Följande C# klass representerar t. ex. en grundläggande kö Storage-utlöst funktion:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Ett bindande attribut används för varje bindnings parameter som anges till Start punkt metoden. Attributet använder bindnings informationen som parametrar. I det föregående exemplet har den första parametern ett **QueueTrigger** -attribut tillämpat, vilket indikerar att funktionen utlöses av kön. Könamnet och anslutnings strängens inställnings namn skickas som parametrar till attributet **QueueTrigger** . Mer information finns i [Azure Queue Storage-bindningar för Azure Functions](functions-bindings-storage-queue-trigger.md).

Du kan använda proceduren ovan för att lägga till fler funktioner till ditt Function app-projekt. Varje funktion i projektet kan ha en annan utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Lägga till bindningar

Som med utlösare läggs indata-och utgående bindningar till i funktionen som binding-attribut. Lägg till bindningar till en funktion på följande sätt:

1. Kontrol lera att du har [konfigurerat projektet för lokal utveckling](#configure-the-project-for-local-development).

2. Lägg till lämpligt paket för NuGet-tillägg för den angivna bindningen. Mer information finns i [lokal C# utveckling med Visual Studio](./functions-bindings-register.md#local-csharp) i artikeln utlösare och bindningar. De bindande NuGet-paket kraven finns i referens artikeln för bindningen. Sök till exempel efter paket krav för Event Hubs-utlösaren i [referens artikeln Event Hubs bindning](functions-bindings-event-hubs.md).

3. Om det finns appinställningar som bindningen kräver, lägger du till dem i **värde** samlingen i den [lokala inställnings filen](functions-run-local.md#local-settings-file). Dessa värden används när funktionen körs lokalt. När funktionen körs i Function-appen i Azure används [funktionen app-inställningar](#function-app-settings) .

4. Lägg till lämpligt binding-attribut i Metodsignaturen. I följande exempel utlöser ett Queue meddelande funktionen och utgående bindning skapar ett nytt meddelande i kö med samma text i en annan kö.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Anslutningen till Queue Storage hämtas från `AzureWebJobsStorage`s inställningen. Mer information finns i referens artikeln för den angivna bindningen. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.

Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

När projektet körs kan du testa koden på samma sätt som du testar distribuerad funktion. Mer information finns i [strategier för att testa koden i Azure Functions](functions-test-a-function.md). När du kör i fel söknings läge, trycks Bryt punkter i Visual Studio som förväntat. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Mer information om hur du använder Azure Functions Core Tools finns i [kod och testa Azure Functions lokalt](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicera till Azure

När du publicerar från Visual Studio används en av två distributions metoder:

* [Webb distribution](functions-deployment-technologies.md#web-deploy-msdeploy): paket och distribuerar Windows-appar till valfri IIS-server.
* [Zip-distribution med kör från-paket aktiverat](functions-deployment-technologies.md#zip-deploy): rekommenderas för Azure Functions distributioner.

Använd följande steg för att publicera projektet till en Function-app i Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Alla inställningar som du har lagt till i den lokala. Settings. JSON måste också läggas till i Function-appen i Azure. De här inställningarna laddas inte upp automatiskt när du publicerar projektet.

Det enklaste sättet att överföra de nödvändiga inställningarna till din Function-app i Azure är att använda den **hantera program inställningar...** som visas när du har publicerat projektet.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Dialog rutan **program inställningar** för Function-appen visas där du kan lägga till nya program inställningar eller ändra befintliga.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** representerar ett inställnings värde i den lokala. Settings. JSON-filen och **Remote** är den aktuella inställningen i Function-appen i Azure.  Välj **Lägg till inställning** för att skapa en ny app-inställning. Använd **värdet infoga värde från lokal** länk för att kopiera ett inställnings värde till fältet **Remote** . Väntande ändringar skrivs till den lokala inställnings filen och Function-appen när du väljer **OK**.

> [!NOTE]
> Som standard kontrol leras inte filen Local. Settings. json i käll kontrollen. Det innebär att när du klonar ett lokalt Functions-projekt från käll kontrollen har projektet ingen lokal. Settings. JSON-fil. I så fall måste du skapa den lokala. Settings. JSON-filen manuellt i projekt roten så att dialog rutan **program inställningar** fungerar som förväntat. 

Du kan också hantera program inställningar på något av följande sätt:

* [Använda Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Använd alternativet `--publish-local-settings` Publish i Azure Functions Core tools](functions-run-local.md#publish).
* [Använda Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Övervaknings funktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera din Function-app med Azure Application insikter. När du skapar en Function-app i Azure Portal görs denna integrering som standard. Men när du skapar en Function-app under Visual Studio-publiceringen är integreringen i din Function-app i Azure inte färdig.

Så här aktiverar du Application Insights för din Function-app:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [kod och testa Azure Functions lokalt](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klass bibliotek finns i [Azure Functions C# Developer Reference](functions-dotnet-class-library.md). Den här artikeln länkar också till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
