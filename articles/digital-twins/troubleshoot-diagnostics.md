---
title: Konfigurera diagnostik
titleSuffix: Azure Digital Twins
description: Se så här aktiverar du loggning med diagnostikinställningar.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 11a7b4876c773922d4b0ed28f7047912b738ee6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091743"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Felsöka Azure Digitals sammanflätade: diagnostikloggning

Azure Digitals flätar samlar in [Mät värden](troubleshoot-metrics.md) för din tjänst instans som ger information om statusen för dina resurser. Du kan använda dessa mått för att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den. Den här användaren får hjälp att se vad som händer med Azures digitala dubbla, och hjälper till med rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Den här artikeln visar hur du aktiverar **diagnostikloggning** för dina mått data från din Azure Digital-instansen. Du kan använda dessa loggar för att hjälpa dig att felsöka service problem och konfigurera diagnostikinställningar för att skicka Azure Digitals mått till olika mål. Du kan läsa mer om de här inställningarna i [*skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Aktivera diagnostikinställningar med Azure Portal

Så här aktiverar du diagnostikinställningar för din Azure Digital-instansen:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **diagnostikinställningar** på menyn och **Lägg sedan till en diagnostisk inställning** .

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

3. Fyll i följande värden på sidan som följer:
     * **Namn på diagnos inställning** : ge diagnostikinställningar ett namn.
     * **Kategori information** : Välj vilka åtgärder som du vill övervaka och markera kryss rutorna för att aktivera diagnostik för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Mer information om de här alternativen finns i avsnittet [*kategori information*](#category-details) nedan.
     * **Mål information** : Välj var du vill skicka loggarna. Du kan välja en valfri kombination av de tre alternativen:
        - Skicka till Log Analytics
        - Arkivera till ett lagringskonto
        - Strömma till en händelsehubb

        Du kan bli ombedd att fylla i ytterligare information om de behövs för val av mål.  
    
4. Spara de nya inställningarna. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet tillbaka på sidan **diagnostikinställningar** för din instans. 

## <a name="category-details"></a>Kategori information

Här finns mer information om de logg kategorier som kan väljas under **kategori information** när du ställer in diagnostikinställningar.

| Loggkategori | Beskrivning |
| --- | --- |
| ADTModelsOperation | Logga alla API-anrop som rör modeller |
| ADTQueryOperation | Logga alla API-anrop som rör frågor |
| ADTEventRoutesOperation | Logga alla API-anrop som hör till händelse vägar samt utgående av händelser från Azure Digitals dubbla till en slut punkts tjänst som Event Grid, Event Hubs och Service Bus |
| ADTDigitalTwinsOperation | Logga alla API-anrop som hör till Azure Digitals dubbla |

Varje logg kategori består av åtgärder av Skriv-, Läs-, borttagnings-och åtgärds typ.  Dessa mappar till REST API samtal på följande sätt:

| Händelsetyp | REST API åtgärder |
| --- | --- |
| Skriva | PLACERING och korrigering |
| Läs | GET |
| Ta bort | DELETE |
| Åtgärd | POST |

Här är en omfattande lista över åtgärderna och motsvarande [Azure Digital-meddelanden REST API samtal](/rest/api/azure-digitaltwins/) som loggas i varje kategori. 

>[!NOTE]
> Varje logg kategori innehåller flera åtgärder/REST API-anrop. I tabellen nedan mappas varje logg kategori till alla åtgärder/REST API samtal under den tills nästa logg kategori visas. 

| Loggkategori | Åtgärd | REST API-anrop och andra händelser |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modeller/Skriv | Uppdaterings-API för digitala dubbla modeller |
|  | Microsoft. DigitalTwins/modeller/läsa | Digitala dubbla modeller får av ID-och list-API: er |
|  | Microsoft. DigitalTwins/Models/Delete | Digitala dubbla modeller ta bort API |
|  | Microsoft. DigitalTwins/modeller/åtgärd | Digitala dubbla modeller Lägg till API |
| ADTQueryOperation | Microsoft. DigitalTwins/fråga/åtgärd | Frågans dubbla API |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Händelse vägar Lägg till API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Händelse vägar hämtas efter ID och lista API: er |
|  | Microsoft. DigitalTwins/eventroutes/Delete | Borttagnings-API för händelse vägar |
|  | Microsoft. DigitalTwins/eventroutes/Action | Ett problem uppstod vid försök att publicera händelser till en slut punkts tjänst (inte ett API-anrop) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitals, Lägg till, Lägg till relation, uppdatera, uppdatera komponent |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digitals, Hämta efter ID, Hämta komponent, Hämta relation efter ID, lista inkommande relationer, list relationer |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Digitals ta bort, ta bort relation |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Digitala dubblare skicka telemetri för komponent, skicka telemetri |

## <a name="log-schemas"></a>Logg scheman 

Varje logg kategori har ett schema som definierar hur händelser i den kategorin rapporteras. Varje enskild loggpost lagras som text och formateras som en JSON-blob. Fälten i loggen och exempel på JSON-kroppar anges för varje loggtyp nedan. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` , och `ADTQueryOperation` använder ett konsekvent API-logg schema, `ADTEventRoutesOperation` har ett eget separat schema.

### <a name="api-log-schemas"></a>API-logg scheman

Det här logg schemat är konsekvent för `ADTDigitalTwinsOperation` , `ADTModelsOperation` och `ADTQueryOperation` . Den innehåller information som är relevant för API-anrop till en digital Azure-instans.

Här är fält-och egenskaps beskrivningarna för API-loggar.

| Fältnamn | Datatyp | Beskrivning |
|-----|------|-------------|
| `Time` | DateTime | Datum och tid då händelsen inträffade, i UTC |
| `ResourceID` | Sträng | Azure Resource Manager resurs-ID för resursen där händelsen ägde rum |
| `OperationName` | Sträng  | Typ av åtgärd som utförs under händelsen |
| `OperationVersion` | Sträng | Den API-version som används under händelsen |
| `Category` | Sträng | Typ av resurs som genereras |
| `ResultType` | Sträng | Händelsens resultat |
| `ResultSignature` | Sträng | HTTP-statuskod för händelsen |
| `ResultDescription` | Sträng | Ytterligare information om händelsen |
| `DurationMs` | Sträng | Hur lång tid det tog att utföra händelsen i millisekunder |
| `CallerIpAddress` | Sträng | En maskerad käll-IP-adress för händelsen |
| `CorrelationId` | GUID | Kunden tillhandahöll en unik identifierare för händelsen |
| `Level` | Sträng | Händelsens allvarlighets grad |
| `Location` | Sträng | Den region där händelsen ägde rum |
| `RequestUri` | URI | Slut punkten som används under händelsen |

Nedan visas exempel på JSON-instanser för dessa typer av loggar.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Utgående logg scheman

Detta är schemat för `ADTEventRoutesOperation` loggar. Dessa innehåller information som rör undantag och API-åtgärder kring utgående slut punkter som är anslutna till en Azure Digitals-instans.

|Fältnamn | Datatyp | Beskrivning |
|-----|------|-------------|
| `Time` | DateTime | Datum och tid då händelsen inträffade, i UTC |
| `ResourceId` | Sträng | Azure Resource Manager resurs-ID för resursen där händelsen ägde rum |
| `OperationName` | Sträng  | Typ av åtgärd som utförs under händelsen |
| `Category` | Sträng | Typ av resurs som genereras |
| `ResultDescription` | Sträng | Ytterligare information om händelsen |
| `Level` | Sträng | Händelsens allvarlighets grad |
| `Location` | Sträng | Den region där händelsen ägde rum |
| `EndpointName` | Sträng | Namnet på den utgående slut punkten som skapades i Azure Digitals dubbla |

Nedan visas exempel på JSON-instanser för dessa typer av loggar.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar diagnostik finns i [*samla in och använda loggdata från dina Azure-resurser*](../azure-monitor/platform/platform-logs-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
* Information om hur du aktiverar aviseringar för dina mått finns i [*fel sökning: Konfigurera aviseringar*](troubleshoot-alerts.md).