---
title: Utveckla Azure Functions med hjälp av Visual Studio
description: Lär dig hur du utvecklar och testar Azure-funktioner med hjälp av Azure Functions Tools för Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277107"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure Functions med hjälp av Visual Studio  

Med Visual Studio kan du utveckla, testa och distribuera C#-klassbiblioteksfunktioner till Azure. Om den här upplevelsen är din första med Azure Functions kan du läsa mer på [En introduktion till Azure Functions](functions-overview.md).

Visual Studio ger följande fördelar när du utvecklar dina funktioner: 

* Redigera, skapa och kör funktioner på den lokala utvecklingsdatorn. 
* Publicera ditt Azure Functions-projekt direkt till Azure och skapa Azure-resurser efter behov. 
* Använd C#-attribut för att deklarera funktionsbindningar direkt i C#-koden.
* Utveckla och distribuera förkompilerade C#-funktioner. Förfördordnade funktioner ger bättre kallstartsprestanda än C# skriptbaserade funktioner. 
* Koda dina funktioner i C# samtidigt som du har alla fördelar med Visual Studio-utveckling. 

Den här artikeln innehåller information om hur du använder Visual Studio för att utveckla C#-klassbiblioteksfunktioner och publicera dem i Azure. Innan du läser den här artikeln bör du slutföra [snabbstarten Funktioner för Visual Studio](functions-create-your-first-function-visual-studio.md). 

Om inget annat anges gäller procedurer och exempel för Visual Studio 2019. 

## <a name="prerequisites"></a>Krav

Azure Functions Tools ingår i Azure-utvecklingsarbetsbelastningen i Visual Studio som börjar med Visual Studio 2017. Se till att du inkluderar **Azure-utvecklingsarbetsbelastningen** i din Visual Studio-installation.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage-konto, skapas i din prenumeration under publiceringsprocessen.

