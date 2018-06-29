---
title: Utveckla Azure-funktioner med hjälp av Visual Studio | Microsoft Docs
description: Lär dig mer om att utveckla och testa Azure Functions med hjälp av Azure Functions verktyg för Visual Studio-2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: glenga
ms.openlocfilehash: bc280e9b4ade8a4fb8107ec2dcc1c33f538472e1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081568"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure-funktioner med hjälp av Visual Studio  

Azure Functions-verktyg för Visual Studio-2017 är ett tillägg för Visual Studio som låter dig utveckla, testa och distribuera C#-funktioner till Azure. Om det här upplevelsen är först med Azure Functions, kan du läsa mer i [en introduktion till Azure Functions](functions-overview.md).

Azure Functions verktyg ger följande fördelar: 

* Redigera, skapa och köra funktioner på lokala utvecklingsdator. 
* Publicera projektet Azure Functions direkt till Azure. 
* Använda WebJobs-attribut för att deklarera funktionsbindningar direkt i C#-kod i stället för att upprätthålla en separat function.json för bindning definitioner.
* Utveckla och distribuera före kompilerade C#-funktioner. Före följt funktioner ge en bättre kall start prestanda än C# skript-baserad funktion. 
* Felkod dina funktioner i C# med alla fördelar med Visual Studio-utveckling. 

Den här artikeln visar hur du använder Azure Functions-verktyg för Visual Studio 2017 för att utveckla dina funktioner i C#. Du också lära dig hur du publicerar projektet till Azure som en .NET-sammansättning.

> [!IMPORTANT]
> Blanda inte lokal utveckling med portalen utveckling i appen med samma funktion. När du publicerar från ett lokalt projekt till en funktionsapp distributionsprocessen skriver över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Förutsättningar

