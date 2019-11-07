---
title: Noll drift stopp distribution för Durable Functions
description: Lär dig hur du aktiverar Durable Functions dirigering för noll stillestånds distributioner.
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: b47604f2c8703ba587e98d68dc30552e5944f562
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614490"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Noll drift stopp distribution för Durable Functions

Den [tillförlitliga körnings modellen](durable-functions-checkpointing-and-replay.md) för Durable Functions kräver att dirigeringar är deterministiska, vilket skapar en ytterligare utmaning att överväga när du distribuerar uppdateringar. När en distribution innehåller ändringar i aktivitets funktionens signaturer eller Orchestrator-logik, går det inte att utföra Dirigerings instanser i flygning. Den här situationen är särskilt ett problem för instanser av långvariga dirigeringar som kan representera timmar eller arbets dagar.

För att förhindra att dessa problem uppstår måste du antingen skjuta upp distributionen tills alla pågående Dirigerings instanser har slutförts, eller se till att alla pågående Orchestration-instanser använder befintliga versioner av dina funktioner. Mer information om versions hantering finns [i versions hantering i Durable Functions](durable-functions-versioning.md).

> [!NOTE]
> Den här artikeln innehåller rikt linjer för functions-appar som riktar sig Durable Functions 1. x. Den har ännu inte uppdaterats till ändringar som gjorts i Durable Functions 2. x. Mer information om skillnaderna mellan tilläggs versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

Följande diagram jämför de tre huvud strategierna för att uppnå en distribution utan drift stopp för Durable Functions: 

