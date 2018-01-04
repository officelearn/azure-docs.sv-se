---
title: "Utveckla Azure-funktioner med hjälp av Visual Studio | Microsoft Docs"
description: "Lär dig mer om att utveckla och testa Azure Functions med hjälp av Azure Functions verktyg för Visual Studio-2017."
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: 0b7746125c9bcff64bd721f6ec5d2ea2c73f6f18
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="azure-functions-tools-for-visual-studio"></a>Azure Functions Tools för Visual Studio  

Azure Functions-verktyg för Visual Studio-2017 är ett tillägg för Visual Studio som låter dig utveckla, testa och distribuera C#-funktioner till Azure. Om det här är din första upplevelse med Azure Functions kan du läsa mer i [en introduktion till Azure Functions](functions-overview.md).

Azure Functions verktyg ger följande fördelar: 

* Redigera, skapa och köra funktioner på lokala utvecklingsdator. 
* Publicera projektet Azure Functions direkt till Azure. 
* Använda WebJobs-attribut för att deklarera funktionsbindningar direkt i C#-kod i stället för att upprätthålla en separat function.json för bindning definitioner.
* Utveckla och distribuera före kompilerade C#-funktioner. Före följt funktioner ge en bättre kall start prestanda än C# skript-baserad funktion. 
* Felkod dina funktioner i C# med alla fördelar med Visual Studio-utveckling. 

Det här avsnittet visar hur du använder Azure Functions-verktyg för Visual Studio 2017 för att utveckla dina funktioner i C#. Du också lära dig hur du publicerar projektet till Azure som en .NET-sammansättning.

> [!IMPORTANT]
> Blanda inte lokal utveckling med portalen utveckling i appen med samma funktion. När du publicerar från ett lokalt projekt till en funktionsapp distributionsprocessen skriver över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Förutsättningar

Azure Functions-verktyg ingår i Azure-utveckling arbetsbelastning [Visual Studio 2017 version 15,4](https://www.visualstudio.com/vs/), eller en senare version. Kontrollera att du inkluderar den **Azure-utveckling** arbetsbelastning i Visual Studio 2017 installationen:

![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Om du vill skapa och distribuera funktioner, måste du också:

* En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration [kostnadsfria konton](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) är tillgängliga.

* Ett Azure Storage-konto. Om du vill skapa ett lagringskonto finns [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

När du skapar ett nytt projekt med hjälp av Azure Functions-mall kan få du ett tomt C#-projekt som innehåller följande filer:

* **Host.JSON**: kan du konfigurera funktioner värden. Dessa inställningar gäller både när du kör lokalt och i Azure. Mer information finns i [host.json referens](functions-host-json.md).
    
* **Local.Settings.JSON**: underhåller inställningar som används när du kör funktioner lokalt. De här inställningarna används inte av Azure, de används av den [Azure Functions grundläggande verktyg](functions-run-local.md). Använd den här filen för att ange inställningar, till exempel anslutningssträngar till andra Azure-tjänster. Lägg till en ny nyckel till den **värden** matris för varje anslutning som krävs av funktionerna i projektet. Mer information finns i [lokala inställningsfilen](functions-run-local.md#local-settings-file) i avsnittet om Azure Functions grundläggande verktyg.

Functions-runtime används internt i ett Azure Storage-konto. För alla utlösa typer än HTTP och webhooks, måste du ange den **Values.AzureWebJobsStorage** nyckel till en giltig anslutningssträng för Azure Storage-konto.

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 Ange anslutningssträngen för lagring konto:

1. Öppna i Visual Studio **Cloud Explorer**, expandera **Lagringskonto** > **Your Lagringskonto**och välj **egenskaper**och kopiera den **primära anslutningssträngen** värde.   

2. Öppna filen local.settings.json i ditt projekt och ange värdet för den **AzureWebJobsStorage** nyckel i anslutningssträngen som du kopierade.

3. Upprepa det föregående steget för att lägga till unika nycklar till den **värden** matris för alla anslutningar som krävs av dina funktioner.  

## <a name="create-a-function"></a>Skapa en funktion

I förväg kompilerade funktioner definieras de bindningar som används av funktionen genom att använda i koden. När du använder Azure Functions-verktyg för att skapa dina funktioner från de angivna mallarna, tillämpas dessa attribut för dig. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure-funktion**, ange ett **namn** för klass och klicka på **Lägg till**.

2. Välj utlösaren, ange de bindande egenskaperna och klicka på **skapa**. I följande exempel visar inställningar när du skapar en Queue storage aktiveras funktionen. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    En anslutning strängnyckel som heter **QueueStorage** har angetts som definieras i filen local.settings.json. 
 
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

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.  

Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg.  Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

Du kan testa din kod som du vill testa distribuerade funktionen med projektet körs. Mer information finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md). När du kör i felsökningsläge nådde brytpunkter i Visual Studio som förväntat. 

Ett exempel på hur du testar en kö som utlöste funktion finns i [kön aktiveras funktionen Snabbstartsguide](functions-create-storage-queue-triggered-function.md#test-the-function).  

Mer information om hur du använder Azure Functions grundläggande verktygen finns [koden och testa Azure functions lokalt](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicera till Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>Alla inställningar som du lade till i local.settings.json måste också läggas till funktionsapp i Azure. De här inställningarna läggs inte automatiskt. Du kan lägga till nödvändiga inställningar i appen funktion i något av följande sätt:
>
>* [Med hjälp av Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
>* [Med hjälp av den `--publish-local-settings` alternativet Publicera i Azure Functions Core verktyg](functions-run-local.md#publish).
>* [Med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#set). 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions verktyg finns i avsnittet vanliga frågor för den [Visual Studio 2017 verktyg för Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) blogginlägg.

Läs mer om Azure Functions grundläggande verktyg i [koden och testa Azure functions lokalt](functions-run-local.md).  
Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns i [Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md) (Använda .NET-klassbibliotek med Azure Functions). Det här avsnittet innehåller också exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
