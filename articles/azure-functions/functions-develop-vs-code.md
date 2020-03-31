---
title: Utveckla Azure-funktioner med hjälp av Visual Studio-kod
description: Lär dig hur du utvecklar och testar Azure-funktioner med hjälp av Azure Functions-tillägget för Visual Studio-kod.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277172"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Utveckla Azure-funktioner med hjälp av Visual Studio-kod

Azure [Functions-tillägget för Visual Studio-kod] gör att du kan utveckla funktioner lokalt och distribuera dem till Azure. Om den här upplevelsen är din första med Azure Functions kan du läsa mer på [En introduktion till Azure Functions](functions-overview.md).

Azure Functions-tillägget ger följande fördelar:

* Redigera, skapa och kör funktioner på den lokala utvecklingsdatorn.
* Publicera ditt Azure Functions-projekt direkt till Azure.
* Skriv dina funktioner på olika språk samtidigt som du drar nytta av fördelarna med Visual Studio Code.

Tillägget kan användas med följande språk, som stöds av Azure Functions-körningen som börjar med version 2.x:

* [C# kompilerat](functions-dotnet-class-library.md)
* [C# skript](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Kräver att du [anger C#-skriptet som standardprojektspråk](#c-script-projects).

I den här artikeln är exempel för närvarande endast tillgängliga för JavaScript-biblioteksfunktioner (Node.js) och C#.  

Den här artikeln innehåller information om hur du använder Azure Functions-tillägget för att utveckla funktioner och publicera dem i Azure. Innan du läser den här artikeln bör du [skapa din första funktion med hjälp av Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Blanda inte lokal utveckling och portalutveckling för en enda funktionsapp. När du publicerar från ett lokalt projekt till en funktionsapp skriver distributionsprocessen över alla funktioner som du har utvecklat i portalen.

## <a name="prerequisites"></a>Krav

Innan du installerar och kör Azure [Functions-tillägget][Azure Functions för Visual Studio Code]måste du uppfylla följande krav:

* [Visual Studio-kod](https://code.visualstudio.com/) installerad på en av plattformarna [som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andra resurser som du behöver, till exempel ett Azure-lagringskonto, skapas i din prenumeration när du [publicerar med Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Du kan utveckla funktioner lokalt och publicera dem till Azure utan att behöva starta och köra dem lokalt. Om du vill köra dina funktioner lokalt måste du uppfylla några ytterligare krav, inklusive en automatisk hämtning av Azure Functions Core Tools. Mer information finns i [Ytterligare krav för att köra ett projekt lokalt](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med tillägg till Functions kan du skapa ett funktionsappprojekt tillsammans med din första funktion. Följande steg visar hur du skapar en HTTP-utlöst funktion i ett nytt Functions-projekt. [HTTP-utlösare](functions-bindings-http-webhook.md) är den enklaste funktionsutlösaren mall att demonstrera.

1. Från **Azure: Funktioner**väljer du ikonen **Skapa funktion:**

    ![Skapa en funktion](./media/functions-develop-vs-code/create-function.png)

1. Välj mappen för funktionsappprojektet och välj sedan **ett språk för funktionsprojektet**.

1. Om du inte redan har installerat core tools uppmanas du att **välja en version** av de kärnverktyg som ska installeras. Välj version 2.x eller en senare version. 

1. Välj **HTTP-utlösarfunktionsmallen** eller så kan du välja **Hoppa över för tillfället för** att skapa ett projekt utan funktion. Du kan alltid [lägga till en funktion i projektet](#add-a-function-to-your-project) senare.

    ![Välj mallen HTTP-utlösare](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Skriv **HttpExample** för funktionsnamnet och välj Retur och välj sedan **Funktionsauktorisering.** Den här auktoriseringsnivån kräver att du anger en [funktionsnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) när du anropar funktionsslutpunkten.

    ![Välj funktionsauktorisering](./media/functions-develop-vs-code/create-function-auth.png)

    En funktion skapas på ditt valda språk och i mallen för en HTTP-utlöst funktion.

    ![HTTP-utlöst funktionsmall i Visual Studio-kod](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Genererade projektfiler

Projektmallen skapar ett projekt på ditt valda språk och installerar nödvändiga beroenden. För alla språk har det nya projektet följande filer:

* **host.json**: Låter dig konfigurera functions-värden. Dessa inställningar gäller när du kör funktioner lokalt och när du kör dem i Azure. Mer information finns i [host.json reference](functions-host-json.md).

* **local.settings.json**: Upprätthåller inställningar som används när du kör funktioner lokalt. Dessa inställningar används bara när du kör funktioner lokalt. Mer information finns i [Filen Lokala inställningar](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom filen local.settings.json kan innehålla hemligheter måste du utesluta den från projektkällkontrollen.

Beroende på ditt språk skapas dessa andra filer:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs klassbiblioteksfil](functions-dotnet-class-library.md#functions-class-library-project) som implementerar funktionen.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* En package.json-fil i rotmappen.

* En HttpExample-mapp som innehåller [definitionsfilen function.json](functions-reference-node.md#folder-structure) och [filen index.js](functions-reference-node.md#exporting-a-function), en nod.js-fil som innehåller funktionskoden.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Nu kan du lägga till in- och utdatabindningar i funktionen genom [att ändra filen function.json](#add-a-function-to-your-project) eller genom [att lägga till en parameter i en C#-klassbiblioteksfunktion](#add-a-function-to-your-project).

Du kan också [lägga till en ny funktion i projektet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installera bindningstillägg

Med undantag för HTTP- och timerutlösare implementeras bindningar i tilläggspaket. Du måste installera tilläggspaketen för utlösare och bindningar som behöver dem. Processen för att installera bindningstillägg beror på projektets språk.

# <a name="c"></a>[C\#](#tab/csharp)

Kör [kommandot dotnet add package](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att installera de tilläggspaket som du behöver i projektet. Följande kommando installerar Azure Storage-tillägget, som implementerar bindningar för Blob- kö och Tabelllagring.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Lägga till en funktion i projektet

Du kan lägga till en ny funktion i ett befintligt projekt med hjälp av en av de fördefinierade funktionsutlösaremallarna. Om du vill lägga till en ny funktionsutlösare väljer du F1 för att öppna kommandopaletten och söker sedan efter och kör kommandot **Azure Functions: Skapa funktion**. Följ anvisningarna för att välja din utlösartyp och definiera de attribut som krävs för utlösaren. Om utlösaren kräver en åtkomstnyckel eller anslutningssträng för att ansluta till en tjänst gör du den klar innan du skapar funktionsutlösaren.

Resultatet av den här åtgärden beror på projektets språk:

# <a name="c"></a>[C\#](#tab/csharp)

En ny C#-klassbiblioteksfil (.cs) läggs till i projektet.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

En ny mapp skapas i projektet. Mappen innehåller en ny function.json-fil och den nya JavaScript-kodfilen.

---

## <a name="add-input-and-output-bindings"></a>Lägga till in- och utdatabindningar

Du kan utöka funktionen genom att lägga till in- och utdatabindningar. Processen för att lägga till bindningar beror på projektets språk. Mer information om bindningar finns i [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)begrepp .

Följande exempel ansluter till en `outqueue`lagringskö med namnet , där `MyStorageConnection` anslutningssträngen för lagringskontot anges i programinställningen i local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Uppdatera funktionsmetoden för att lägga `Run` till följande parameter i metoddefinitionen:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Den här koden kräver `using` att du lägger till följande uttryck:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Parametern `msg` är `ICollector<T>` en typ som representerar en samling meddelanden som skrivs till en utdatabindning när funktionen är klar. Du lägger till ett eller flera meddelanden i samlingen. Dessa meddelanden skickas till kön när funktionen är klar.

Mer information finns i dokumentationen [för kölagringsutdatabindning.](functions-bindings-storage-queue-output.md)

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Med Visual Studio Code kan du lägga till bindningar i filen function.json genom att följa en praktisk uppsättning uppmaningar. Om du vill skapa en bindning högerklickar du (Ctrl+klicka på macOS) **filen function.json** i funktionsmappen och väljer **Lägg till bindning:**

![Lägga till en bindning i en befintlig JavaScript-funktion ](media/functions-develop-vs-code/function-add-binding.png)

Följande är exempel på uppmaningar att definiera en ny lagringsutdatabindning:

| Uppmaning | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindningsriktning** | `out` | Bindningen är en utdatabindning. |
| **Välj bindning med riktning** | `Azure Queue Storage` | Bindningen är en Azure Storage-köbindning. |
| **Namnet som används för att identifiera den här bindningen i koden** | `msg` | Namn som identifierar bindningsparametern som refereras i koden. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *könamnet* inte finns skapar bindningen den vid första användningen. |
| **Välj inställning från "local.settings.json"** | `MyStorageConnection` | Namnet på en programinställning som innehåller anslutningssträngen för lagringskontot. Inställningen `AzureWebJobsStorage` innehåller anslutningssträngen för det lagringskonto som du skapade med funktionsappen. |

I det här exemplet läggs följande `bindings` bindning till i matrisen i filen function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Du kan också lägga till samma bindningsdefinition direkt i din function.json.

I funktionskoden `msg` nås bindningen från `context`, som i det här exemplet:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Mer information finns i referensen [för kölagringsbindning.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicera till Azure

Med Visual Studio Code kan du publicera ditt Functions-projekt direkt till Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

När du publicerar från Visual Studio-kod till en ny funktionsapp i Azure erbjuds du både en snabbfunktionsapp skapa sökväg och en avancerad sökväg. 

När du publicerar från Visual Studio Code kan du dra nytta av [zip-driftsättningstekniken.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Skapa snabbfunktionsapp

När du väljer **+ Skapa ny funktionsapp i Azure...** genererar tillägget automatiskt värden för De Azure-resurser som behövs av din funktionsapp. Dessa värden baseras på funktionsappnamnet som du väljer. Ett exempel på hur du använder standardvärden för att publicera projektet i en ny funktionsapp i Azure finns i [snabbstartsartikeln för Visual Studio-kod](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Om du vill ange explicita namn för de skapade resurserna måste du välja den avancerade skapa sökvägen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publicera ett projekt till en ny funktionsapp i Azure med hjälp av avancerade alternativ

Följande steg publicerar projektet i en ny funktionsapp som skapats med avancerade skapandealternativ:

1. I området **Azure: Functions** väljer du ikonen **Distribuera till funktionsapp.**

    ![Funktionsappinställningar](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Om du inte är inloggad uppmanas du att **logga in på Azure**. Du kan också **skapa ett kostnadsfritt Azure-konto**. När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code.

1. Om du har flera prenumerationer **väljer du en prenumeration** för funktionsappen och väljer sedan + Skapa ny **funktionsapp i Azure... _Avancerad_**. Det här _avancerade_ alternativet ger dig mer kontroll över de resurser du skapar i Azure. 

1. Ange följande information genom att följa anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj funktionsapp i Azure | Skapa en ny funktionsapp i Azure | Vid nästa fråga skriver du ett globalt unikt namn som identifierar din nya funktionsapp och väljer sedan Retur. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`. |
    | Välj ett operativsystem | Windows | Funktionsappen körs i Windows. |
    | Välj en värdplan | Förbrukningsplan | En serverlös [förbrukningsplan värd](functions-scale.md#consumption-plan) används. |
    | Välj en körning för din nya app | Ditt projektspråk | Körningen måste matcha projektet som du publicerar. |
    | Välj en resursgrupp för nya resurser | Skapa ny resursgrupp | Vid nästa prompt skriver du ett `myResourceGroup`resursgruppnamn, till exempel , och väljer sedan retur. Du kan också välja en befintlig resursgrupp. |
    | Välj ett lagringskonto | Skapa ett nytt lagringskonto | Vid nästa fråga skriver du ett globalt unikt namn för det nya lagringskontot som används av funktionsappen och väljer sedan Retur. Lagringskontonamnen måste vara mellan 3 och 24 tecken långa och får bara innehålla siffror och gemener. Du kan också välja ett befintligt konto. |
    | Välj en plats för nya resurser | regionen | Välj en plats i en [region](https://azure.microsoft.com/regions/) nära dig eller i närheten av andra tjänster som dina funktioner har åtkomst till. |

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet tillämpas. Välj **Visa utdata** i det här meddelandet om du vill visa skapande- och distributionsresultaten, inklusive De Azure-resurser som du skapade.

## <a name="republish-project-files"></a>Publicera om projektfiler

När du konfigurerar [kontinuerlig distribution](functions-continuous-deployment.md)uppdateras funktionsappen i Azure när källfiler uppdateras på den anslutna källplatsen. Vi rekommenderar kontinuerlig distribution, men du kan också publicera om projektfilsuppdateringarna från Visual Studio Code.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

1. Öppna kommandopaletten i Visual Studio-kod. Sök efter och välj Azure **Functions: Distribuera**för att fungera i kommandopaletten.

1. Om du inte är inloggad uppmanas du att **logga in på Azure**. När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code. Om du har flera prenumerationer **väljer du en prenumeration** som innehåller din funktionsapp.

1. Välj din befintliga funktionsapp i Azure. När du varnas för att skriva över alla filer i funktionsappen väljer du **Distribuera** för att bekräfta varningen och fortsätta.

Projektet återskapas, paketeras om och överförs till Azure. Det befintliga projektet ersätts av det nya paketet och funktionsappen startas om.

## <a name="get-the-url-of-the-deployed-function"></a>Hämta URL:en för den distribuerade funktionen

Om du vill anropa en HTTP-utlöst funktion behöver du url:en för funktionen när den distribueras till din funktionsapp. Den här URL:en innehåller alla [nödvändiga funktionstangenter](functions-bindings-http-webhook-trigger.md#authorization-keys). Du kan använda tillägget för att hämta dessa webbadresser för dina distribuerade funktioner.

1. Välj F1 för att öppna kommandopaletten och sök sedan efter och kör kommandot **Azure Functions: Copy Function URL**.

1. Följ anvisningarna för att välja din funktionsapp i Azure och sedan den specifika HTTP-utlösare som du vill anropa.

Funktions-URL:en kopieras till Urklipp, tillsammans med `code` eventuella nödvändiga nycklar som skickas av frågeparametern. Använd ett HTTP-verktyg för att skicka POST-begäranden eller en webbläsare för GET-begäranden till fjärrfunktionen.  

## <a name="run-functions-locally"></a>Kör funktioner lokalt

Med Azure Functions-tillägget kan du köra ett Functions-projekt på din lokala utvecklingsdator. Den lokala körningen är samma körning som är värd för din funktionsapp i Azure. Lokala inställningar läses från [filen local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Ytterligare krav för att driva ett projekt lokalt

Om du vill köra ditt Functions-projekt lokalt måste du uppfylla dessa ytterligare krav:

* Installera version 2.x eller senare av [Azure Functions Core Tools](functions-run-local.md#v2). Core Tools-paketet hämtas och installeras automatiskt när du startar projektet lokalt. Core Tools innehåller hela Azure Functions runtime, så nedladdning och installation kan ta lite tid.

* Installera de specifika kraven för ditt valda språk:

    | Språk | Krav |
    | -------- | --------- |
    | **C#** | [C# förlängning](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI-verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Felsökare för Java-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 eller senare](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) rekommenderas|

    <sup>*</sup>Aktiva LTS- och underhålls-LTS-versioner (8.11.1 och 10.14.1 rekommenderas).

### <a name="configure-the-project-to-run-locally"></a>Konfigurera projektet så att det körs lokalt

Funktionskörningen använder ett Azure Storage-konto internt för alla utlösartyper än HTTP och webhooks. Så du måste ange **nyckeln Values.AzureWebJobsStorage** till en giltig Azure Storage-anslutningssträng.

I det här avsnittet används [Azure Storage-tillägget för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) med [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till och hämta lagringsanslutningssträngen.

Så här anger du anslutningssträngen för lagringskonto:

1. Öppna **Cloud Explorer**i Visual Studio, expandera **Lagringskonto** > **ditt lagringskonto**och välj sedan **Egenskaper** och kopiera värdet för den **primära anslutningssträngen.**

2. Öppna filen local.settings.json i projektet och ange värdet för **AzureWebJobsStorage-nyckeln** till den anslutningssträng som du kopierade.

3. Upprepa föregående steg om du vill lägga till unika nycklar i matrisen **Värden** för alla andra anslutningar som krävs av dina funktioner.

Mer information finns i [Filen Lokala inställningar](#local-settings-file).

### <a name="debugging-functions-locally"></a>Felsökningsfunktioner lokalt  

Om du vill felsöka dina funktioner väljer du F5. Om du inte redan har hämtat [Core Tools]Azure Functions[Core Tools]uppmanas du att göra det. När Core Tools är installerat och körs visas utdata i terminalen. Detta är samma sak `func host start` som att köra kommandot Core Tools från terminalen, men med ytterligare bygguppgifter och en bifogad felsökning.  

När projektet körs kan du utlösa dina funktioner på samma sätt som när projektet distribueras till Azure. När projektet körs i felsökningsläge träffas brytpunkter i Visual Studio-kod, som förväntat.

Url:en för begäran för HTTP-utlösare visas i utdata i terminalen. Funktionstangenter för HTTP-utlösare används inte när ett projekt körs lokalt. Mer information finns i [Strategier för testning av din kod i Azure Functions](functions-test-a-function.md).  

Mer information finns i [Arbeta med Azure Functions Core Tools]Azure Functions Core[Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras inte dessa inställningar automatiskt när projektet publiceras på Azure. När publiceringen är klar får du möjlighet att publicera inställningar från local.settings.json till din funktionsapp i Azure. Mer information finns i [Publicera programinställningar](#publish-application-settings).

Värden i **ConnectionStrings publiceras** aldrig.

Funktionsinställningarnas värden kan också läsas i koden som miljövariabler. Mer information finns i avsnitten Miljövariabler i dessa språkspecifika referensartiklar:

* [C# förkompilerad](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Programinställningar i Azure

Inställningarna i filen local.settings.json i projektet ska vara desamma som programinställningarna i funktionsappen i Azure. Alla inställningar som du lägger till i local.settings.json måste också läggas till i funktionsappen i Azure. De här inställningarna överförs inte automatiskt när du publicerar projektet. På samma sätt måste alla inställningar som du skapar i funktionsappen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) hämtas till ditt lokala projekt.

### <a name="publish-application-settings"></a>Publicera programinställningar

Det enklaste sättet att publicera de nödvändiga inställningarna till din funktionsapp i Azure är att använda länken **Ladda upp inställningar** som visas när du har publicerat projektet:

![Ladda upp programinställningar](./media/functions-develop-vs-code/upload-app-settings.png)

Du kan också publicera inställningar med kommandot **Azure Functions: Upload Local Setting** i kommandopaletten. Du kan lägga till enskilda inställningar i programinställningar i Azure med kommandot **Azure Functions: Add New Setting.**

> [!TIP]
> Var noga med att spara filen local.settings.json innan du publicerar den.

Om den lokala filen krypteras dekrypteras, publiceras och krypteras den igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du ska gå vidare.

Visa befintliga appinställningar i området **Azure: Functions** genom att utöka prenumerationen, funktionsappen och **programinställningarna**.

![Visa funktionsappinställningar i Visual Studio-kod](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Hämta inställningar från Azure

Om du har skapat programinställningar i Azure kan du hämta dem till filen local.settings.json med kommandot **Azure Functions: Download Remote Settings.**

Precis som med uppladdning, om den lokala filen är krypterad, är den dekrypterad, uppdaterad och krypterad igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du ska gå vidare.

## <a name="monitoring-functions"></a>Övervakningsfunktioner

När du [kör funktioner lokalt](#run-functions-locally)strömmas loggdata till Terminal-konsolen. Du kan också hämta loggdata när ditt Functions-projekt körs i en funktionsapp i Azure. Du kan antingen ansluta till strömmande loggar i Azure för att se loggdata i nära realtid, eller så kan du aktivera Application Insights för en mer fullständig förståelse för hur din funktionsapp beter sig.

### <a name="streaming-logs"></a>Strömmande loggar

När du utvecklar ett program är det ofta användbart att se loggningsinformation i nära realtid. Du kan visa en ström av loggfiler som genereras av dina funktioner. Den här utdata är ett exempel på direktuppspelningsloggar för en begäran till en HTTP-utlöst funktion:

![Utdata för direktuppspelning loggar ut för HTTP-utlösare](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Mer information finns i [Strömmande loggar](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Strömmande loggar stöder endast en enda instans av functions-värden. När din funktion skalas till flera instanser visas inte data från andra instanser i loggströmmen. [Live Metrics Stream](../azure-monitor/app/live-stream.md) i Application Insights stöder flera instanser. Streaminganalysen är också i nästan realtid baserat på [exempeldata.](functions-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Vi rekommenderar att du övervakar körningen av dina funktioner genom att integrera din funktionsapp med Application Insights. När du skapar en funktionsapp i Azure-portalen sker den här integreringen som standard. När du skapar din funktionsapp under Visual Studio-publicering måste du själv integrera Application Insights.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md).

## <a name="c-script-projects"></a>C-skriptprojekt\#

Som standard skapas alla C#-projekt som [C# kompilerade klassbiblioteksprojekt](functions-dotnet-class-library.md). Om du föredrar att arbeta med C#-skriptprojekt i stället måste du välja C#-skript som standardspråk i inställningarna för Azure Functions-tillägg:

1. Välj**Inställningar** **för filinställningar** > **Preferences** > .

1. Gå till**Extensions** > **Azure-funktioner** **för tillägg för användarinställningar** > .

1. Välj **C#Script** från **Azure-funktionen: Projektspråk**.

När du har slutfört de här stegen innehåller `--csx` anrop till de underliggande kärnverktygen alternativet, som genererar och publicerar C#-skript (.csx) projektfiler. När du har angett det här standardspråket kan alla projekt som du skapar som standard skapa C#-skriptprojekt. Du uppmanas inte att välja ett projektspråk när en standard är inställd. Om du vill skapa projekt på andra språk måste du ändra den här inställningen eller ta bort den från användarinställningsfilen.json. När du har tagit bort den här inställningen uppmanas du återigen att välja språk när du skapar ett projekt.

## <a name="command-palette-reference"></a>Referens för kommandopalett

Azure Functions-tillägget ger ett användbart grafiskt gränssnitt i området för att interagera med dina funktionsappar i Azure. Samma funktioner är också tillgängliga som kommandon i kommandopaletten (F1). Dessa Azure Functions-kommandon är tillgängliga:

|Kommandot Azure Functions  | Beskrivning  |
|---------|---------|
|**Lägg till nya inställningar**  |  Skapar en ny programinställning i Azure. Mer information finns i [Publicera programinställningar](#publish-application-settings). Du kan också behöva [hämta den här inställningen till dina lokala inställningar](#download-settings-from-azure). |
| **Konfigurera distributionskälla** | Ansluter din funktionsapp i Azure till en lokal Git-databas. Mer information finns i [Kontinuerlig distribution för Azure-funktioner](functions-continuous-deployment.md). |
| **Ansluta till GitHub-databasen** | Ansluter din funktionsapp till en GitHub-databas. |
| **URL för kopieringsfunktion** | Hämtar fjärr-URL:en för en HTTP-utlöst funktion som körs i Azure. Mer information finns [i Hämta URL:en för den distribuerade funktionen](#get-the-url-of-the-deployed-function). |
| **Skapa funktionsapp i Azure** | Skapar en ny funktionsapp i din prenumeration i Azure. Mer information finns i avsnittet om hur du [publicerar till en ny funktionsapp i Azure](#publish-to-azure).        |
| **Dekryptera inställningar** | Dekrypterar [lokala inställningar](#local-settings-file) som har krypterats av Azure **Functions: Kryptera inställningar**.  |
| **Appen Ta bort funktion** | Tar bort en funktionsapp från din prenumeration i Azure. När det inte finns några andra appar i App Service-planen får du möjlighet att ta bort det också. Andra resurser, som lagringskonton och resursgrupper, tas inte bort. Om du vill ta bort alla resurser bör du i stället [ta bort resursgruppen](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Ditt lokala projekt påverkas inte. |
|**Ta bort funktion**  | Tar bort en befintlig funktion från en funktionsapp i Azure. Eftersom den här borttagningen inte påverkar det lokala projektet bör du i stället överväga att ta bort funktionen lokalt och sedan [publicera om projektet.](#republish-project-files) |
| **Ta bort proxy** | Tar bort en Azure Functions-proxy från din funktionsapp i Azure. Mer information om proxyservrar finns i [Arbeta med Azure Functions Proxies](functions-proxies.md). |
| **Ta bort inställning** | Tar bort en funktionsappinställning i Azure. Den här borttagningen påverkar inte inställningarna i filen local.settings.json. |
| **Koppla från Repo**  | Tar bort den [kontinuerliga distributionsanslutningen](functions-continuous-deployment.md) mellan en funktionsapp i Azure och en källkontrolldatabas. |
| **Hämta fjärrinställningar** | Hämtar inställningar från den valda funktionsappen i Azure till filen local.settings.json. Om den lokala filen är krypterad dekrypteras, uppdateras och krypteras den igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du ska gå vidare. Var noga med att spara ändringar i filen local.settings.json innan du kör det här kommandot. |
| **Redigera inställningar** | Ändrar värdet för en befintlig funktionsappinställning i Azure. Det här kommandot påverkar inte inställningarna i filen local.settings.json.  |
| **Kryptera inställningar** | Krypterar enskilda objekt `Values` i matrisen i de [lokala inställningarna](#local-settings-file). I den `IsEncrypted` här filen `true`är också inställd på , som anger att den lokala körningen kommer att dekryptera inställningar innan du använder dem. Kryptera lokala inställningar för att minska risken för läckande värdefull information. I Azure lagras alltid programinställningar krypterade. |
| **Kör funktion nu** | Startar manuellt en [timerutlöst funktion](functions-bindings-timer.md) i Azure. Det här kommandot används för testning. Mer information om hur du utlöser icke-HTTP-funktioner i Azure finns i [Manuellt köra en icke HTTP-utlöst funktion](functions-manually-run-non-http.md). |
| **Initiera projekt för användning med VS-kod** | Lägger till de nödvändiga Visual Studio Code-projektfilerna i ett befintligt Functions-projekt. Använd det här kommandot om du vill arbeta med ett projekt som du har skapat med hjälp av Core Tools. |
| **Installera eller uppdatera Azure Functions Core Tools** | Installerar eller uppdaterar [Azure Functions Core Tools], som används för att köra funktioner lokalt. |
| **Omdistribuera**  | Gör att du kan distribuera om projektfiler från en ansluten Git-databas till en viss distribution i Azure. Om du vill publicera om lokala uppdateringar från Visual Studio Code [publicerar du om projektet](#republish-project-files). |
| **Byt namn på inställningar** | Ändrar nyckelnamnet för en befintlig funktionsappinställning i Azure. Det här kommandot påverkar inte inställningarna i filen local.settings.json. När du har bytt namn på inställningarna i Azure bör du [hämta ändringarna till det lokala projektet](#download-settings-from-azure). |
| **Starta** | Startar om funktionsappen i Azure. Distribuera uppdateringar startar också funktionen app. |
| **Ange AzureWebJobsStorage**| Anger värdet för `AzureWebJobsStorage` programinställningen. Den här inställningen krävs av Azure Functions. Den ställs in när en funktionsapp skapas i Azure. |
| **Start** | Startar en stoppad funktionsapp i Azure. |
| **Starta strömmande loggar** | Startar strömningsloggarna för funktionsappen i Azure. Använd strömmande loggar under fjärrfelsökning i Azure om du behöver se loggningsinformation i nära realtid. Mer information finns i [Strömmande loggar](#streaming-logs). |
| **Stopp** | Stoppar en funktionsapp som körs i Azure. |
| **Stoppa strömmande loggar** | Stoppar strömmande loggar för funktionsappen i Azure. |
| **Växla som platsinställning** | När den är aktiverad säkerställer du att en programinställning kvarstår för en viss distributionsplats. |
| **Avinstallera Azure Functions core-verktyg** | Tar bort Azure Functions Core Tools, vilket krävs av tillägget. |
| **Ladda upp lokala inställningar** | Överför inställningar från filen local.settings.json till den valda funktionsappen i Azure. Om den lokala filen är krypterad dekrypteras, laddas upp och krypteras den igen. Om det finns inställningar som har motstridiga värden på de två platserna uppmanas du att välja hur du ska gå vidare. Var noga med att spara ändringar i filen local.settings.json innan du kör det här kommandot. |
| **Visa commit i GitHub** | Visar det senaste åtagandet i en viss distribution när funktionsappen är ansluten till en databas. |
| **Visa distributionsloggar** | Visar loggarna för en specifik distribution till funktionsappen i Azure. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [Arbeta med Azure Functions Core Tools](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klassbibliotek finns i [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md). Den här artikeln innehåller också länkar till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.

[Azure Functions-tillägg för Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Grundläggande verktyg för Azure-funktioner]: functions-run-local.md