| Strategi |  När du ska använda detta | Proffs | Nackdelar |
| -------- | ------------ | ---- | ---- |
| **[Versions hantering](#versioning)** |  Program som inte upplever frekventa [ändringar.](durable-functions-versioning.md) | Enkelt att implementera. |  Ökad funktions program storlek i minnet och antalet funktioner.<br/>Kod duplicering. |
| **[Status kontroll med plats](#status-check-with-slot)** | Ett system som inte har långvariga dirigeringar som varar mer än 24 timmar eller ofta överlappande dirigeringar. | Enkel kodbas.<br/>Kräver inte ytterligare hantering av funktions program. | Kräver ytterligare lagrings konto eller aktivitets NAVs hantering.<br/>Kräver tids perioder då inga dirigeringar körs. |
| **[Program dirigering](#application-routing)** | Ett system som inte har några tids perioder när Orchestration inte körs, t. ex. de som har ett Orchestration som varar mer än 24 timmar eller med ofta överlappande dirigeringar. | Hanterar nya versioner av system med kontinuerlig körning av dirigeringar som har avbrutit ändringar. | Kräver en intelligent programrouter.<br/>Det gick inte att max gränsen för antalet funktions program som tillåts av din prenumeration (standard 100). |

## <a name="versioning"></a>Versionshantering

Definiera nya versioner av funktionerna och lämna de gamla versionerna i din Function-app. Som du kan se i diagrammet blir en funktions version en del av dess namn. Eftersom tidigare versioner av Functions bevaras, kan Dirigerings instanser i flyg plan fortsätta att referera till dem. Efter frågas begär Anden om nya Orchestration-instanser att anropa den senaste versionen, vilken Orchestration-klienten kan referera till från en app-inställning.

![Strategi för versions hantering](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

I den här strategin måste alla funktioner kopieras och dess referenser till andra funktioner uppdateras. Du kan göra det enklare genom att skriva ett skript. Här är ett [exempel projekt](https://github.com/TsuyoshiUshio/DurableVersioning) med ett migreringsarkiv.

>[!NOTE]
>Den här strategin använder distributions platser för att undvika drift stopp under distributionen. Mer detaljerad information om hur du skapar och använder nya distributions platser hittar [Azure Functions distributions fack](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Status kontroll med plats

När den aktuella versionen av din Function-App körs på produktions platsen, kan du distribuera den nya versionen av din Function-app till mellanlagringsplatsen. Innan du byter ut dina produktions-och mellanlagringsplatser bör du kontrol lera om det finns kör Orchestration-instanser. När alla Dirigerings instanser har slutförts kan du göra växlingen. Den här strategin fungerar när du har förutsägbara perioder när inga Dirigerings instanser är i flygning. Detta är den bästa metoden när dina dirigeringar inte är långvariga och när dina Dirigerings körningar inte ofta överlappar varandra.

### <a name="function-app-configuration"></a>Function-app-konfiguration

Du kan använda följande procedur för att konfigurera det här scenariot:

1. [Lägg till distributions platser](../functions-deployment-slots.md#add-a-slot) i din Function-app för mellanlagring och produktion.

1. För varje plats ställer du in [program inställningen AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) på anslutnings strängen för ett delat lagrings konto. Detta kommer att användas av Azure Functions Runtime. Det här kontot kommer att användas av Azure Functions Runtime och hanterar funktionens nycklar.

1. Skapa en ny app-inställning för varje plats (t. ex. DurableManagementStorage) och ange dess värde till anslutnings strängen för olika lagrings konton. Dessa lagrings konton kommer att användas av Durable Functions-tillägget för [tillförlitlig körning](durable-functions-checkpointing-and-replay.md). Använd ett separat lagrings konto för varje plats. Markera inte den här inställningen som en distributions plats inställning.

1. Ange azureStorageConnectionStringName som namn på den app-inställning som du skapade i steg 3 i durableTask-avsnittet för Function-appens [Host. JSON-fil](durable-functions-bindings.md#hostjson-settings).

Diagrammet nedan visar visar den beskrivna konfigurationen av distributions platser och lagrings konton. I det här scenariot körs version 2 av en Function-app på produktions platsen, medan version 1 är kvar på mellanlagringsplatsen.

![Distributionsplats](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Host. JSON-exempel

Följande JSON-fragment är exempel på inställningen för anslutnings strängen i Host. JSON-filen.

#### <a name="functions-20"></a>Functions 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Konfiguration av konfiguration av CI/CD-pipeline

Konfigurera din CI/CD-pipeline så att den bara distribueras när Function-appen inte har några väntande eller pågående Dirigerings instanser. När du använder Azure-pipeliner kan du skapa en funktion som söker efter dessa villkor, som i följande exempel:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Konfigurera sedan mellanlagrings porten så att den väntar tills inga dirigeringar körs. Mer information finns i avsnittet om [distribution av distributions kontroll med hjälp av portar](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Distributions grind](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure-pipeline kontrollerar din app för att köra Orchestration-instanser innan distributionen startar.

![Distributions grind (körs)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nu bör den nya versionen av din Function-app distribueras till mellanlagringsplatsen.

![Distributionsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Slutligen kan du växla fack. 

Program inställningar som inte har marker ATS som distributions plats inställningar växlas också, så version 2-appen behåller sin referens till lagrings konto A. Eftersom Dirigerings status spåras i lagrings kontot fortsätter alla dirigeringar som körs på version 2-appen att köras på den nya platsen utan avbrott.

![Distributionsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Om du vill använda samma lagrings konto för båda platserna kan du ändra namnen på dina aktivitets nav. I så fall måste du hantera statusen för dina platser och dina appar HubName-inställningar. Mer information finns [i aktivitets nav i Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Program dirigering

Den här strategin är den mest komplexa. Det kan dock användas för Function-appar som inte har tid mellan att köra dirigeringar.

För den här strategin måste du skapa en *programrouter* framför din Durable functions. Den här routern kan implementeras med Durable Functions och har följande ansvars områden:

* Distribuera Function-appen.
* Hantera versionen av Durable Functions. 
* Routning av Dirigerings begär anden till Function-appar.

Första gången en Dirigerings förfrågan tas emot gör routern aktiviteterna:

1. Skapar en ny function-app i Azure.
2. Distribuerar din Function app-kod till den nya Function-appen i Azure.
3. Vidarebefordrar Orchestration-begäran till den nya appen.

Routern hanterar det tillstånd för vilken version av appens kod distribueras till vilken Function-app i Azure.

![Programroutning (första gången)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Routern dirigerar distributions-och Dirigerings begär anden till lämplig Function-app baserat på `version` som skickas med begäran och ignorerar korrigerings versionen.

När du distribuerar en ny version av din app *utan* en avbrytande ändring kan du öka korrigerings versionen. Routern distribuerar till din befintliga Function-app och skickar begär Anden för gamla och nya versioner av koden dirigeras till samma Function-app.

![Programroutning (ingen brytande ändring)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

När du distribuerar en ny version av din app med en avbrytande ändring kan du öka den övre eller lägre versionen. Programroutern skapar sedan en ny function-app i Azure, distribuerar till den och dirigerar begär Anden för den nya versionen av din app till den. I diagrammet nedan ska körningen av dirigering i 1.0.1-versionen av appen fortsätta att köras, men förfrågningar för 1.1.0-versionen dirigeras till den nya Function-appen.

![Programroutning (bryta ändring)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Routern övervakar status för dirigering i 1.0.1-versionen och tar bort appar när alla dirigeringar har slutförts.  

### <a name="tracking-store-settings"></a>Spårar butiks inställningar

Varje Function-app bör använda separata schemaläggnings köer, eventuellt i separata lagrings konton. Men om du vill fråga alla Dirigerings instanser i alla versioner av programmet kan du dela instans-och historik tabeller i dina funktions program. Du kan dela tabeller genom att konfigurera `trackingStoreConnectionStringName` och `trackingStoreNamePrefix` i filen [Host. JSON-inställningar](durable-functions-bindings.md#host-json) så att alla använder samma värden.

Mer information finns [i Hantera instanser i Durable Functions i Azure](durable-functions-instance-management.md).

![Spårar butiks inställningar](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versions Durable Functions](durable-functions-versioning.md)

