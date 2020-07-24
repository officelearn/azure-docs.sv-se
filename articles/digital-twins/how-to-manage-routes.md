---
title: Hantera slutpunkter och vägar
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar och hanterar slut punkter och händelse vägar för Azure Digitals dubbla data.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bc22cf5a21709ccacafe068a60541cc9990d1131
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132270"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Hantera slut punkter och vägar i digitala Azure-dubbla

I Azure Digitals dubbla, kan du dirigera [händelse meddelanden](how-to-interpret-event-data.md) till underordnade tjänster eller ansluta till beräknings resurser. Detta görs genom att först konfigurera **slut punkter** som kan ta emot händelserna, följt av de [**händelse vägar**](concepts-route-events.md) som anger vilka händelser som genererats av Azure Digitals-meddelanden som levereras till vilka slut punkter.

Slut punkts typer som stöds är:
* [Händelsehubb](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Mer information om de olika slut punkterna finns i [*Välj mellan Azure Messaging Services*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Slut punkter och vägar hanteras med EventRoutes- [**API: er**](how-to-use-apis-sdks.md), [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)eller [Azure Digitals flätade CLI](how-to-use-cli.md). De kan också hanteras via [Azure Portal](https://portal.azure.com).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Skapa en slut punkt för Azure Digitals dubbla

Om du vill länka en slut punkt till Azure Digitals flätar, Event-hubben, Event Grid-ämnet eller Service Bus som du använder för slut punkten måste redan finnas. 

I följande exempel visas hur du skapar ett event Grid-ämne med hjälp av Azure CLI:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Om du vill skapa en lista med namn på Azure-regioner som kan skickas till kommandon i Azure CLI kör du följande kommando:
> ```azurecli
> az account list-locations -o table
> ```

När du har skapat ämnet kan du länka det till Azure Digitals dubbla med följande kommando:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Detta gör Event Grid-ämnet tillgängligt i Azure Digitals, under namnet som anges med `--endpoint-name` argumentet. Du använder vanligt vis det namnet som mål för en **händelse väg**, som du kommer att skapa i nästa avsnitt med hjälp av Azure Digitals tjänst-API.

Det finns motsvarande kommandon för Händelsehubben och Service Bus slut punkter:

* Lägg till Service Bus avsnitts slut punkt (kräver en redan skapad Service Bus-resurs)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Lägg till Event Hub-slutpunkten (kräver en tidigare skapad Event Hub-resurs)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Hantera händelse vägar med API: er

Om du faktiskt vill skicka data från digitala Azure-sändningar till en slut punkt måste du definiera en händelse väg. Med Azure Digitals **EventRoutes-API: er** kan utvecklare skapa händelse flöde, i hela systemet och i underordnade tjänster. Läs mer om händelse vägar i [*begrepp: routing Azure Digitals, dubbla händelser*](concepts-route-events.md).

Du kan fortsätta att skapa en händelse väg när slut punkterna har koner ATS.

>[!NOTE]
>Om du nyligen har distribuerat dina slut punkter kontrollerar du att de är klara med distributionen **innan** du försöker använda dem för en ny händelse väg. Om väg distributionen Miss lyckas eftersom slut punkterna inte är klara, väntar du några minuter och försöker igen.
>
> Om du använder skript för det här flödet kanske du vill ta hänsyn till det genom att skapa i 2-3 minuter vänte tid för slut punkts tjänsten för att slutföra distributionen innan du går vidare till väg installationen.

I exemplen i den här artikeln används C# SDK.

Händelse vägar definieras med hjälp av data Plans-API: er. En flödes definition kan innehålla följande element:
* Det väg-ID som du vill använda
* Namnet på den slut punkt som du vill använda
* Ett filter som definierar vilka händelser som skickas till slut punkten 

Om det inte finns något väg-ID dirigeras inga meddelanden utanför Azures digitala dubbla. Om det finns ett väg-ID och filtret är `true` , dirigeras alla meddelanden till slut punkten. Om ett väg-ID och ett annat filter läggs till filtreras meddelandena baserat på filtret.

En väg bör tillåta att flera meddelanden och händelse typer väljs. 

`CreateEventRoute`är SDK-anropet som används för att lägga till en händelse väg. Här är ett exempel på användningen:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

### <a name="event-route-sample-code"></a>Exempel kod för händelse väg

Följande kod exempel visar hur du skapar, visar och tar bort en händelse väg:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrera händelser

Utan filtrering tar slut punkter emot flera olika händelser från Azures digitala dubbla:
* Telemetri som har utlösts av [digitala sammanflätade](concepts-twins-graph.md) tjänster med Azure Digitals dubbla tjänst-API
* Dubbla egenskaps ändrings meddelanden, utlöses vid egenskaps ändringar för alla dubbla i Azure Digitals-instansen
* Livs cykel händelser, utlöses när de är uppdelade eller relationer skapade eller raderas
* Modell ändrings händelser, som utlöses när [modeller](concepts-models.md) som kon figurer ATS i en Azure Digitals dubblas-instans läggs till eller tas bort

Du kan begränsa vilka händelser som ska skickas genom att lägga till ett filter till en slut punkt.

Om du vill lägga till ett filter kan du använda en skicka-begäran till *https://{YourHost}/EventRoutes/myNewRoute? API-version = 2020-05 -31-Preview* med följande text:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Här följer de väg filter som stöds.

| Filternamn | Beskrivning | Filter schema | Värden som stöds | 
| --- | --- | --- | --- |
| Typ | Den [typ av händelse](./concepts-route-events.md#types-of-event-messages) som flödar genom den digitala dubbla instansen | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Källa | Namn på Azure Digitals dubbla instanser | `"filter" : "source = '<hostname>'"`|  **För meddelanden**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **För telemetri**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Ämne | En beskrivning av händelsen i kontexten för händelse källan ovan | `"filter": " subject = '<subject>'"` | **För meddelanden**: ämnet är`<twinid>` <br> eller ett URI-format för ämnen som identifieras unikt av flera delar eller ID:<br>`<twinid>/relationships/<relationshipid>`<br> **För telemetri**: ämnet är komponent Sök vägen (om telemetri skickas från en dubbel komponent), till exempel `comp1.comp2` . Om telemetri inte genereras från en komponent är dess ämnes fält tomt. |
| Data schema | DTDL modell-ID | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **För telemetri**: dataschemat är modell-ID: t för den dubbla eller komponenten som utvärderar Telemetrin <br>**För meddelanden**: data schema stöds inte|
| Innehållstyp | Innehålls typ för data värde | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Specifikations version | Den version av händelse schemat som du använder | `"filter": "specversion = '<version>'"` | Måste vara `1.0` . Detta anger CloudEvents-schema version 1,0 |
| True/false | Tillåter att du skapar en väg utan filtrering eller inaktiverar en väg | `"filter" : "<true/false>"` | `true`= Route är aktiverat utan filtrering <br> `false`= vägen är inaktive rad |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Det är också möjligt att kombinera filter med följande åtgärder:

| Filternamn | Filter schema | Exempel | 
| --- | --- | --- |
| OCH/ELLER | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| OCH/ELLER | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Kapslade åtgärder | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Under för hands versionen stöds endast sträng likhets tecken (=,! =).

När du implementerar eller uppdaterar ett filter kan det ta några minuter innan ändringen visas i data pipelinen.

## <a name="manage-endpoints-and-routes-with-cli"></a>Hantera slut punkter och vägar med CLI

Slut punkter och vägar kan också hanteras med hjälp av Azure Digitals flätade CLI. Kommandona finns i [*anvisningar: använda Azure Digitals flätade CLI*](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Övervaka händelse vägar

Routnings mått som antal, svars tid och frekvens kan visas i [Azure Portal](https://portal.azure.com/). 

Från portalens start sida söker du efter din Azure Digital-instansen för att hämta information. Välj alternativet **mått** från Azure Digitals instansen meny för att öppna sidan *mått* .

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Sidan mått för en digital Azure-instans i Azure Portal":::

Härifrån kan du visa måtten för din instans och skapa anpassade vyer.

## <a name="next-steps"></a>Nästa steg

Läs om de olika typerna av händelse meddelanden som du kan ta emot:
* [*Anvisningar: tolka händelse data*](how-to-interpret-event-data.md)
