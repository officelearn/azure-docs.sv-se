---
title: Utveckla Azure-funktioner med hjälp av Visual Studio | Microsoft Docs
description: Lär dig mer om att utveckla och testa Azure Functions med hjälp av Azure Functions Tools för Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 63a2d5a62cf2cdfa2a1a08c56ef5a87aaaa13529
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395551"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure-funktioner med hjälp av Visual Studio  

Azure Functions Tools för Visual Studio 2017 är ett tillägg för Visual Studio som låter dig utveckla, testa och distribuera C#-funktioner till Azure. Om det här är din första med Azure Functions, kan du läsa mer på [en introduktion till Azure Functions](functions-overview.md).

Azure Functions Tools ger följande fördelar: 

* Redigera, skapa och köra functions på din lokala utvecklingsdator. 
* Publicera projektet Azure Functions direkt till Azure. 
* Använda WebJobs-attribut för att deklarera funktionsbindningar direkt i C#-kod i stället för att upprätthålla en separat function.json för att binda definitioner.
* Utveckla och distribuera förkompilerad C#-funktioner. Före följt functions erbjuder en bättre kallstart prestanda än C#-skriptbaserade funktioner. 
* Koda dina funktioner i C# samtidigt som du har alla fördelarna med Visual Studio-utveckling. 

Den här artikeln visar hur du använder Azure Functions Tools för Visual Studio 2017 för att utveckla dina funktioner i C#. Du också lära dig hur du publicerar projektet till Azure som en .NET-sammansättning.

> [!IMPORTANT]
> Blanda inte lokal utveckling med portalen utvecklingen i samma funktionsapp. När du publicerar från ett lokalt projekt till en funktionsapp skriver distributionsprocessen över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Förutsättningar

Med Azure Functions Tools ingår i arbetsbelastningen Azure development av [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/), eller en senare version. Kontrollera att du inkluderar den **Azure development** arbetsbelastningen i Visual Studio 2017-installationen:

![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Se till att din Visual Studio är uppdaterad och att du använder den [den senaste versionen](#check-your-tools-version) av Azure Functions-verktyg.

### <a name="other-requirements"></a>Andra krav

För att skapa och distribuera functions, behöver du:

* En aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration [kostnadsfria konton](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) är tillgängliga.

* Ett Azure Storage-konto. Om du vill skapa ett lagringskonto, [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

### <a name="check-your-tools-version"></a>Kontrollera vilken version av verktyg

1. Från den **verktyg** menyn, Välj **tillägg och uppdateringar**. Expandera **installerad** > **verktyg** och välj **Azure Functions och Webjobs-verktyg**.

    ![Kontrollera versionen för Functions-verktyg](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Observera den installerade **Version**. Du kan jämföra den här versionen med den senaste versionen visas [i viktig](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Om din version är äldre uppdatera dina verktyg i Visual Studio som du ser i följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I den **tillägg och uppdateringar** dialogrutan Expandera **uppdateringar** > **Visual Studio Marketplace**, Välj **Azure Functions och Webjobs-verktyg**  och välj **uppdatering**.

    ![Uppdatera versionen av Functions-verktyg](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. När verktyg uppdateringen laddas ned, stänger du Visual Studio till utlösaren verktygen uppdatera med VSIX installationsprogrammet.

3. I installationsprogrammet, väljer **OK** att starta och sedan **ändra** uppdatera verktygen. 

4. När uppdateringen är klar, väljer **Stäng** och starta om Visual Studio.

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Projektmallen skapar ett C#-projekt, installerar den `Microsoft.NET.Sdk.Functions` NuGet-paketet, och anger målramverk. Fungerar 1.x mål .NET Framework och 2.x mål .NET Standard-funktioner. Det nya projektet har följande filer:

* **Host.JSON**: låter dig konfigurera Functions-värden. Dessa inställningar gäller både när du kör lokalt och i Azure. Mer information finns i [referens för host.json](functions-host-json.md).

* **Local.Settings.JSON**: underhåller inställningar som används när du kör funktioner lokalt. De här inställningarna används inte av Azure, de som används av den [Azure Functions Core Tools](functions-run-local.md). Använd den här filen för att ange inställningar för variabler som krävs av dina funktioner. Lägg till ett nytt objekt i den **värden** matris för varje anslutning som krävs av funktions-bindningar i projektet. Mer information finns i [lokala inställningsfilen](functions-run-local.md#local-settings-file) i Azure Functions Core Tools-artikeln.

Mer information finns i [Functions klassbiblioteksprojektet](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Ett Azure Storage-konto används internt i Functions-körning. För alla utlösa andra typer än HTTP och webhooks, måste du ställa in den **Values.AzureWebJobsStorage** avgörande för att en giltig anslutningssträng för Azure Storage-konto. Funktionsappen kan också använda den [Azure storage-emulatorn](../storage/common/storage-use-emulator.md) för den **AzureWebJobsStorage** anslutning inställningen att krävs av projektet. Om du vill använda emulatorn, ange värdet för **AzureWebJobsStorage** till `UseDevelopmentStorage=true`. Du måste ändra den här inställningen till en verkligt lagringsutrymme som anslutning före distributionen.

Ange anslutningssträng för lagringskonto:

1. Öppna i Visual Studio **Cloud Explorer**, expandera **Lagringskonto** > **Your Storage Account**och välj sedan **egenskaper**och kopiera den **primär anslutningssträng** värde.

2. Öppna filen local.settings.json i projektet, och ange värdet för den **AzureWebJobsStorage** nyckeln på anslutningssträngen som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar till den **värden** matris för andra anslutningar som krävs av dina funktioner.

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

I förväg kompilerad funktion definieras de bindningar som används av funktionen genom att använda i koden. När du använder Azure Functions Tools för att skapa dina funktioner från de tillhandahållna mallarna, tillämpas attributen för dig. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure Function**, ange ett **namn** för klass och klicka på **Lägg till**.

2. Välj utlösaren, ange egenskaper för bindning och klicka på **skapa**. I följande exempel visar inställningarna när du skapar en Queue storage funktion som utlöses. 

    ![Skapa en funktion som utlöses av lagringskön](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Det här exemplet utlösaren använder en anslutningssträng med en nyckel med namnet **QueueStorage**. Den här inställningen för anslutningssträngen måste definieras i den [local.settings.json-fil](functions-run-local.md#local-settings-file).

3. Granska den nyligen tillagda klassen. Du ser en statisk **kör** metod som kan ha den **FunctionName** attribut. Det här attributet anger att metoden är startpunkten för funktionen.

    Till exempel representerar följande C#-klass ett grundläggande kö funktion som utlöses storage:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ````
    En bindning-specifika attribut tillämpas på varje bindningsparametern som angetts för metoden. Attributet tar bindningsinformationen som parametrar. I exemplet ovan den första parametern har en **QueueTrigger** attributet tillämpas, som anger funktion som utlöses kö. Könamn och namn på anslutningssträng inställningen skickas som parametrar till den **QueueTrigger** attribut. Mer information finns i [Azure Queue storage-bindningar för Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).
    
Du kan använda proceduren ovan för att lägga till fler funktioner i ditt funktionsappsprojekt. Varje funktion i projektet kan ha en annan utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Lägga till bindningar

Precis som med utlösare, indata- och bindningar läggs till i din funktion som binder attribut. Lägg till bindningar till en funktion på följande sätt:

1. Kontrollera att du har [konfigureras projektet för lokal utveckling](#configure-the-project-for-local-development).

2. Lägg till lämpligt NuGet-tillägg-paket för specifika bindningen. Mer information finns i [lokala C#-utveckling med Visual Studio](functions-triggers-bindings.md#local-csharp) i artikeln utlösare och bindningar. Bindningen-specifika NuGet-paketet krav finns i referensartikeln för bindningen. Till exempel hitta paket-krav för Event Hubs-utlösare i den [referensartikeln för Event Hubs-bindning](functions-bindings-event-hubs.md).

3. Om det finns inställningar som krävs för bindningen kan du lägga till dem i den **värden** samling i den [lokala filen](functions-run-local.md#local-settings-file). De här värdena används när funktionen körs lokalt. När funktionen körs i funktionsappen i Azure, den [fungera appinställningar](#function-app-settings) används.

4. Lägg till attributet passande bindande till Metodsignaturen. I följande exempel visas ett kömeddelande utlöser funktionen och utdatabindningen skapar ett nytt meddelande i kön med samma text i en annan kö.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            TraceWriter log)
        {
            log.Info($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
Anslutningen till Queue storage hämtas från den `AzureWebJobsStorage` inställningen. Mer information finns i referensartikeln för specifika bindningen. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.

Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

Du kan testa din kod som du vill testa distribuerade funktionen med projekt som körs. Mer information finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md). När du kör i felsökningsläge kan uppnås brytpunkter i Visual Studio som förväntat. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Mer information om hur du använder Azure Functions Core Tools finns [kod och testa Azure functions lokalt](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicera till Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Alla inställningar som du lade till i local.settings.json måste också läggas till funktionsappen i Azure. Dessa inställningar överförs inte automatiskt när du publicerar projektet.

Det enklaste sättet att ladda upp inställningarna som krävs till funktionsappen i Azure är att använda den **hantera programinställningar...**  länken som visas när du har publicerat ditt projekt. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Detta visar den **programinställningar** dialogrutan för funktionsappen, där du kan lägga till nya programinställningar eller ändra befintliga.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Du kan också hantera programinställningar i någon av dessa andra sätt:

* [Med Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Med hjälp av den `--publish-local-settings` alternativet Publicera i Azure Functions Core Tools](functions-run-local.md#publish).
* [Med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Tools finns i avsnittet vanliga frågor i den [Visual Studio 2017-verktyg för Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogginlägg.

Läs mer om Azure Functions Core Tools i [kod och testa Azure functions lokalt](functions-run-local.md).

Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använda attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