> [!NOTE]
> I Visual Studio 2017 installerar Azure-utvecklingsarbetsbelastningen Azure Functions Tools som ett separat tillägg. När du uppdaterar Visual Studio 2017 ska du också se till att du använder den [senaste versionen](#check-your-tools-version) av Azure Functions-verktygen. I följande avsnitt visas hur du kontrollerar och (om det behövs) uppdatera ditt Azure Functions Tools-tillägg i Visual Studio 2017. 
>
> Hoppa över det här avsnittet när du använder Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Kontrollera din verktygsversion i Visual Studio 2017

1. Välj Tillägg och **uppdateringar**på **Verktyg-menyn** . Expandera **installerade** > **verktyg** och välj **Azure-funktioner och webbjobbverktyg**.

    ![Verifiera versionen av funktionsverktyg](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Observera den installerade **versionen**. Du kan jämföra den här versionen med den senaste versionen som anges [i viktig information](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Om din version är äldre uppdaterar du verktygen i Visual Studio enligt följande avsnitt.

### <a name="update-your-tools-in-visual-studio-2017"></a>Uppdatera dina verktyg i Visual Studio 2017

1. I dialogrutan **Tillägg och uppdateringar** expanderar du **Uppdaterar** > **Visual Studio Marketplace**, väljer Azure Functions och **Webbjobb verktyg** och väljer **Uppdatera**.

    ![Uppdatera versionen av funktionsverktyg](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. När verktygsuppdateringen har hämtats stänger du Visual Studio för att utlösa verktygsuppdateringen med VSIX-installationsprogrammet.

1. I installationsprogrammet väljer du **OK** för att starta och **sedan Ändra** för att uppdatera verktygen. 

1. När uppdateringen är klar väljer du **Stäng** och startar om Visual Studio.

> [!NOTE]  
> I Visual Studio 2019 och senare uppdateras azure functions-verktygstillägget som en del av Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Projektmallen skapar ett C#-projekt, `Microsoft.NET.Sdk.Functions` installerar NuGet-paketet och anger målramverket. Det nya projektet har följande filer:

* **host.json**: Låter dig konfigurera functions-värden. Dessa inställningar gäller både när du kör lokalt och i Azure. Mer information finns i [host.json reference](functions-host-json.md).

* **local.settings.json**: Upprätthåller inställningar som används när funktioner körs lokalt. Dessa inställningar används inte när du kör i Azure. Mer information finns i [Filen Lokala inställningar](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom filen local.settings.json kan innehålla hemligheter måste du utesluta den från projektkällkontrollen. Inställningen **Kopiera till utdatakatalog** för den här filen ska alltid vara **Kopiera om den är nyare**. 

Mer information finns i [Projekt för Functions-klassbibliotek](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Inställningarna i local.settings.json överförs inte automatiskt när du publicerar projektet. Om du vill vara säker på att dessa inställningar även finns i funktionsappen i Azure måste du ladda upp dem när du har publicerat projektet. Mer information finns i [Funktionsappinställningar](#function-app-settings).

Värden i **ConnectionStrings publiceras** aldrig.

Värdena för funktionsappinställningar kan också läsas i koden som miljövariabler. Mer information finns i [Miljövariabler](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Funktionskörningen använder ett Azure Storage-konto internt. För alla andra utlösartyper än HTTP och webhooks måste du ange nyckeln **Values.AzureWebJobsStorage** till en giltig Azure Storage-kontoanslutningssträng. Din funktionsapp kan också använda [Azure-lagringsemulatorn](../storage/common/storage-use-emulator.md) för anslutningsinställningen **AzureWebJobsStorage** som krävs av projektet. Om du vill använda emulatorn anger du värdet `UseDevelopmentStorage=true`för **AzureWebJobsStorage** till . Ändra den här inställningen till en verklig anslutningssträng för lagringskonto före distributionen.

Så här anger du anslutningssträngen för lagringskonto:

1. Öppna **Cloud Explorer**i Visual Studio och expandera **Lagringskonto** > **ditt lagringskonto**och kopiera sedan värdet för den **primära anslutningssträngen** på fliken **Egenskaper.**

2. Öppna filen local.settings.json i projektet och ange värdet för **AzureWebJobsStorage-nyckeln** till den anslutningssträng som du kopierade.

3. Upprepa föregående steg om du vill lägga till unika nycklar i matrisen **Värden** för alla andra anslutningar som krävs av dina funktioner. 

## <a name="add-a-function-to-your-project"></a>Lägga till en funktion i projektet

I C#-klassbiblioteksfunktioner definieras bindningarna som används av funktionen genom att använda attribut i koden. När du skapar funktionsutlösare från de angivna mallarna används utlösarattributen åt dig. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure-funktion,** skriv ett **namn** för klassen och klicka på **Lägg till**.

2. Välj utlösaren, ange bindningsegenskaper och klicka på **Skapa**. I följande exempel visas inställningarna när du skapar en funktion för kölagring som utlösts. 

    ![Skapa en köutlöst funktion](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    I det här utlösarexemplet används en anslutningssträng med en nyckel med namnet **QueueStorage**. Den här anslutningsstränginställningen måste definieras i [filen local.settings.json](functions-run-local.md#local-settings-file).

3. Undersök den nyligen tillagda klassen. Du ser en statisk **körningsmetod** som tillskrivs med **attributet FunctionName.** Det här attributet anger att metoden är startpunkten för funktionen.

    Följande klass C# representerar till exempel en grundläggande kölagringsutlöst funktion:

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

    Ett bindningsspecifikt attribut tillämpas på varje bindningsparameter som anges på startpunktsmetoden. Attributet tar bindningsinformationen som parametrar. I föregående exempel har den första parametern ett **QueueTrigger-attribut** tillämpat, vilket anger köutlöst funktion. Könamnet och namnet på anslutningssträngen skickas som parametrar till **attributet QueueTrigger.** Mer information finns i [Azure Queue storage bindings för Azure Functions](functions-bindings-storage-queue-trigger.md).

Du kan använda ovanstående procedur för att lägga till fler funktioner i funktionsappprojektet. Varje funktion i projektet kan ha en annan utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Lägga till bindningar

Precis som med utlösare läggs in- och utdatabindningar till i din funktion som bindningsattribut. Lägg till bindningar i en funktion enligt följande:

1. Kontrollera att du har [konfigurerat projektet för lokal utveckling](#configure-the-project-for-local-development).

2. Lägg till lämpligt NuGet-tilläggspaket för den specifika bindningen. Mer information finns i [Lokal C#-utveckling med hjälp av Visual Studio](./functions-bindings-register.md#local-csharp) i artikeln Triggers and Bindings. De bindningsspecifika NuGet-paketkraven finns i referensartikeln för bindningen. Hitta till exempel paketkrav för utlösaren eventhubbar i [referensartikeln för händelsehubbar.](functions-bindings-event-hubs.md)

3. Om det finns appinställningar som bindningen behöver lägger du till dem i samlingen **Värden** i den [lokala inställningsfilen](functions-run-local.md#local-settings-file). Dessa värden används när funktionen körs lokalt. När funktionen körs i funktionsappen i Azure används [funktionsappinställningarna.](#function-app-settings)

4. Lägg till lämpligt bindningsattribut i metodsignaturen. I följande exempel utlöser ett kömeddelande funktionen och utdatabindningen skapar ett nytt kömeddelande med samma text i en annan kö.

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
   Anslutningen till kölagring hämtas `AzureWebJobsStorage` från inställningen. Mer information finns i referensartikeln för den specifika bindningen. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.

Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

När projektet körs kan du testa koden på samma sätt som du skulle testa distribuerad funktion. Mer information finns i [Strategier för testning av din kod i Azure Functions](functions-test-a-function.md). När du kör i felsökningsläge träffas brytpunkter i Visual Studio som förväntat. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Mer information om hur du använder Azure Functions Core Tools finns i [Kod och testa Azure-funktioner lokalt](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicera till Azure

När du publicerar från Visual Studio används en av två distributionsmetoder:

* [Webb distribuera:](functions-deployment-technologies.md#web-deploy-msdeploy)paket och distribuerar Windows-appar till alla IIS-servrar.
* [Zip-distribution med Run-From-Package aktiverat](functions-deployment-technologies.md#zip-deploy): rekommenderas för Azure Functions-distributioner.

Följ följande steg för att publicera projektet till en funktionsapp i Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Alla inställningar som du har lagt till i local.settings.json måste också läggas till i funktionsappen i Azure. De här inställningarna överförs inte automatiskt när du publicerar projektet.

Det enklaste sättet att ladda upp de nödvändiga inställningarna till din funktionsapp i Azure är att använda länken **Hantera programinställningar...** som visas när du har publicerat projektet.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Då visas dialogrutan **Programinställningar** för funktionsappen, där du kan lägga till nya programinställningar eller ändra befintliga.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokal** representerar ett inställningsvärde i filen local.settings.json och **Remote** är den aktuella inställningen i funktionsappen i Azure.  Välj **Lägg till inställning** om du vill skapa en ny appinställning. Använd **värdet Infoga från Lokal** länk för att kopiera ett **inställningsvärde** till fältet Fjärr. Väntande ändringar skrivs till den lokala inställningsfilen och funktionsappen när du väljer **OK**.

> [!NOTE]
> Som standard är filen local.settings.json inte incheckad i källkontrollen. Det innebär att när du klonar ett lokalt Functions-projekt från källkontrollen har projektet inte en local.settings.json-fil. I det här fallet måste du manuellt skapa filen local.settings.json i projektroten så att dialogrutan **Programinställningar** fungerar som förväntat. 

Du kan också hantera programinställningar på något av följande sätt:

* [Använda Azure-portalen](functions-how-to-use-azure-function-app-settings.md#settings).
* [Använda `--publish-local-settings` publiceringsalternativet i Azure Functions Core Tools](functions-run-local.md#publish).
* [Använda Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Övervakningsfunktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera din funktionsapp med Azure Application Insights. När du skapar en funktionsapp i Azure-portalen görs den här integreringen åt dig som standard. Men när du skapar din funktionsapp under Visual Studio-publicering görs inte integreringen i funktionsappen i Azure.

Så här aktiverar du Application Insights för din funktionsapp:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [Kod och testa Azure-funktioner lokalt](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klassbibliotek finns i [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md). Den här artikeln länkar också till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
