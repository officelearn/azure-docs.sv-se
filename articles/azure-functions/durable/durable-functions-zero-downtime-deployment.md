---
title: Zero-stilltime-distribution för varaktiga funktioner
description: Lär dig hur du aktiverar orchestrationen varaktiga funktioner för zero-downtime-distributioner.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231260"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Zero-stilltime-distribution för varaktiga funktioner

Den [tillförlitliga körningsmodellen](durable-functions-checkpointing-and-replay.md) för varaktiga funktioner kräver att orkestreringar är deterministiska, vilket skapar ytterligare en utmaning att tänka på när du distribuerar uppdateringar. När en distribution innehåller ändringar i aktivitetsfunktionssignaturer eller orchestratorlogik misslyckas orchestration-instanser under flygning. Denna situation är särskilt ett problem för fall av långvariga orkestreringar, som kan representera timmar eller dagar av arbete.

För att förhindra att dessa fel inträffar har du två alternativ: 
- Fördröja distributionen tills alla gående orchestration-instanser har slutförts.
- Kontrollera att alla orchestration-instanser som körs använder befintliga versioner av dina funktioner. 

> [!NOTE]
> Den här artikeln innehåller vägledning för funktionsappar som är inriktade på varaktiga funktioner 1.x. Det har inte uppdaterats för att ta hänsyn till ändringar som införts i Varaktiga funktioner 2.x. Mer information om skillnaderna mellan tilläggsversioner finns i [versioner av varaktiga funktioner](durable-functions-versions.md).

I följande diagram jämförs de tre huvudstrategierna för att uppnå noll driftstopp för varaktiga funktioner: 

