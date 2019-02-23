---
title: Azure Functions runtime versioner-översikt
description: Azure Functions har stöd för flera versioner av körningen. Läs om skillnaderna mellan dem och hur du väljer det alternativ som passar dig.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: cc19a2d7641b20a413bbc39efe0d1c5ede362e6d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734463"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions runtime versioner-översikt

 Det finns två större versioner av Azure Functions-körningen: 1.x och 2.x. Den aktuella versionen där den nya funktionen arbete och förbättringar görs är 2.x, även om båda har stöd för produktionsscenarier.  Följande beskriver några av skillnaderna mellan två, hur du kan skapa varje version och uppgradera från 1.x till 2.x.

> [!NOTE]
> Den här artikeln handlar om Molntjänsten Azure Functions. Information om förhandsversion som kan du köra Azure Functions lokalt finns i den [översikt över Azure Functions Runtime](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Plattformsoberoende utveckling

Version 2.x-körningen körs på .NET Core 2, vilket gör att den kan köras på alla plattformar som stöds av .NET Core, inklusive macOS och Linux. Gör som körs på .NET Core plattformsoberoende utveckling och värdscenario.

Jämförelsevis har stöd version 1.x-körningen endast för utveckling och som är värd i Azure-portalen eller på Windows-datorer.

## <a name="languages"></a>Languages

Version 2.x-körningen använder en ny modell för utökningsbarhet språk. I version 2.x kan alla funktioner i en funktionsapp måste dela samma språk. Språk av funktioner i en funktionsapp som ska väljas när du skapar appen.

Azure Functions 1.x experimentella språk uppdateras inte om du vill använda den nya modellen så att de inte stöds i 2.x. Följande tabell visar vilka programmeringsspråk stöds för närvarande i varje runtime-versionen.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="creating-1x-apps"></a>Kör version 1.x

Funktionsappar som skapats i Azure-portalen är som standard till version 2.x. Om det är möjligt bör du använda den här körningsversion där den nya funktionen investeringar görs. Om du behöver kan kör du fortfarande en funktionsapp på version 1.x-körningen. Du kan bara ändra runtime-versionen när du har skapat din funktionsapp, men innan du lägger till alla funktioner. Läs hur man fäster körningsversion till 1.x i [visa och uppdatera den aktuella runtime-versionen](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Migrera från 1.x till 2.x

Du kan välja att migrera en befintlig app som skrivits du använder version 1.x-körningen att i stället använda version 2.x. De flesta av de ändringar som du behöver göra är relaterade till ändringar i CLR, till exempel C# API-ändringar mellan .NET Framework 4.7 och .NET Core 2. Du måste också se till att din kod och bibliotek som är kompatibla med CLR som du väljer. Slutligen bör du se till att notera eventuella ändringar i utlösare och bindningar funktioner markerade nedan. För bästa resultat för migrering, bör du skapa en ny funktionsapp för version 2.x och port din befintliga version 1.x funktion code till den nya appen.  

### <a name="changes-in-triggers-and-bindings"></a>Ändringar i utlösare och bindningar

Version 2.x måste du installera tillägget för specifika utlösare och bindningar som används av funktioner i din app. Det enda undantaget för den här HTTP- och timer-utlösare, som inte kräver ett tillägg.  Mer information finns i [Register och installera bindande tillägg](./functions-bindings-expressions-patterns.md).

Har det även skett några ändringar i den `function.json` eller attribut för funktionen mellan versioner. Till exempel Event Hub `path` egenskapen är nu `eventHubName`. Se den [befintliga bindande tabell](#bindings) länkar till dokumentationen för varje bindning.

### <a name="changes-in-features-and-functionality"></a>Ändringar i funktionerna

Några funktioner som har också tagits bort, uppdatera eller ersatts i den nya versionen. Det här avsnittet beskrivs de ändringar som du ser i version 2.x när du använt version 1.x.

I version 2.x kan följande ändringar har gjorts:

* Nycklar för att anropa HTTP-slutpunkter måste alltid lagras krypterade i Azure Blob storage. I version 1.x, nycklar har lagrats i Azure File storage är standard. När du uppgraderar en app från version 1.x till version 2.x kan befintliga hemligheter som används i file storage återställs.

* Version 2.x-körningen innehåller inte inbyggt stöd för webhook-leverantörer. Den här ändringen har gjorts för att förbättra prestanda. Du kan fortfarande använda HTTP-utlösare som slutpunkter för webhooks.

* Värd-konfigurationsfilen (host.json) ska vara tomt eller ha strängen `"version": "2.0"`.

* Att förbättra övervakning, WebJobs-instrumentpanelen i portalen, som används i [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) inställningen ersätts med Azure Application Insights, som använder den [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsightsinstrumentationkey) inställningen. Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

* Alla funktioner i en funktionsapp måste dela samma språk. När du skapar en funktionsapp måste du välja en körningsstack för appen. Körningsstack som anges av den [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functionsworkerruntime) värdet i programinställningarna. Det här kravet har lagts till att förbättra tid-fotavtryck och Start. När du utvecklar lokalt, måste du också inkludera den här inställningen i den [local.settings.json-fil](functions-run-local.md#local-settings-file).

* Standardvärdet för timeout för i en App Service plan ändras till 30 minuter. Du kan manuellt ändra timeout-värdet till obegränsat med hjälp av den [functionTimeout](functions-host-json.md#functiontimeout) i host.json.

* HTTP-begränsningar för samtidighet implementeras som standard för förbrukning plan funktioner, med ett standardvärde på 100 samtidiga begäranden per instans. Du kan ändra detta i den [ `maxConcurrentRequests` ](functions-host-json.md#http) i host.json-filen.

* Grund av [.NET core begränsningar](https://github.com/Azure/azure-functions-host/issues/3414), stöd för F# (.fsx)-skriptfunktioner har tagits bort. Kompilerade F# (.fs)-funktioner stöds fortfarande.

* Event Grid-utlösare webhooks URL-formatet har ändrats till `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrera ett lokalt utvecklade program

Du kan ha befintliga funktionen app-projekt som du har utvecklat lokalt med hjälp av version 1.x-körningen. Uppgradera till version 2.x, bör du skapa en lokal funktionsappsprojekt mot version 2.x och port din befintliga kod i den nya appen. Du kan manuellt uppdatera det befintliga projektet och kod, ett slags ”” uppgradering på plats. Men det finns ett antal andra förbättringar mellan version 1.x och version 2.x som du kan fortfarande behöva göra. Till exempel i C# även felsökning objektet har ändrats från `TraceWriter` till `ILogger`. Genom att skapa ett nytt projekt i version 2.x kan börja du med uppdaterade funktioner baserat på de senaste version 2.x mallarna.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio-körningsversioner

I Visual Studio väljer du runtime-versionen när du skapar ett projekt. Med Azure Functions tools för Visual Studio stöder både större runtime-version. Rätt version används när felsökning och publicering av utifrån Projektinställningar. Inställningarna för version har angetts i den `.csproj` filen i följande egenskaper:

##### <a name="version-1x"></a>Version 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Version 2.x

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

När du felsöker eller publicera ditt projekt, används rätt version av körningen.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code och Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) används för utveckling av kommandorad och även av den [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. Du utvecklar mot version 2.x, installera version 2.x av de viktigaste verktygen. Version 1.x utveckling kräver version 1.x av de viktigaste verktygen. Mer information finns i [installera Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

För Visual Studio Code-utveckling du också behöva uppdatera användarinställningen för den `azureFunctions.projectRuntime` så att den matchar versionen av verktygen som installeras.  Den här inställningen uppdateras också mallar och språk som används när funktionen app skapas.

### <a name="changing-version-of-apps-in-azure"></a>Ändra versionen av appar i Azure

Versionen av Functions-körning som används av publicerade appar i Azure beror den [ `FUNCTIONS_EXTENSION_VERSION` ](functions-app-settings.md#functionsextensionversion) programinställningen. Värdet `~2` riktar sig till version 2.x-körningen och `~1` riktar sig mot version 1.x-körningen. Godtyckligt ändras inte den här inställningen eftersom andra ändringar i appen och kodändringar i dina funktioner är sannolikt krävs. Mer information om det rekommenderade sättet att migrera din funktionsapp till en annan körningsversion, se [hur du Azure Functions runtime versioner](set-runtime-version.md).

## <a name="bindings"></a>Bindningar

Version 2.x-körningen använder en ny [bindning modell för utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som ger följande fördelar:

* Stöd för tillägg av bindning från tredje part.

* Frikoppling av runtime och bindningar. Den här ändringen kan tillägg av bindning vara versionsnummer och utgivna oberoende av varandra. Du kan till exempel välja för att uppgradera till en version av ett tillägg som är beroende av en nyare version av en underliggande SDK.

* En ljusare körningsmiljö där endast bindningarna som används är kända och lästs in av körningen.

Med undantag för HTTP och timer utlösare, måste alla bindningar vara explicit har lagts till i funktionsappsprojekt eller registrerad i portalen. Mer information finns i [registrera tillägg av bindning](./functions-bindings-expressions-patterns.md).

I följande tabell visas vilka stöds i varje runtime-versionen.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Hur du Azure Functions runtime versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)
