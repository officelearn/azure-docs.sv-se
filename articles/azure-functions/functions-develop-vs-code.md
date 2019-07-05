---
title: Utveckla Azure-funktioner med hjälp av Visual Studio Code | Microsoft Docs
description: Lär dig mer om att utveckla och testa Azure Functions med hjälp av Azure Functions-tillägget för Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452701"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Utveckla Azure-funktioner med hjälp av Visual Studio Code

Den [Azure Functions-tillägg för Visual Studio Code] kan du utveckla och distribuera funktioner till Azure lokalt. Om det här är din första med Azure Functions, kan du läsa mer på [en introduktion till Azure Functions](functions-overview.md).

Azure Functions-tillägget ger följande fördelar: 

* Redigera, skapa och köra functions på din lokala utvecklingsdator. 
* Publicera projektet Azure Functions direkt till Azure. 
* Skriv dina funktioner på olika språk samtidigt som du har alla fördelarna med Visual Studio Code. 

Tillägget kan användas med följande språk som stöds av Azure Functions version 2.x-körningen: 

* [C#kompileras](functions-dotnet-class-library.md) 
* [C#-skript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Kräver att du som du [ange C# skript som standardspråk för projektet](#c-script-projects).

I den här artikeln exempel är för närvarande endast tillgängliga för JavaScript (Node.js) och C# klassen biblioteksfunktionerna.  

Den här artikeln innehåller information om hur du använder Azure Functions-tillägget för att utveckla funktioner och publicera dem till Azure. Innan du läser den här artikeln bör du [skapa din första funktion med Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Blanda inte lokal utveckling med portalen utvecklingen i samma funktionsapp. När du publicerar från ett lokalt projekt till en funktionsapp skriver distributionsprocessen över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Förutsättningar

Innan du installerar och kör den [Azure Functions-tillägget][azure functions-tillägg för visual studio code], måste du uppfylla följande krav:

* [Visual Studio Code](https://code.visualstudio.com/) installerad på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure Storage-konto skapas i din prenumeration när du [publicera med hjälp av Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Du kan utveckla funktioner lokalt och publicera till Azure utan att behöva starta och kör dem lokalt. Det finns ytterligare krav för att köra dina funktionella lokalt, inklusive en automatisk nedladdning av Azure Functions Core Tools. Mer information finns i [ytterligare krav för att köra lokalt](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Functions-tillägget kan du skapa ett funktionsapprojekt, tillsammans med din första funktion. Följande steg visar hur du skapar en HTTP-utlöst funktion i ett nytt funktionsprojekt. [HTTP-utlösare](functions-bindings-http-webhook.md) är den enklaste funktionen utlösarmallen för att visa.

1. I **Azure: Functions** väljer du ikonen Skapa funktion.

    ![Skapa en funktion](./media/functions-develop-vs-code/create-function.png)

1. Välj mapp för ditt funktionsappsprojekt och sedan **Välj ett språk för ditt funktionsprojekt**. 

1. Välj den **HTTP-utlösare** funktionsmallen eller om du kan välja att **hoppa över** att skapa ett projekt utan en funktion. Du kan alltid [Lägg till en funktion i projektet](#add-a-function-to-your-project) vid ett senare tillfälle. 

    ![Välj mallen HTTP-utlösare](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Typ `HTTPTrigger` för funktionens namn och tryck på RETUR, Välj **funktionen** auktorisering. Den här åtkomstnivån måste du ange en [funktionsnyckel](functions-bindings-http-webhook.md#authorization-keys) när du anropar funktionen slutpunkten.

    ![Välj funktionen-autentisering](./media/functions-develop-vs-code/create-function-auth.png)

    En funktion skapas på ditt valda språk med hjälp av mallen för en HTTP-utlöst funktion.

    ![Mall för HTTP-utlöst funktion i Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Projektmallen skapar ett projekt i ditt valda språk, installerar nödvändiga beroenden. För alla språk har det nya projektet följande filer:

* **host.json**: Kan du konfigurera Functions-värden. Dessa inställningar gäller både när du kör lokalt och i Azure. Mer information finns i [referens för host.json](functions-host-json.md).

* **local.settings.json**: Sparar inställningarna som används när du kör funktioner lokalt. De här inställningarna används bara när du kör lokalt. Mer information finns i [lokala inställningsfilen](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom filen local.settings.json kan innehålla hemligheter, måste den undantas från ditt projekt källkontroll.

Nu kan du lägga till indata och utdatabindningar till din funktion genom [ändra filen function.json](#javascript-2), eller genom [att lägga till en parameter till en C# klassen library fungerar](#c-class-library-2).

Du kan också [Lägg till en ny funktion i projektet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installera bindningstillägg

Undantag för HTTP och Timer utlösare implementeras bindningar i tilläggspaket. Du måste installera tilläggspaket för utlösare och bindningar som kräver att användaren. Hur du installerar tillägg av bindning beror på ditt projektspråk.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# -klassbiblioteket

Kör den [dotnet lägga till paketet](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att installera tilläggspaket som du behöver i ditt projekt. I följande exempel installeras tillägget Azure Storage som implementerar bindningar för Blob, Queue och Table storage.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

Du kan lägga till en ny funktion i ett befintligt projekt genom att använda någon av de fördefinierade mallarna för Functions-utlösare. Om du vill lägga till en ny funktion-utlösare, tryck på F1 att öppna kommandopaletten, och sedan söka efter och kör kommandot **Azure Functions: Skapa funktion...** . Följ anvisningarna för att välja din Utlösartyp av och definiera attributen som krävs för utlösaren. Om utlösaren kräver en åtkomst åtkomstnyckel eller anslutningssträng för att ansluta till en tjänst, Förbered det innan du skapar utlösare för funktionen. 

Resultatet av den här åtgärden beror på ditt projektspråk:

### <a name="javascript"></a>JavaScript

En ny mapp skapas i projektet, som innehåller en ny fil i function.json och den nya filen för JavaScript-kod.

### <a name="c-class-library"></a>C\# -klassbiblioteket

En ny C# klass (.cs) fil läggs till i ditt projekt.

## <a name="add-input-and-output-bindings"></a>Lägg till indata och utdata bindningar

Du kan expandera din funktion genom att lägga till bindningar för indata och utdata. Hur du gör detta beror på ditt projektspråk. Mer information om bindningar finns [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md). 

I följande exempel ansluter till en lagringskö med namnet `outqueue`, där anslutningssträngen för lagringskontot har angetts i den `MyStorageConnection` inställningen i local.settings.json. 

### <a name="javascript"></a>JavaScript

Visual Studio Code kan du lägga till bindningar i function.json-filen genom att följa en lämplig uppsättning prompter. Att skapa en bindning, högerklicka på (Ctrl + klicka på macOS) den `function.json` filen i mappen funktionen och välj **Lägg till bindning...** . 

![Lägga till en bindning till en befintlig JavaScript-funktion ](media/functions-develop-vs-code/function-add-binding.png)

Följande är exempel anvisningarna för att definiera en ny storage-utdatabindning:

| Uppmaning | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindning riktning** | `out` | Bindningen är en utdatabindning. |
| **Välj bindning med riktning...** | `Azure Queue Storage` | Bindningen är en bindning för Azure Storage-kö. |
| **Namnet används för att identifiera den här bindningen i koden** | `msg` | Namn som identifierar bindningsparametern som refereras till i din kod. |
| **Kön som meddelandet ska skickas** | `outqueue` | Namnet på kön som bindningen skriver till. När den *queueName* inte finns några bindningen skapar den första användningen. |
| **Välj inställningen från ”local.setting.json”** | `MyStorageConnection` | Namnet på en programinställning med anslutningssträngen för lagringskontot. Den `AzureWebJobsStorage` inställningen innehåller anslutningssträngen för lagringskontot som du skapade med appen. |

I det här exemplet läggs följande bindningen till den `bindings` matris i function.json filen:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Du kan också lägga till samma Bindningsdefinitionen direkt till din function.json.

I Funktionskoden, den `msg` bindning nås från den `context`, som i följande exempel:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Mer information finns i den [Queue storage-utdatabindning](functions-bindings-storage-queue.md#output---javascript-example) referens.

### <a name="c-class-library"></a>C\# -klassbiblioteket

Uppdatera metoden funktionen för att lägga till följande parameter till den `Run` metoddefinitionen:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Den här koden måste du lägga till följande `using` instruktionen:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Den `msg` parametern är en `ICollector<T>` typ som representerar en samling av meddelanden som skrivs till en utdatabindning när funktionen har slutförts. Du lägga till ett eller flera meddelanden till samlingen som skickas till kön när funktionen har slutförts.

Mer information finns i den [Queue storage-utdatabindning](functions-bindings-storage-queue.md#output---c-example) referens.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publicera till Azure

I Visual Studio Code kan du publicera funktionsprojektet direkt på Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

När du publicerar från Visual Studio Code, används en av två metoder för distribution:

* [ZIP-distribuera med aktiverad kör-från-Package](functions-deployment-technologies.md#zip-deploy): används för de flesta Azure Functions-distributioner.
* [Externt paket URL](functions-deployment-technologies.md#external-package-url): används för distribution till Linux-appar på en [förbrukningsplan](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Skapa appar för snabb funktion

Som standard genererar Visual Studio Code automatiskt värden för de Azure resurser som krävs av funktionsappen. Dessa värden är baserade på funktionsappens namn du väljer. Ett exempel på hur du använder standard publicerar projektet till en ny funktionsapp i Azure finns i den [snabbstartsartikel för Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Om du vill ange explicit namn för de skapade resurserna, måste du aktivera publicering med avancerade alternativ.

### <a name="enabled-publishing-with-advanced-create-options"></a>Aktiverade publicering med avancerade alternativ för att skapa

För att ge dig kontroll över inställningarna som är associerade med skapa Azure Functions appar, uppdatera tillägg för Azure Functions för att aktivera avancerade inställningar.

1. Klicka på **fil > Inställningar > Inställningar**

1. Navigera genom **användarinställningar > Tillägg > Azure Functions**

1. Markera kryssrutan för **Azure-funktion: Skapa en avancerad**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publicera till en ny funktionsapp i Azure med avancerade skapas

Följande steg publicera ditt projekt till en ny funktionsapp som skapats med hjälp av avancerade alternativ för att skapa.

1. I den **Azure: Functions** området, Välj för att distribuera till Funktionsapp-ikon.

    ![Funktionen programinställningar](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Om inte loggat in, uppmanas du att **logga in på Azure**. Du kan också **skapa ett kostnadsfritt konto**. Efter lyckad inloggning i webbläsaren, gå tillbaka till Visual Studio Code.

1. Om du har flera prenumerationer **Välj en prenumeration** för funktionsappen, välj sedan **+ Skapa ny Funktionsapp i Azure**.

1. Ange följande information följer sedan anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj funktionsapp i Azure | + Skapa ny Funktionsapp i Azure | Ange ett globalt unikt namn som identifierar din nya funktionsapp i nästa meddelande, och tryck på RETUR. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj ett operativsystem | Windows | Funktionsappen som körs på Windows |
    | Välj en värdplan | Förbrukningsplan | Serverlös [värd för Förbrukningsplan](functions-scale.md#consumption-plan) används. |
    | Välj en körning för din nya app | Ditt projektspråk | Körningen måste matcha det projekt som du publicerar. |
    | Välj en resursgrupp för nya resurser | Skapa ny resursgrupp | I nästa meddelande du skriver ett Resursgruppsnamn som `myResourceGroup`, och tryck på RETUR. Du kan också välja en befintlig resursgrupp. |
    | Välj ett lagringskonto | Skapa ett nytt lagringskonto | I nästa meddelande, typ ett globalt unikt namn för det nya lagringskontot som används av din funktionsapp och tryck på RETUR. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också välja ett befintligt konto. |
    | Välj en plats för nya resurser | region | Välj en plats i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som används av dina funktioner. |

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **visa utdata** i det här meddelandet att visa skapande och distributionsresultat, inklusive Azure-resurser som du skapade.

## <a name="republish-project-files"></a>Publicera projektfiler

När du ställer in [kontinuerlig distribution](functions-continuous-deployment.md), funktionsappen i Azure ska uppdateras varje gång källfiler uppdateras i anslutna källplatsen. Medan vi rekommenderar detta utveckling, kan du också publicera om filuppdateringar ditt projekt från Visual Studio Code. 

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten. I kommandopaletten, Sök efter och välj `Azure Functions: Deploy to function app...`.

1. Om inte loggat in, uppmanas du att **logga in på Azure**. Efter lyckad inloggning i webbläsaren, gå tillbaka till Visual Studio Code. Om du har flera prenumerationer **Välj en prenumeration** som innehåller din funktionsapp.

1. Välj din befintliga funktionsapp i Azure. När en varning om att skriva över alla filer i funktionsappen, Välj **distribuera** bekräfta varningen och fortsätta. 

Projektet är återskapas, paketerats och överfört till Azure. Befintligt projekt ersättas med det nya paketet och funktionsappen startas om.

## <a name="get-deployed-function-url"></a>Hämta distribuerade Funktionswebbadress

Om du vill kunna anropa en HTTP-utlöst funktion måste URL: en för funktionen när de distribueras till din funktionsapp. Denna URL innehåller alla obligatoriska [funktionstangenter](functions-bindings-http-webhook.md#authorization-keys). Du kan använda tillägget för att hämta dessa URL: er för dina distribuerade funktioner.

1. Tryck på F1 så öppna kommandopaletten, och sedan söka efter och kör kommandot **Azure Functions: Kopiera Funktionswebbadressen**.

1. Följ anvisningarna för att välja din funktionsapp i Azure och sedan de specifika HTTP-utlösare som du vill anropa. 

Funktionen URL: en har kopierats till Urklipp, tillsammans med eventuella obligatoriska nycklar som skickas med hjälp av den `code` frågeparameter. Använd en HTTP-verktyget för att skicka POST-förfrågningar, eller en webbläsare för GET-begäranden till fjärransluten funktionen.  

## <a name="run-functions-locally"></a>Köra funktioner lokalt

Azure Functions-tillägget kan du köra en functions-projekt på din lokala utvecklingsdator. Den lokala körningstiden är samma körning som är värd för din funktionsapp i Azure. Lokala inställningar läses från den [local.settings.json-fil](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Ytterligare krav för att köra lokalt

För att kunna köra projektet funktioner lokalt, måste du också uppfylla dessa ytterligare krav:

* Installera version 2.x av den [Azure Functions Core Tools](functions-run-local.md#v2). Core-verktygspaketet hämtas och installeras för din automatiskt när du startar projektet lokalt. De viktigaste verktygen innehåller hela Azure Functions-körningen så hämtningen och installationen kan ta lite tid.

* Installera de specifika kraven för ditt valda språk:

    | Språk | Krav |
    | -------- | --------- |
    | **C#** | [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET core CLI-verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Felsökare för Java-tillägget](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6+](https://www.python.org/downloads/)|

    <sup>*</sup>Aktiva LTS och underhåll LTS versioner (8.11.1 och 10.14.1 rekommenderas).

### <a name="configure-the-project-to-run-locally"></a>Konfigurera projektet att köras lokalt

Ett Azure Storage-konto används internt i Functions-körning för alla typer av utlösare än HTTP och webhooks. Det innebär att du måste ange den **Values.AzureWebJobsStorage** avgörande för att en giltig anslutningssträng för Azure Storage-konto.

Det här avsnittet använder de [Azure Storage-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) med [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com/) att ansluta till och hämta anslutningssträngen för lagring.   

Ange anslutningssträng för lagringskonto:

1. Öppna i Visual Studio **Cloud Explorer**, expandera **Lagringskonto** > **Your Storage Account**och välj sedan **egenskaper**och kopiera den **primär anslutningssträng** värde.

2. Öppna filen local.settings.json i projektet, och ange värdet för den **AzureWebJobsStorage** nyckeln på anslutningssträngen som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar till den **värden** matris för andra anslutningar som krävs av dina funktioner.

Mer information finns i [lokala inställningsfilen](#local-settings-file).

### <a name="debugging-functions-locally"></a>Felsöka funktioner lokalt  

Tryck på F5 för att felsöka dina funktioner. Om du inte redan har hämtat den [Kärnverktyg][Azure Functions Core Tools], du uppmanas att göra detta. När Core Tools har installerats och körs, utdata visas i terminalen. Det här är samma som att köra `func host start` Core Tools kommando från terminalen, men med ytterligare skapa uppgifter och en anslutna felsökare.  

Du kan utlösa dina funktioner precis som när de distribueras till Azure med projekt som körs. När du kör i felsökningsläge kan uppnås brytpunkter i Visual Studio Code, som förväntat.

URL: en för HTTP-utlösare visas i utdata i terminalen. Funktionstangenter för HTTP-utlösare används inte när du kör lokalt. Mer information finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md).  

Mer information finns i [arbeta med Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard dessa inställningar migreras inte automatiskt när projektet har publicerats till Azure. När publiceringen är klar tillåts du publicera inställningar från local.settings.json till din funktionsapp i Azure. Mer information finns i [publicera programinställningar](#publish-application-settings).

Värdena i **ConnectionStrings** aldrig har publicerats.

Inställningsvärden för funktionen program kan också läsa i koden som miljövariabler. Mer information finns i avsnittet miljö variabler i dessa språkspecifika referensartiklar:

* [C#-förkompilerad version](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Programinställningar i Azure

Inställningarna i filen local.settings.json i projektet ska vara samma som programinställningar i funktionsappen i Azure. Alla inställningar som du lägger till i local.settings.json måste också läggas till funktionsappen i Azure. Dessa inställningar överförs inte automatiskt när du publicerar projektet. På samma sätt kan alla inställningar som du skapar i din funktionsapp [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) måste laddas ned till ditt lokala projekt.

### <a name="publish-application-settings"></a>Publicera programinställningar

Det enklaste sättet att publicera inställningarna som krävs till funktionsappen i Azure är att använda den **Överföringsinställningar** länken som visas när du har publicerat ditt projekt.

![Distributionen har slutförts överföring programinställningar](./media/functions-develop-vs-code/upload-app-settings.png)

Du kan också publicera inställningar med hjälp av den `Azure Functions: Upload Local Setting` i kommandopaletten. Individuella inställningar har lagts till programinställningar i Azure med hjälp av den `Azure Functions: Add New Setting...` kommando. 

> [!TIP]
> Glöm inte att spara filen local.settings.json innan du publicerar den.

Det är dekryptera, publiceras och krypteras på nytt om den lokala filen är krypterad. Om inställningar finns med olika värden på båda platserna, uppmanas du att välja hur du fortsätter.

Visa befintliga appinställningar i den **Azure: Functions** området genom att expandera din prenumeration, din funktionsapp och **programinställningar**.

![Visa funktionsappsinställning i Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Hämta inställningar från Azure

Om du har skapat programinställningar i Azure kan hämta du dem i filen local.settings.json. med hjälp av den `Azure Functions: Download Remote Settings...` kommando. 

Det är precis som med överföring, om den lokala filen är krypterad, dekrypteras, uppdateras och krypteras på nytt. Om inställningar finns med olika värden på båda platserna, uppmanas du att välja hur du fortsätter.

## <a name="monitoring-functions"></a>Övervakningsfunktioner

När du [kör lokalt](#run-functions-locally), loggdata strömmas till Terminal-konsolen. Du kan också hämta loggdata när din functions-projekt körs i en funktionsapp i Azure. Du antingen ansluter till direktuppspelningsloggar i Azure för att se loggdata för nästan i realtid eller du kan aktivera Application Insights för en bättre förståelse av hur appen fungerar.

### <a name="streaming-logs"></a>Direktuppspelningsloggar

När du utvecklar ett program, kan det ofta vara användbart att visa loggningsinformation i nära realtid. Du kan visa en dataström med loggfiler som skapas av dina funktioner. Följande utdata är ett exempel på direktuppspelningsloggar för en begäran att en HTTP-funktion som utlöses:

![Direktuppspelningsloggar utdata för HTTP-utlösare](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Mer information finns i [Streaming loggar](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Direktuppspelningsloggar stöder endast en instans av Functions-värden. När din funktion skalas till flera instanser visas inte data från andra instanser i strömmen log. Den [Live Metrics Stream](../azure-monitor/app/live-stream.md) i Application Insights stöd för flera instanser. Medan även i nära realtid, strömningsanalyser även baseras på [exempeldata](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Det är det rekommenderade sättet att övervaka körning av dina funktioner genom att integrera din funktionsapp med Azure Application Insights. När du skapar en funktionsapp i Azure portal görs den här integreringen för dig som standard. Integrering i din funktionsapp i Azure är inte dock utföras när du skapar funktionsappen vid publicering av Visual Studio.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# skript projekt

Som standard alla C# projekt skapas som [ C# kompileras klass biblioteksprojekt](functions-dotnet-class-library.md). Om du i stället föredrar att arbeta med C# skriptet projekt, måste du välja C# skript som standardspråk i inställningarna för Azure Functions-tillägget.

1. Klicka på **fil > Inställningar > Inställningar**.

1. Navigera genom **användarinställningar > Tillägg > Azure Functions**.

1. Välj **C #Script** från **Azure-funktion: Projektet språk**.

Nu anrop som görs till de underliggande Kärnverktyg inkluderar den `--csx` alternativ, vilket genererar och publicerar C# skript (.csx) projektfiler. Med ett standardspråk anges, skapas alla projekt som standard C# skript projekt. Du uppmanas inte att välja ett projektspråk när ett standardvärde har angetts. Om du vill skapa andra språk-projekt, måste du ändra den här inställningen eller ta bort den från filen Settings.JSON för användaren. När du har tagit bort den här inställningen uppmanas du igen att välja språk när du skapar ett projekt.

## <a name="command-palette-reference"></a>Kommandoreferens för färgpalett

Azure Functions-tillägget tillhandahåller ett praktiskt grafiskt gränssnitt i området Azure för att interagera med dina funktionsappar i Azure. Samma funktion är också tillgängligt som kommandona i kommandopaletten (F1). Följande kommandon för Azure Functions-specifika är tillgängliga:

|Azure Functions-kommando  | Beskrivning  |
|---------|---------|
|**Lägg till nya inställningar...**  |  Skapar en ny programinställning i Azure. Mer information finns i [publicera programinställningar](#publish-application-settings). Du kan också behöva [ladda ned den här inställningen till dina lokala inställningar](#download-settings-from-azure). |
| **Konfigurera distributionskälla...** | Anslut din funktionsapp i Azure till en lokal Git-lagringsplats. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). |
| **Anslut till GitHub-lagringsplatsen...** | Anslut din funktionsapp en GitHub-lagringsplats. |
| **Kopiera funktions-URL** | Hämtar remote URL: en för en HTTP-utlöst funktion som körs i Azure. Mer information finns i så här [hämta URL: en för distribuerade funktionen](#get-deployed-function-url). |
| **Skapa en funktionsapp i Azure...** | Skapar en ny funktionsapp i din prenumeration i Azure. Mer information finns i så här [publicera till en ny funktionsapp i Azure](#publish-to-azure).        |
| **Dekryptera inställningar** | Använda för att dekryptera [lokala inställningar](#local-settings-file) som har krypterats med hjälp av `Azure Functions: Encrypt Settings`.  |
| **Ta bort Funktionsappen...** | Tar bort en befintlig funktionsapp från din prenumeration i Azure. När det finns inga andra appar i App Service-plan, får du alternativet Ta bort den också. Andra resurser, till exempel lagringskonton och resursgrupper, tas inte bort. Om du vill ta bort alla resurser bör du i stället [ta bort resursgruppen](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Projektet lokala påverkas inte. |
|**Ta bort funktionen...**  | Tar bort en befintlig funktion från en funktionsapp i Azure. Eftersom borttagningen inte påverkar projektet lokalt, i stället bör du ta bort funktionen lokalt och sedan [publicera projektet](#republish-project-files). |
| **Ta bort Proxy...** | Tar bort en Azure Functions-proxy från din funktionsapp i Azure. Läs mer om proxyservrar i [arbeta med Azure Functions Proxies](functions-proxies.md). |
| **Ta bort inställning...** | Tar bort en programinställning för funktionen i Azure. Påverkar inte inställningarna i filen local.settings.json. |
| **Koppla från lagringsplatsen...**  | Ta bort den [kontinuerlig distribution](functions-continuous-deployment.md) anslutning mellan en funktionsapp i Azure och ett källkontrollscentrallager. |
| **Hämta Fjärrinställningar...** | Hämtar inställningar från den valda funktionsappen i Azure i filen local.settings.json. Om den lokala filen är krypterad, är det dekrypteras, uppdateras och krypteras på nytt. Om inställningar finns med olika värden på båda platserna, uppmanas du att välja hur du fortsätter. Kontrollera att du har sparat ändringarna i filen local.settings.json innan du kör det här kommandot. |
| **Redigera inställningar...** | Ändrar värdet för en befintlig funktion programinställning i Azure. Påverkar inte inställningarna i filen local.settings.json.  |
| **Kryptera inställningar** | Krypterar enskilda objekt i den `Values` matrisen i den [lokala inställningar](#local-settings-file). I den här filen `IsEncrypted` anges också `true`, som anger den lokala körningen för att dekryptera inställningar innan du använder dem. Kryptera lokala inställningar för att minska risken för läcker värdefull information. I Azure krypterat program-inställningar lagras alltid. |
| **Kör funktionen nu** | Startar en [timer-funktion som utlöses](functions-bindings-timer.md) i Azure manuellt för testning. Mer information om hur du aktiverar icke-HTTP-funktioner i Azure finns [manuellt köra en icke HTTP-utlöst funktion](functions-manually-run-non-http.md). |
| **Starta projektet för användning med VS Code...** | Lägger till de nödvändiga filerna för Visual Studio Code-projekt till ett befintligt Functions-projekt. Använd kommandot för att arbeta med ett projekt som du skapade med Core Tools. |
| **Installationen av uppdateringen Azure Functions Core Tools** | Installerar eller uppdaterar den [Azure Functions Core Tools] som används för att köras lokalt. |
| **Distribuera om**  | Kan du distribuera om projektfilerna från en ansluten Git-lagringsplats till en specifik distribution i Azure. Att publicera lokala uppdateringar från Visual Studio Code [publicera projektet](#republish-project-files). |
| **Byt namn på inställningar...** | Ändrar nyckelnamnet för en befintlig funktion programinställning i Azure. Påverkar inte inställningarna i filen local.settings.json. När du byter namn på inställningar i Azure, bör du [dessa ändringar ska hämtas till det lokala projektet](#download-settings-from-azure). |
| **Starta om** | Startar om funktionsappen i Azure. Distribuera uppdateringar startar också om funktionsappen. |
| **Ange AzureWebJobStorage...**| Anger värdet för den `AzureWebJobStorage` programinställningen. Den här inställningen krävs av Azure functions och anges när funktionsappen har skapats i Azure. |
| **Börja** | Startar en stoppad funktionsapp i Azure. | 
| **Starta Direktuppspelningsloggar** | Startar strömningsloggar för funktionsappen i Azure. Använda direktuppspelningsloggarna under fjärrfelsökning i Azure om du vill se den här informationen i nära realtid. Mer information finns i [Streaming loggar](#streaming-logs). |
| **Stanna** | Stängs ned en funktionsapp som körs i Azure. |
| **Stoppa Direktuppspelningsloggar** | Stoppar strömningsloggar för funktionsappen i Azure. |
| **Visa/Dölj som platsinställning** | När aktiverad, ser du till att en programinställning kvarstår för en given distributionsplats. |
| **Avinstallera Azure Functions Core Tools** | Tar bort Azure Functions Core Tools, vilket krävs av tillägget. |
| **Ladda upp lokala inställningar...** | Överför inställningar från filen local.settings.json till den valda funktionsappen i Azure. Det är dekryptera, överförs och krypteras på nytt om den lokala filen är krypterad. Om inställningar finns med olika värden på båda platserna, uppmanas du att välja hur du fortsätter. Kontrollera att du har sparat ändringarna i filen local.settings.json innan du kör det här kommandot. |
| **Visa Commit i GitHub** | Visar den senaste incheckningen i en viss distribution när funktionsappen är ansluten till en databas. |
| **Visa Distributionsloggar** | Visar loggar för en specifik distribution till funktionsappen i Azure. |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions Core Tools i [kod och testa Azure functions lokalt](functions-run-local.md).

Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använda attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    

[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md