| Strategi |  När du ska använda detta | Fördelar | Nackdelar |
| -------- | ------------ | ---- | ---- |
| [Versionshantering](#versioning) |  Program som inte ofta [bryter ändringar.](durable-functions-versioning.md) | Enkelt att implementera. |  Ökad funktion app storlek i minne och antal funktioner.<br/>Koddubblering. |
| [Statuskontroll med kortplats](#status-check-with-slot) | Ett system som inte har långvariga orkestreringar som varar mer än 24 timmar eller ofta överlappande orkestreringar. | Enkel kodbas.<br/>Kräver inte ytterligare funktionsapphantering. | Kräver ytterligare lagringskonto eller uppgiftsnavhantering.<br/>Kräver tidsperioder när inga orkestreringar körs. |
| [Programdirigering](#application-routing) | Ett system som inte har tidsperioder när orkestreringar inte körs, till exempel de tidsperioder med orkestreringar som varar mer än 24 timmar eller med ofta överlappande orkestreringar. | Hanterar nya versioner av system med kontinuerligt löpande orkestreringar som har brytande ändringar. | Kräver en intelligent programrouter.<br/>Kan maxa antalet funktionsappar som tillåts av din prenumeration. Standardvärdet är 100. |

## <a name="versioning"></a>Versionshantering

Definiera nya versioner av dina funktioner och lämna de gamla versionerna i funktionsappen. Som du kan se i diagrammet blir en funktions version en del av namnet. Eftersom tidigare versioner av funktioner bevaras kan orchestrationinstanser under flygning fortsätta att referera till dem. Under tiden kräver begäranden om nya orchestration-instanser den senaste versionen, som din orkestreringsklientfunktion kan referera till från en appinställning.

![Strategi för versionshantering](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

I den här strategin måste varje funktion kopieras och dess referenser till andra funktioner måste uppdateras. Du kan göra det enklare genom att skriva ett skript. Här är ett [exempelprojekt](https://github.com/TsuyoshiUshio/DurableVersioning) med ett migreringsskript.

>[!NOTE]
>Den här strategin använder distributionsplatser för att undvika driftstopp under distributionen. Mer detaljerad information om hur du skapar och använder nya distributionsplatser finns i [Azure Functions distributionsplatser](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Statuskontroll med kortplats

Medan den aktuella versionen av funktionsappen körs i produktionsplatsen distribuerar du den nya versionen av funktionsappen till mellanlagringsplatsen. Innan du byter produktions- och mellanlagringsplatser kontrollerar du om det finns några förekomster av orkestrering. När alla orkestreringsinstanser är slutförd kan du göra bytet. Den här strategin fungerar när du har förutsägbara perioder när inga orchestration-instanser är under flygning. Detta är den bästa metoden när dina orkestreringar inte är långvariga och när dina orkestreringskörningar inte ofta överlappar varandra.

### <a name="function-app-configuration"></a>Konfiguration av funktionsapp

Använd följande procedur för att ställa in det här scenariot.

1. [Lägg till distributionsplatser](../functions-deployment-slots.md#add-a-slot) i funktionsappen för mellanlagring och produktion.

1. För varje plats anger du [programinställningen AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) på anslutningssträngen för ett delat lagringskonto. Den här anslutningssträngen för lagringskonto används av Azure Functions-körningen. Det här kontot används av Azure Functions-körningen och hanterar funktionens nycklar.

1. Skapa en ny appinställning för varje `DurableManagementStorage`plats, till exempel . Ange dess värde till anslutningssträngen för olika lagringskonton. Dessa lagringskonton används av tillägget Varaktiga funktioner för [tillförlitlig körning](durable-functions-checkpointing-and-replay.md). Använd ett separat lagringskonto för varje plats. Markera inte den här inställningen som en distributionsplatsinställning.

1. I funktionsappens [host.json-fils durableTask-avsnitt](durable-functions-bindings.md#hostjson-settings)anger du `azureStorageConnectionStringName` som namnet på appinställningen som du skapade i steg 3.

Följande diagram visar den beskrivna konfigurationen av distributionsplatser och lagringskonton. I det här potentiella scenariot för fördistribution körs version 2 av en funktionsapp i produktionsplatsen, medan version 1 finns kvar i mellanlagringsplatsen.

![Distributionsplatser och lagringskonton](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json exempel

Följande JSON-fragment är exempel på anslutningsstränginställningen i *filen host.json.*

#### <a name="functions-20"></a>Funktioner 2.0

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

### <a name="cicd-pipeline-configuration"></a>Konfiguration av CI/CD-pipeline

Konfigurera CI/CD-pipelinen så att den bara distribueras när funktionsappen inte har några väntande eller körande orkestreringsinstanser. När du använder Azure Pipelines kan du skapa en funktion som söker efter dessa villkor, som i följande exempel:

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

Konfigurera sedan mellanlagringsporten så att den väntar tills inga orkestreringar körs. Mer information finns i [Frigöra distributionskontroll med grindar](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Distributionsport](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines kontrollerar din funktionsapp för att köra orchestration-instanser innan distributionen startar.

![Utplaceringsgrind (löpning)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nu ska den nya versionen av funktionsappen distribueras till mellanlagringsplatsen.

![Mellanlagringsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Slutligen, swap slots. 

Programinställningar som inte är markerade som inställningar för distributionsplats byts också, så version 2-appen behåller sin referens till lagringskonto A. Eftersom orkestreringstillstånd spåras i lagringskontot fortsätter alla orkestreringar som körs i version 2-appen att köras i den nya platsen utan avbrott.

![Distributionsplats](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Om du vill använda samma lagringskonto för båda facken kan du ändra namnen på aktivitetshubbar. I det här fallet måste du hantera läget för dina kortplatser och appens HubName-inställningar. Mer information finns [i Aktivitetshubbar i varaktiga funktioner](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Programdirigering

Denna strategi är den mest komplexa. Den kan dock användas för funktionsappar som inte har tid mellan att köra orkestreringar.

För den här strategin måste du skapa en *programrouter* framför dina varaktiga funktioner. Denna router kan implementeras med varaktiga funktioner. Routern har ansvaret för att:

* Distribuera funktionsappen.
* Hantera versionen av varaktiga funktioner. 
* Dirigera orkestreringsbegäranden till funktionsappar.

Första gången en orkestreringsbegäran tas emot utför routern följande uppgifter:

1. Skapar en ny funktionsapp i Azure.
2. Distribuerar funktionsappens kod till den nya funktionsappen i Azure.
3. Vidarebefordrar orkestreringsbegäran till den nya appen.

Routern hanterar tillståndet för vilken version av appens kod som distribueras till vilken funktionsapp i Azure.

![Programdirigering (första gången)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Routern dirigerar distributions- och orkestreringsbegäranden till lämplig funktionsapp baserat på den version som skickas med begäran. Den ignorerar patchversionen.

När du distribuerar en ny version av appen utan att brytas kan du öka korrigeringsversionen. Routern distribueras till din befintliga funktionsapp och skickar begäranden om gamla och nya versioner av koden, som dirigeras till samma funktionsapp.

![Programdirigering (ingen brytningsändring)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

När du distribuerar en ny version av appen med en brytningsändring kan du öka huvud- eller delversionen. Sedan skapar programroutern en ny funktionsapp i Azure, distribuerar till den och dirigerar begäranden för den nya versionen av din app till den. I följande diagram fortsätter 1.0.1-versionen av appen att köras när orchestrations körs i 1.0.1-versionen, men begäranden om 1.1.0-versionen dirigeras till den nya funktionsappen.

![Programdirigering (brytningsändring)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Routern övervakar status för orkestreringar på 1.0.1-versionen och tar bort appar när alla orkestreringar är klara. 

### <a name="tracking-store-settings"></a>Spåra butiksinställningar

Varje funktionsapp bör använda separata schemaläggningsköer, eventuellt i separata lagringskonton. Om du vill fråga alla orchestrations-instanser i alla versioner av ditt program kan du dela instans- och historiktabeller över dina funktionsappar. Du kan dela tabeller genom `trackingStoreConnectionStringName` `trackingStoreNamePrefix` att konfigurera och inställningarna i filen [host.json-inställningar](durable-functions-bindings.md#host-json) så att alla använder samma värden.

Mer information finns [i Hantera instanser i varaktiga funktioner i Azure](durable-functions-instance-management.md).

![Spåra butiksinställningar](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Varaktiga funktioner för versionshantering](durable-functions-versioning.md)

