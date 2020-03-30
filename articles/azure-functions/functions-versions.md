---
title: Översikt över Azure Functions runtime-versioner
description: Azure Functions stöder flera versioner av körningen. Lär dig skillnaderna mellan dem och hur du väljer den som är rätt för dig.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276561"
---
# <a name="azure-functions-runtime-versions-overview"></a>Översikt över Azure Functions runtime-versioner

Huvudversionerna av Azure Functions-körningen är relaterade till den version av .NET som körningen baseras på. I följande tabell visas den aktuella versionen av körningen, utgivningsnivån och den relaterade .NET-versionen. 

| Runtime-version | Släpp nivå<sup>1</sup> | .NET-version | 
| --------------- | ------------- | ------------ |
| 3.x | Allmän tillgänglighet (GA) | .NET-kärna 3.1 | 
| 2.x | Allmän tillgänglighet (GA) | .NET Core 2.2 |
| 1.x | GA<sup>2 (PÅ ANDRA)</sup> | .NET-ramverket 4,6<sup>3</sup> |

<sup>1</sup> GA-versioner stöds för produktionsscenarier.   
<sup>2</sup> Version 1.x är i underhållsläge. Förbättringar tillhandahålls endast i senare versioner.   
<sup>3</sup> Stöder endast utveckling i Azure-portalen eller lokalt på Windows-datorer.

I den här artikeln beskrivs några av skillnaderna mellan de olika versionerna, hur du kan skapa varje version och hur du ändrar versioner.

## <a name="languages"></a>Språk

