---
title: Händelsevägar
titleSuffix: Azure Digital Twins
description: Förstå hur du dirigerar händelser i Azure Digitals dubbla och till andra Azure-tjänster.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: be5709c8ccf8626ac3a48fdf7cad1c61dbfbf628
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729529"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Dirigera händelser inom och utanför Azures digitala dubbla

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Azure Digitals dubbla använder **händelse vägar** för att skicka data till konsumenter utanför tjänsten. 

Under för hands versionen finns det två viktiga fall för att skicka Azure Digitals dubbla data:
* Skicka data från en mitt i det digitala Azure-diagrammet till en annan. Om du till exempel har en egenskap på en digital, dubbla ändringar kanske du vill meddela och uppdatera en annan digital i enlighet med detta.
* Skicka data till underordnade data tjänster för ytterligare lagring eller bearbetning (kallas även *utgående data*). Till exempel,
  - Ett sjukhus kan vilja skicka data för Azure Digitals dubbla data till [Time Series Insights (TSD)](../time-series-insights/time-series-insights-update-overview.md)för att registrera Time Series-data för handwashing-relaterade händelser för Mass analys.
  - En verksamhet som redan använder [Azure Maps](../azure-maps/about-azure-maps.md) kanske vill använda Azure Digital-dubbla för att förbättra sin lösning. De kan snabbt aktivera en Azure-karta när du har konfigurerat Azure Digitals-enheter, ta Azure Map-entiteter i Azure Digitals-enheter som [digitala](concepts-twins-graph.md) delar i den dubbla grafen eller kör kraftfulla frågor som använder sina Azure Maps och Azure Digitals sammanställda data tillsammans.

Händelse vägar används för båda dessa scenarier.

## <a name="about-event-routes"></a>Om händelse vägar

Med en händelse väg kan du skicka händelse data från digitala sändningar i Azure Digitals dubbla till anpassade slut punkter i dina prenumerationer. Tre Azure-tjänster stöds för närvarande för slut punkter: [händelsehubben](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)och [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Var och en av dessa Azure-tjänster kan anslutas till andra tjänster och fungerar som mellanman och skickar data till slutgiltiga destinationer, till exempel TSD eller Azure Maps för vilken bearbetning du behöver.

I följande diagram illustreras flödet av händelse data via en större IoT-lösning med en digital Azure-dataaspekt:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital sammanflätar data via slut punkter till flera underordnade tjänster" border="false":::

Vanliga underordnade mål för händelse vägar är resurser som TSD, Azure Maps, lagring och analys lösningar.

### <a name="event-routes-for-internal-digital-twin-events"></a>Händelse vägar för interna digitala dubbla händelser

Under den aktuella för hands versionen används händelse vägar också för att hantera händelser i den dubbla grafen och skicka data från digitala dubbla till digitala. Detta görs genom att du ansluter händelse vägar via Event Grid för att beräkna resurser, till exempel [Azure Functions](../azure-functions/functions-overview.md). Dessa funktioner definierar sedan hur dubbla ska ta emot och svara på händelser. 

När en beräknings resurs vill ändra det dubbla diagrammet baserat på en händelse som tas emot via händelse vägen, är det bra att veta vilken som är den som ska ändras i förväg. 

Alternativt innehåller händelse meddelandet också ID: t för den källa som skickade meddelandet, så att beräknings resursen kan använda frågor eller passera relationer för att hitta ett mål som är två för önskad åtgärd. 

Beräknings resursen måste också upprätta säkerhets-och åtkomst behörigheter oberoende av varandra.

Information om hur du konfigurerar en Azure-funktion för att bearbeta digitala dubbla händelser finns i [How-to: Konfigurera en Azure-funktion för bearbetning av data](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Skapa en slutpunkt

För att definiera en händelse väg måste utvecklare först definiera slut punkter. En **slut punkt** är ett mål utanför Azures digitala dubbla, som har stöd för en väg anslutning. Destinationer som stöds i den aktuella för hands versionen är:
* Event Grid anpassade ämnen
* Händelsehubb
* Service Bus

Slut punkter konfigureras med hjälp av API: er för kontroll plan (stöds av [Azure Digitals flätat CLI](how-to-use-cli.md)eller via Azure Portal. En slut punkts definition ger:
* Slut punktens ID (eller eget namn)
* Slut punkts typ (Event Grid, Händelsehubben eller Service Bus)
* Den primära anslutnings strängen och den sekundära anslutnings strängen som ska autentiseras 
* Avsnitts Sök vägen för slut punkten, till exempel *Your-topic.westus2.eventgrid.Azure.net*

Slut punkts-API: erna som är tillgängliga i kontroll planet är:
* Skapa slut punkt
* Hämta lista över slut punkter
* Hämta slut punkt efter ID (pass i slut punkts-ID)
* Ta bort slut punkt per ID (pass i slut punkt ID)

## <a name="create-an-event-route"></a>Skapa en händelse väg
 
Händelse vägar skapas i ett klient program med följande [.net (C#) SDK-](how-to-use-apis-sdks.md) anrop: 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-ID>"));
```

* `endpoint-ID`Identifierar en slut punkt, till exempel en händelsehubben, Event Grid eller Service Bus. De här slut punkterna måste skapas i din prenumeration och anslutas till Azure Digital-dubbla med kontroll Plans-API: er innan det här registrerings anropet görs.

Händelse vägen som skickas till `EventRoutes.Add` tar också en [ **filter** parameter](./how-to-manage-routes.md#filter-events)som kan användas för att begränsa vilka typer av händelser som följer den här vägen.

Vägar kan också skapas med hjälp av [Azure Digitals flätade CLI](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Typer av händelse meddelanden

Olika typer av händelser i IoT Hub och Azure Digitals skapar olika typer av meddelanden, enligt beskrivningen nedan.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Nästa steg

Se så här konfigurerar och hanterar du en händelse väg:
* [Anvisningar: hantera slut punkter och vägar](how-to-manage-routes.md)

Du kan också se hur du använder Azure Functions för att dirigera händelser i Azure Digitals:
* [Anvisningar: Konfigurera en Azure-funktion för bearbetning av data](how-to-create-azure-function.md)