---
title: Översikt över Azure Functions körnings versioner
description: Azure Functions stöder flera versioner av körnings miljön. Lär dig mer om skillnaderna mellan dem och hur du väljer det som passar dig bäst.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: glenga
ms.openlocfilehash: 9ca7006bb842cbe235d2e982e611613e1fd74ed9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597395"
---
# <a name="azure-functions-runtime-versions-overview"></a>Översikt över Azure Functions körnings versioner

Huvud versionerna av Azure Functions runtime är relaterade till den version av .NET som körningen baseras på. I följande tabell visas den aktuella versionen av körnings miljön, versions nivån och den relaterade .NET-versionen. 

| Körnings version | Versions nivå<sup>1</sup> | .NET-version | 
| --------------- | ------------- | ------------ |
| 3.x  | förhandsversion | .NET Core 3. x | 
| 2x | Allmänt tillgänglig | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> GA-versioner stöds för produktions scenarier.   
<sup>2</sup> Version 1. x är i underhålls läge. Förbättringar finns bara i senare versioner.   
<sup>3</sup> Stöder endast utveckling i Azure Portal eller lokalt på Windows-datorer.

>[!NOTE]  
> Version 3. x av Functions-körningen är i för hands version och stöds inte för produktions miljöer. Mer information om hur du testar version 3. x finns i [det här meddelandet](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

I den här artikeln beskrivs några skillnader mellan olika versioner, hur du kan skapa varje version och hur du ändrar versioner.

## <a name="languages"></a>Språk

Från och med version 2. x använder körnings modellen en språk utöknings modell och alla funktioner i en Function-app måste dela samma språk. Språket i funktioner i en Function-app väljs när du skapar appen och underhålls i [\_RUNTIME inställningen funktioner \_WORKER](functions-app-settings.md#functions_worker_runtime) . 

Azure Functions 1. x experimentella språk kan inte använda den nya modellen, så de stöds inte i 2. x. Följande tabell visar vilka programmeringsspråk som för närvarande stöds i varje körnings version.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="creating-1x-apps"></a>Kör på en angiven version

Som standard är Function-appar som skapats i Azure Portal och av Azure CLI inställda på version 2. x. När det är möjligt bör du använda den här körnings versionen. Om du behöver kan du fortfarande köra en Function-app på version 1. x-körningsmiljön. Du kan bara ändra kör tids versionen när du har skapat din Function-app, men innan du lägger till några funktioner. Information om hur du fäster körnings versionen på 1. x finns i [Visa och uppdatera den aktuella körnings versionen](set-runtime-version.md#view-and-update-the-current-runtime-version).

Du kan också uppgradera till version 3. x av körnings miljön, som finns i för hands version. Gör detta om du behöver kunna köra dina funktioner på .NET Core 3. x. Information om hur du uppgraderar till 3. x finns i [Visa och uppdatera den aktuella körnings versionen](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrera från 1. x till senare versioner

Du kan välja att migrera en befintlig app som skrivits för att använda version 1. x runtime för att istället använda version 2. x. De flesta ändringar du behöver göra är relaterade till ändringar i språk körningen, till exempel C# API-ändringar mellan .NET Framework 4,7 och .net Core 2. Du måste också kontrol lera att din kod och dina bibliotek är kompatibla med den språk körning som du väljer. Slutligen är det viktigt att notera eventuella ändringar i utlösare, bindningar och funktioner som marker ATS nedan. För bästa migrerings resultat bör du skapa en ny function-app för version 2. x och port din befintliga version 1. x-funktions kod till den nya appen.  

### <a name="changes-in-triggers-and-bindings"></a>Ändringar i utlösare och bindningar

Version 2. x kräver att du installerar tillägg för vissa utlösare och bindningar som används av funktionerna i din app. Det enda undantaget för HTTP-och timer-utlösare, vilket inte kräver ett tillägg.  Mer information finns i [Registrera och installera bindnings tillägg](./functions-bindings-register.md).

Det har också uppstått några ändringar i `function.json` eller attributen för funktionen mellan versioner. Till exempel är Event Hub `path`-egenskapen nu `eventHubName`. I den [befintliga bindnings tabellen](#bindings) finns länkar till dokumentation för varje bindning.

### <a name="changes-in-features-and-functionality"></a>Ändringar i funktioner och funktioner

Några funktioner som också har tagits bort, uppdaterats eller ersatts i den nya versionen. Det här avsnittet innehåller information om de ändringar som visas i version 2. x efter att ha använt version 1. x.

I version 2. x gjordes följande ändringar:

* Nycklar för att anropa HTTP-slutpunkter lagras alltid krypterade i Azure Blob Storage. I version 1. x lagrades nycklar i Azure File Storage som standard. När du uppgraderar en app från version 1. x till version 2. x återställs befintliga hemligheter som finns i fil lagringen.

* Version 2. x-körningsmiljön innehåller inget inbyggt stöd för webhook-providrar. Den här ändringen gjordes för att förbättra prestandan. Du kan fortfarande använda HTTP-utlösare som slut punkter för Webhooks.

* Värd konfigurations filen (Host. JSON) måste vara tom eller ha strängen `"version": "2.0"`.

* För att förbättra övervakningen ersätts instrument panelen WebJobs i portalen, som använde inställningen [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) , med Azure Application insikter som använder inställningen [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) . Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

* Alla funktioner i en Function-app måste dela samma språk. När du skapar en Function-app måste du välja en körnings stack för appen. Körnings stacken anges av [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) svärdet i program inställningarna. Detta krav har lagts till för att förbättra utrymme och start tid. När du utvecklar lokalt måste du också inkludera den här inställningen i [filen Local. Settings. JSON](functions-run-local.md#local-settings-file).

* Standard tids gränsen för funktioner i en App Service plan ändras till 30 minuter. Du kan manuellt ändra tillbaka tids gränsen till obegränsad med hjälp av inställningen [functionTimeout](functions-host-json.md#functiontimeout) i Host. JSON.

* HTTP-samtidighets begränsning implementeras som standard för förbruknings plan funktioner, med standardvärdet 100 samtidiga begär Anden per instans. Du kan ändra detta i [`maxConcurrentRequests`](functions-host-json.md#http) inställningen i Host. JSON-filen.

* På grund av [begränsningar i .net Core](https://github.com/Azure/azure-functions-host/issues/3414)har F# stöd för skript funktioner (. FSX) tagits bort. Kompilerade F# funktioner (. FS) stöds fortfarande.

* URL-formatet för Event Grid trigger-webhookar har ändrats till `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrera ett lokalt utvecklat program

Du kan ha befintliga Function app-projekt som du har utvecklat lokalt med version 1. x-körningsmiljön. Om du vill uppgradera till version 2. x bör du skapa ett lokalt Function-projekt med version 2. x och port din befintliga kod i den nya appen. Du kan uppdatera det befintliga projektet och koden manuellt, en sortering av "på plats"-uppgraderingen. Det finns dock ett antal andra förbättringar mellan version 1. x och version 2. x som du kanske fortfarande måste göra. Till exempel har C# ett fel söknings objekt ändrats från `TraceWriter` till `ILogger`. Genom att skapa ett nytt version 2. x-projekt börjar du med uppdaterade funktioner baserat på de senaste version 2. x-mallarna.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versioner

I Visual Studio väljer du kör tids versionen när du skapar ett projekt. Azure Functions Tools för Visual Studio stöder både huvud körnings versioner. Rätt version används vid fel sökning och publicering baserat på projekt inställningar. Versions inställningarna definieras i `.csproj`-filen i följande egenskaper:

##### <a name="version-1x"></a>Version 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Version 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

När du felsöker eller publicerar ditt projekt används rätt version av körnings miljön.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS-kod och Azure Functions Core Tools

[Azure Functions Core tools](functions-run-local.md) används för utveckling av kommando rader och även av [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. För att utveckla mot version 2. x, installerar du version 2. x av kärn verktygen. Version 1. x-utveckling kräver version 1. x av kärn verktygen. Mer information finns i [installera Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools).

För utveckling av Visual Studio-kod kan du också behöva uppdatera användar inställningen för `azureFunctions.projectRuntime` så att den matchar versionen av de installerade verktygen.  Den här inställningen uppdaterar även de mallar och språk som används när en funktion skapas.

### <a name="changing-version-of-apps-in-azure"></a>Ändra versionen av appar i Azure

Den version av Functions runtime som används av publicerade appar i Azure bestäms av inställningen för [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) program. Ett värde på `~2` riktar sig till version 2. x-körningen och `~1` riktar sig till version 1. x-körningsmiljön. Ändra inte den här inställningen godtyckligt, eftersom andra program inställnings ändringar och kod ändringar i dina funktioner troligen krävs. Information om det rekommenderade sättet att migrera din Function-app till en annan körnings version finns i [så här fungerar Azure Functions runtime-versioner](set-runtime-version.md).

## <a name="bindings"></a>Bindningar

Från och med version 2. x använder körningen en ny [bindnings modell för bindning](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som erbjuder följande fördelar:

* Stöd för bindnings tillägg från tredje part.

* Fri koppling av körning och bindningar. Den här ändringen tillåter att bindnings tilläggen versions och släpps oberoende av varandra. Du kan till exempel välja att uppgradera till en version av ett tillägg som förlitar sig på en nyare version av ett underliggande SDK.

* En miljö med en ljusare körning där endast de bindningar som används är kända och läses in av körnings miljön.

Med undantag för HTTP-och timer-utlösare måste alla bindningar uttryckligen läggas till i programmets projekt eller registreras i portalen. Mer information finns i [Registrera bindnings tillägg](./functions-bindings-expressions-patterns.md).

I följande tabell visas vilka bindningar som stöds i varje körnings version.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Så här riktar du Azure Functions runtime-versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)