Från och med version 2.x använder körningen en språkutökningsmodell och alla funktioner i en funktionsapp måste ha samma språk. Språket för funktioner i en funktionsapp väljs när appen skapas och underhålls i inställningen [FUNCTIONS\_WORKER\_RUNTIME.](functions-app-settings.md#functions_worker_runtime) 

Azure Functions 1.x experimentella språk kan inte använda den nya modellen, så de stöds inte i 2.x. I följande tabell visas vilka programmeringsspråk som för närvarande stöds i varje körningsversion.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Köra på en viss version

Som standard är funktionsappar som skapats i Azure-portalen och av Azure CLI inställda på version 3.x. Du kan ändra den här versionen efter behov. Du kan bara ändra körningsversionen till 1.x när du har skapat funktionsappen men innan du lägger till några funktioner.  Det är tillåtet att flytta mellan 2.x och 3.x även med appar som har funktioner, men det rekommenderas fortfarande att testa i en ny app först.

## <a name="migrating-from-1x-to-later-versions"></a>Migrera från 1.x till senare versioner

Du kan välja att migrera en befintlig app som skrivits för att använda version 1.x-körningen för att i stället använda en nyare version. De flesta av de ändringar du behöver göra är relaterade till ändringar i språkkörningen, till exempel C#API-ändringar mellan .NET Framework 4.7 och .NET Core. Du måste också se till att koden och biblioteken är kompatibla med den språkkörning du väljer. Slutligen, se till att notera eventuella ändringar i utlösare, bindningar och funktioner som markeras nedan. För bästa migreringsresultat bör du skapa en ny funktionsapp i en ny version och portera din befintliga version 1.x-funktionskod till den nya appen.  

Även om det är möjligt att göra en "på plats" uppgradering genom att manuellt uppdatera appkonfigurationen, innehåller går från 1.x till en högre version några bryta ändringar. I C#ändras till exempel felsökningsobjektet `TraceWriter` `ILogger`från till . Genom att skapa ett nytt version 3.x-projekt börjar du med uppdaterade funktioner baserade på de senaste version 3.x-mallarna.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Ändringar i utlösare och bindningar efter version 1.x

Från och med version 2.x måste du installera tilläggen för specifika utlösare och bindningar som används av funktionerna i appen. Det enda undantaget för den här HTTP- och timerutlösaren, som inte kräver ett tillägg.  Mer information finns i [Registrera och installera bindningstillägg](./functions-bindings-register.md).

Det finns också några ändringar i *funktionen.json* eller attribut för funktionen mellan versioner. Egenskapen Event Hub `path` är `eventHubName`till exempel nu . Se den [befintliga bindningstabellen](#bindings) för länkar till dokumentation för varje bindning.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Ändringar i funktioner och funktioner efter version 1.x

Några funktioner har tagits bort, uppdaterats eller ersatts efter version 1.x. I det här avsnittet beskrivs de ändringar som visas i senare versioner efter att ha använt version 1.x.

I version 2.x gjordes följande ändringar:

* Nycklar för att anropa HTTP-slutpunkter lagras alltid krypterade i Azure Blob-lagring. I version 1.x lagrades nycklar som standard i Azure File Storage. När du uppgraderar en app från version 1.x till version 2.x återställs befintliga hemligheter som finns i fillagringen.

* Versionskörningen 2.x innehåller inte inbyggt stöd för webhook-leverantörer. Denna förändring gjordes för att förbättra prestanda. Du kan fortfarande använda HTTP-utlösare som slutpunkter för webhooks.

* Värdkonfigurationsfilen (host.json) ska vara tom `"version": "2.0"`eller ha strängen .

* För att förbättra övervakningen ersätts WebJobs-instrumentpanelen i portalen, som använde [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) inställningen, med Azure Application Insights, som använder [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) inställningen. Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md).

* Alla funktioner i en funktionsapp måste ha samma språk. När du skapar en funktionsapp måste du välja en körningsstack för appen. Körningsstacken anges av [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) värdet i programinställningarna. Det här kravet har lagts till för att förbättra fotavtryck och starttid. När du utvecklar lokalt måste du också inkludera den här inställningen i [filen local.settings.json](functions-run-local.md#local-settings-file).

* Standardtidsgränsen för funktioner i en App Service-plan ändras till 30 minuter. Du kan manuellt ändra tidsgränsen tillbaka till obegränsad med hjälp av [inställningen functionTimeout](functions-host-json.md#functiontimeout) i host.json.

* HTTP-samtidighetsbegränsningar implementeras som standard för förbrukningsplanfunktioner, med en standard på 100 samtidiga begäranden per instans. Du kan ändra [`maxConcurrentRequests`](functions-host-json.md#http) detta i inställningen i filen host.json.

* På grund av [.NET Core-begränsningar](https://github.com/Azure/azure-functions-host/issues/3414)har stöd för F#-skriptfunktioner (.fsx) tagits bort. Kompilerade F#-funktioner (.fs) stöds fortfarande.

* URL-formatet för Händelserutnätsutlösare `https://{app}/runtime/webhooks/{triggerName}`har ändrats till .

## <a name="migrating-from-2x-to-3x"></a>Migrera från 2.x till 3.x

Azure Functions version 3.x är mycket bakåtkompatibel med version 2.x.  Många appar bör kunna uppgradera till 3.x på ett säkert sätt utan några kodändringar.  När du flyttar till 3.x uppmuntras, se till att köra omfattande tester innan du ändrar huvudversionen i produktionsappar.

### <a name="breaking-changes-between-2x-and-3x"></a>Bryta ändringar mellan 2.x och 3.x

Följande är de ändringar som ska vara medvetna om innan du uppgraderar en 2.x-app till 3.x.

#### <a name="javascript"></a>JavaScript

* Utdatabindningar `context.done` som tilldelas genom eller returnerar `context.bindings`värden fungerar nu på samma sätt som inställningen i .

* Timer trigger objekt är camelCase istället för PascalCase

* Event Hub utlösta `dataType` funktioner med binära kommer att få en matris `binary` i stället för `string`.

* Nyttolasten för HTTP-begäran kan `context.bindingData.req`inte längre nås via .  Den kan fortfarande nås som en `context.req`indataparameter och i `context.bindings`.

* Node.js 8 stöds inte längre och körs inte i 3.x-funktioner.

#### <a name="net"></a>.NET

* [Synkron serveråtgärder är inaktiverade som standard](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Ändra version av appar i Azure

Den version av funktionskörningen som används av publicerade [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) appar i Azure styrs av programinställningen. Följande huvudvärden för körningsversion stöds:

| Värde | Körningsmål |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Ändra inte den här inställningen godtyckligt eftersom det kan krävas ändringar och ändringar i funktionskoden i andra appinställningar och ändringar i funktionskoden.

### <a name="locally-developed-application-versions"></a>Lokalt utvecklade programversioner

Du kan göra följande uppdateringar för att fungera appar för att lokalt ändra riktade versioner.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio-körningsversioner

I Visual Studio väljer du körningsversionen när du skapar ett projekt. Azure Functions-verktyg för Visual Studio stöder de tre huvudkörningsversionerna. Rätt version används vid felsökning och publicering baserat på projektinställningar. Versionsinställningarna definieras i `.csproj` filen i följande egenskaper:

##### <a name="version-1x"></a>Version 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Version 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Version 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x och `Microsoft.NET.Sdk.Functions` .NET kräver `3.0.0`att tillägget ska vara minst .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Uppdatera 2.x-appar till 3.x i Visual Studio

Du kan öppna en befintlig funktion som är inriktad på 2.x och gå till 3.x genom att redigera `.csproj` filen och uppdatera värdena ovan.  Visual Studio hanterar körningsversioner automatiskt för dig baserat på projektmetadata.  Det är dock möjligt om du aldrig har skapat en 3.x-app innan Visual Studio ännu inte har mallarna och körningen för 3.x på datorn.  Detta kan ge sig ett fel som "inga tillgängliga funktioner som matchar den version som anges i projektet".  Om du vill hämta de senaste mallarna och körningen går du igenom upplevelsen för att skapa ett nytt funktionsprojekt.  När du kommer till skärmen för versions- och mallval väntar du tills Visual Studio har hämtat de senaste mallarna.  När de senaste .NET Core 3-mallarna är tillgängliga och visade bör du kunna köra och felsöka alla projekt som konfigurerats för version 3.x.

> [!IMPORTANT]
> Version 3.x-funktioner kan endast utvecklas i Visual Studio om du använder Visual Studio version 16.4 eller nyare.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Vs-kod och Azure Functions kärnverktyg

[Azure Functions Core Tools](functions-run-local.md) används för utveckling av kommandorader och även av [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio-kod. Om du vill utveckla mot version 3.x installerar du version 3.x av Core Tools. Version 2.x utveckling kräver version 2.x av Core Tools, och så vidare. Mer information finns [i Installera Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

För utveckling av Visual Studio-kod kan du också `azureFunctions.projectRuntime` behöva uppdatera användarinställningen för att matcha versionen av de installerade verktygen.  Den här inställningen uppdaterar också de mallar och språk som används när funktionsappen skapades.  Om du `~3` vill skapa `azureFunctions.projectRuntime` appar i `~3`uppdaterar du användarinställningen till .

![Körningsinställning för Azure Functions-tillägg](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven- och Java-appar

Du kan migrera Java-appar från version 2.x till 3.x genom att [installera 3.x-versionen av de kärnverktyg](functions-run-local.md#install-the-azure-functions-core-tools) som krävs för att köras lokalt.  När du har kontrollerat att appen fungerar korrekt när den körs lokalt `POM.xml` på version `FUNCTIONS_EXTENSION_VERSION` 3.x uppdaterar du appens fil så att inställningen ändrars till `~3`, som i följande exempel:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Bindningar

Från och med version 2.x använder körningen en ny [bindningsutökningsmodell](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som erbjuder följande fördelar:

* Stöd för bindningstillägg från tredje part.

* Frikoppling av körtid och bindningar. Den här ändringen gör att bindningstillägg kan versionsas och släppas oberoende av dem. Du kan till exempel välja att uppgradera till en version av ett tillägg som är beroende av en nyare version av ett underliggande SDK.

* En lättare körningsmiljö, där endast bindningar som används är kända och inlästa av körningen.

Med undantag för HTTP- och timerutlösare måste alla bindningar uttryckligen läggas till i funktionsappprojektet eller registreras i portalen. Mer information finns i [Registrera bindningstillägg](./functions-bindings-expressions-patterns.md).

I följande tabell visas vilka bindningar som stöds i varje körningsversion.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Så här inriktar du dig på Azure Functions runtime-versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)