Azure Functions-verktyg ingår i Azure-utveckling arbetsbelastning [Visual Studio 2017 version 15,5](https://www.visualstudio.com/vs/), eller en senare version. Kontrollera att du inkluderar den **Azure-utveckling** arbetsbelastning i Visual Studio 2017 installationen:

![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Kontrollera att din Visual Studio är uppdaterad och att du använder den [senaste versionen](#check-your-tools-version) Azure Functions-verktyg.

### <a name="other-requirements"></a>Andra krav

Om du vill skapa och distribuera funktioner, måste du också:

* En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration [kostnadsfria konton](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) är tillgängliga.

* Ett Azure Storage-konto. Om du vill skapa ett lagringskonto finns [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Kontrollera din verktyg-version

1. Från den **verktyg** -menyn, Välj **tillägg och uppdateringar**. Expandera **installerad** > **verktyg** och välj **Azure Functions och jobb Webbverktyg**.

    ![Verifiera funktioner verktyg version](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Observera den installerade **Version**. Du kan jämföra den här versionen med den senaste versionen visas [i dokumentet](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Om din version är äldre, uppdatera din verktyg i Visual Studio som visas i följande avsnitt.

### <a name="update-your-tools"></a>Uppdatera dina verktyg

1. I den **tillägg och uppdateringar** dialogrutan Expandera **uppdateringar** > **Visual Studio Marketplace**, Välj **Azure Functions och Webbverktyg jobb**  och välj **uppdatering**.

    ![Uppdatera funktioner verktyg version](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Stäng Visual Studio att utlösa verktygen uppdatera med hjälp av installationsprogrammet VSIX när verktyg uppdateringen laddas ned.

3. I installationsprogrammet, väljer **OK** att starta och sedan **ändra** att uppdatera verktygen. 

4. När uppdateringen är klar väljer **Stäng** och starta om Visual Studio.

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Projektmallen skapar ett C#-projekt, installerar den `Microsoft.NET.Sdk.Functions` NuGet-paketet och anger Målversionen av framework. Fungerar 1.x mål .NET Framework och fungerar 2.x mål .NET Standard. Det nya projektet har följande filer:

* **Host.JSON**: kan du konfigurera funktioner värden. Dessa inställningar gäller både när du kör lokalt och i Azure. Mer information finns i [host.json referens](functions-host-json.md).

* **Local.Settings.JSON**: underhåller inställningar som används när du kör funktioner lokalt. De här inställningarna används inte av Azure, de används av den [Azure Functions grundläggande verktyg](functions-run-local.md). Använd den här filen för att ange app-inställningar för variabler som krävs av dina funktioner. Lägg till ett nytt objekt i den **värden** matris för varje anslutning krävs för funktioner bindningar i projektet. Mer information finns i [lokala inställningsfilen](functions-run-local.md#local-settings-file) i Azure Functions grundläggande verktyg artikeln.

Mer information finns i [funktioner klassbiblioteksprojektet](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Functions-runtime används internt i ett Azure Storage-konto. För alla utlösa typer än HTTP och webhooks, måste du ange den **Values.AzureWebJobsStorage** nyckel till en giltig anslutningssträng för Azure Storage-konto. Funktionen appen kan också använda den [Azure storage-emulatorn](../storage/common/storage-use-emulator.md) för den **AzureWebJobsStorage** anslutning inställningen att krävs för projektet. Om du vill använda emulatorn värdet för **AzureWebJobsStorage** till `UseDevelopmentStorage=true`. Du måste ändra den här inställningen till en anslutning för faktiska lagringsplatsen före distributionen.

Ange anslutningssträngen för lagring konto:

1. Öppna i Visual Studio **Cloud Explorer**, expandera **Lagringskonto** > **Your Lagringskonto**och välj **egenskaper**och kopiera den **primära anslutningssträngen** värde.

2. Öppna filen local.settings.json i ditt projekt och ange värdet för den **AzureWebJobsStorage** nyckel i anslutningssträngen som du kopierade.

3. Upprepa det föregående steget för att lägga till unika nycklar till den **värden** matris för alla anslutningar som krävs av dina funktioner.

## <a name="create-a-function"></a>Skapa en funktion

I förväg kompilerade funktioner definieras de bindningar som används av funktionen genom att använda i koden. När du använder Azure Functions-verktyg för att skapa dina funktioner från de angivna mallarna, tillämpas dessa attribut för dig. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure-funktion**, ange ett **namn** för klass och klicka på **Lägg till**.

2. Välj utlösaren, ange de bindande egenskaperna och klicka på **skapa**. I följande exempel visar inställningar när du skapar en Queue storage aktiveras funktionen. 

    ![Skapa en kö som utlöste-funktion](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Den här utlösaren exempel används en anslutningssträng med en nyckel som heter **QueueStorage**. Den här inställningen för anslutningssträngen måste definieras i den [local.settings.json filen](functions-run-local.md#local-settings-file).

3. Granska nyligen tillagda klassen. Du ser en statisk **kör** metod som har den **FunctionName** attribut. Det här attributet anger att metoden är startpunkten för funktionen.

    Till exempel följande C# klassen representerar en grundläggande kön utlöses lagringsfunktionen:

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
    En bindning-specifika attributet används för varje bindning-parameter som skickades till metoden. Attributet tar bindningsinformationen som parametrar. I föregående exempel är den första parametern har en **QueueTrigger** -attribut som används, som anger kön aktiveras funktionen. Könamnet och namn för anslutningssträngen inställningen överförs som parametrar till den **QueueTrigger** attribut.
    
Du kan använda proceduren ovan för att lägga till fler funktioner i projektet funktionen app. Varje funktion i projektet kan ha en annan utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Lägga till bindningar

Som utlösare, läggs inkommande och utgående bindningar till din funktion som binder attribut. Lägga till bindningar till en funktion på följande sätt:

1. Kontrollera att du har [konfigureras projektet för lokal utveckling](#configure-the-project-for-local-development).

2. Lägg till lämpligt NuGet-tillägg-paket för specifika bindningen. Mer information finns i [lokala C# utveckling med hjälp av Visual Studio](functions-triggers-bindings.md#local-csharp) i artikeln utlösare och bindningar. Bindningen-specifika NuGet-paketet krav finns i referensartikeln för bindningen. Till exempel hitta paketet krav för Händelsehubbar utlösaren i den [Händelsehubbar bindning referensartikeln](functions-bindings-event-hubs.md).

3. Om app-inställningar som bindningen måste du lägga till dem i den **värden** samling i den [lokala filen](functions-run-local.md#local-settings-file). Dessa värden används när funktionen körs lokalt. När funktionen körs i funktionsapp i Azure, den [fungerar appinställningar](#function-app-settings) används.

4. Lägga till attributet passande bindande i Metodsignaturen. I följande exempel visas ett meddelande i kön utlöser funktionen och utdata bindningen skapar ett nytt meddelande i kön med samma namn i en annan kö.

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

Du kan testa din kod som du vill testa distribuerade funktionen med projektet körs. Mer information finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md). När du kör i felsökningsläge nådde brytpunkter i Visual Studio som förväntat. 

Ett exempel på hur du testar en kö som utlöste funktion finns i [kön aktiveras funktionen Snabbstartsguide](functions-create-storage-queue-triggered-function.md#test-the-function).  

Mer information om hur du använder Azure Functions grundläggande verktygen finns [koden och testa Azure functions lokalt](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicera till Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Alla inställningar som du lade till i local.settings.json måste också läggas till funktionsapp i Azure. De här inställningarna överförs inte automatiskt när du publicerar projektet.

Det enklaste sättet att överföra nödvändiga inställningar i appen funktionen i Azure är att använda den **hantera programinställningar...**  länk som visas när du har publicerat projektet. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Visar den **programinställningar** dialogrutan för funktionsapp, där du kan lägga till nya programinställningar eller ändra befintliga.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Du kan också hantera inställningar för program på något av dessa andra sätt:

* [Med hjälp av Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Med hjälp av den `--publish-local-settings` alternativet Publicera i Azure Functions Core verktyg](functions-run-local.md#publish).
* [Med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions verktyg finns i avsnittet vanliga frågor för den [Visual Studio 2017 verktyg för Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogginlägg.

Läs mer om Azure Functions grundläggande verktyg i [koden och testa Azure functions lokalt](functions-run-local.md).

Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns [Azure Functions C#-utvecklare](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
