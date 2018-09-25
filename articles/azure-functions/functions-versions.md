---
title: Azure Functions runtime versioner-översikt
description: Azure Functions har stöd för flera versioner av körningen. Läs om skillnaderna mellan dem och hur du väljer det alternativ som passar dig.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978632"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions runtime versioner-översikt

 Det finns två större versioner av Azure Functions-körningen: 1.x och 2.x. Den aktuella versionen där den nya funktionen arbete och förbättringar görs är 2.x, även om båda har stöd för produktionsscenarier.  Följande beskriver några av skillnaderna mellan två, hur du kan skapa varje version och uppgradera från 1.x till 2.x.

> [!NOTE] 
> Den här artikeln handlar om Molntjänsten Azure Functions. Information om förhandsversion som kan du köra Azure Functions lokalt finns i den [översikt över Azure Functions Runtime](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Skapa 1.x-appar

Nya appar som har skapats i Azure Portal är inställda på 2.x som standard eftersom det här är den senaste versionen och där den nya funktionen investeringar görs.  Du kan dock fortfarande skapa v1.x appar genom att göra följande.

1. Skapa en Azure-funktion från Azure Portal
1. Öppna appen skapade och när den är tom öppnar den **funktionsinställningar**
1. Ändra versionen från ~ 2 till ~ 1.  *Den här växlingen kommer att inaktiveras om du har funktioner i din app*.
1. Klicka på Spara och starta om appen.  Alla mallar ska nu skapa och köra i 1.x.

## <a name="cross-platform-development"></a>Plattformsoberoende utveckling

Runtime 1.x har stöd för utveckling och som är värd för endast i portalen eller på Windows. Runtime 2.x körs på .NET Core 2, vilket innebär att den kan köras på alla plattformar som stöds av .NET Core, inklusive macOS och Linux. Det möjliggör plattformsoberoende utveckling och värdscenario.

## <a name="languages"></a>Språk

Runtime 2.x använder en ny modell för utökningsbarhet språk. Dessutom för att förbättra verktyg och prestanda begränsas varje Apps 2.x till endast ha funktioner på ett språk. Språk som stöds för närvarande i 2.x är C#, F #, JavaScript och Java. Azure Functions 1.x experimentella språk har inte uppdaterats för att använda den nya modellen så att de inte stöds i 2.x. Följande tabell visar vilka programmeringsspråk som stöds i varje runtime-versionen.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Migrera från 1.x till 2.x

Du kanske vill flytta en befintlig app som skrivits i 1.x till 2.x.  De flesta av överväganden krävs i flytta mellan versioner är relaterade till language runtime ändringarna som listas ovan (till exempel C# flyttar från .NET Framework 4.7 till .NET Core 2).  Du måste se till att din kod och bibliotek som är kompatibla med språkkörningar som används.  Heller se till att notera eventuella ändringar i utlösare och bindningar funktioner markerade nedan.

### <a name="changes-in-triggers-and-bindings"></a>Ändringar i utlösare och bindningar

Samtidigt som de flesta egenskaper för utlösare och bindningen och konfigurationer kvarstår mellan versioner, i 2.x behöver du installera alla lagringsutlösare eller bindning till appen. Det enda undantaget till detta är HTTP och Timer utlösare.  Se [Register och installera bindande tillägg](./functions-triggers-bindings.md#register-binding-extensions).  Observera att det kan också förekomma ändringar i den `function.json` eller attribut för funktionen mellan versioner (till exempel CosmosDB `connection` egenskapen är nu `ConnectionStringSetting`).  Referens för den [befintliga bindande tabell](#bindings) länkar till dokumentationen för varje bindning.

### <a name="changes-in-features-available"></a>Ändringar i funktionerna

Förutom ändringarna i språk och bindningar finns det vissa funktioner som har tagits bort, uppdatera eller ersätta mellan versioner.  Nedan visas några av de viktigast att tänka på när du börjar med 2.x när du har använt 1.x.  Följande ändringar har gjorts i v2.x:

* Nycklar för att anropa en funktion lagras alltid i krypterad blob storage. I 1.x som standard de var i file storage och kan flyttas till blob om att aktivera funktioner som platser.  Uppgradera en 1.x-app till 2.x och hemligheter finns i file storage för närvarande återställs.
* För att förbättra prestanda, tas bort och ersätts med ”HTTP” utlösare ”webhook” typ av utlösare.
* Värdkonfigurationen (`host.json`) ska antingen vara tomma eller innehålla `version` av `2.0` för en av egenskaperna.
* Att förbättra övervaknings- och observability, WebJobs-instrumentpanelen (`AzureWebJobsDashboard`) ersätts med [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Programinställningar (`local.settings.json`) kräver ett värde för egenskapen `FUNCTIONS_WORKER_RUNTIME` som mappar till språket i appen `dotnet | node | java | python`.
    * Appar är begränsad till ett språk för att förbättra tid-fotavtryck och Start. Du kan publicera flera appar om du vill ha funktioner på olika språk för samma lösning.
* Standardvärdet för timeout för i en app service-plan är 30 minuter.  Det kan fortfarande manuellt sättas till obegränsad.
* [På grund av .NET core uppladdningskvoter](https://github.com/Azure/azure-functions-host/issues/3414), `.fsx` skript för F #-funktioner har tagits bort. Kompilerade F #-funktioner stöds fortfarande.
* Formatet för webhook-baserade utlösare (t.ex. Event Grid) har ändrats till `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Uppgradera ett lokalt utvecklade program

Om v1.x appen utvecklades lokalt, du kan ändra till app eller projekt så att de blir kompatibel med v2.  Vi rekommenderar att skapa en ny app och port över koden till den nya appen.  Det finns ändringar som kan göras i en befintlig app för att utföra en på plats uppgradera, det finns ett antal andra förbättringar mellan v1 och v2 som äldre kod som sannolikt inte att utnyttja (till exempel i C# ändringen från `TraceWriter` till `ILogger`) .  

Den rekommendera sökvägen är start från någon av de v2-mallarna och flytta över kod i ett nytt projekt eller app.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio-körningsversioner

I Visual Studio väljer du runtime-versionen när du skapar ett projekt.  Visual Studio har bitar för båda huvudversioner och använda dynamiskt rätt typ för projektet.  De här inställningarna härleds från den `.csproj` filen.  För 1.x appar har projektet egenskaperna

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

I v2 är projektegenskaperna

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Klicka på felsökning eller publicera kommer korrekt ställer in rätt version för projektinställningarna.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code och Azure Functions Core Tools

Andra lokala verktyg är beroende av Azure Functions Core Tools.  Dessa verktyg är installerade på datorn och vanligtvis endast en version är installerad på en utvecklingsdator i taget.  Se [anvisningar hur du installerar specifika versioner av de viktigaste verktygen](./functions-run-local.md).

För VS Code du också behöva uppdatera användarinställningen för den `azureFunctions.projectRuntime` så att den matchar versionen av verktygen som installeras.  Det här uppdaterar även mallar och språk som visas när du skapar nya appar.

### <a name="changing-version-of-apps-in-azure"></a>Ändra versionen av appar i Azure

Publicerade versioner anges via inställningen `FUNCTIONS_RUNTIME_VERSION`.  Detta är inställt på `~2` för v2-appar och `~1` för v1-appar.  Nu är det rekommenderas inte att ändra runtime-versionen av en app som har befintliga funktioner som publiceras till den utan att också ändra koden för dessa funktioner.  Den rekommendera sökvägen är att skapa en ny funktionsapp och inställd på rätt version, testa ändringar, och sedan inaktivera eller ta bort appen tidigare.

## <a name="bindings"></a>Bindningar 

Runtime 2.x använder en ny [bindning modell för utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som ger följande fördelar:

* Stöd för tillägg av bindning från tredje part.
* Frikoppling av runtime och bindningar. På så sätt kan tillägg av bindning vara versionsnummer och utgivna oberoende av varandra. Du kan till exempel välja för att uppgradera till en version av ett tillägg som är beroende av en nyare version av en underliggande SDK.
* En ljusare körningsmiljö där endast bindningarna som används är kända och lästs in av körningen.

Alla inbyggda Azure Functions-bindningar har antagit den här modellen och ingår inte längre som standard, förutom den Timer som utlösaren och HTTP-utlösaren. Dessa tillägg installeras automatiskt när du skapar funktioner via verktyg som Visual Studio eller via portalen.

Följande tabell visar vilka stöds i varje runtime-versionen.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Hur du Azure Functions runtime versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)
