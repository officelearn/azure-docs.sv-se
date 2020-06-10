---
title: Tolka händelse data
titleSuffix: Azure Digital Twins
description: Se hur du tolkar olika händelse typer och deras olika meddelanden.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4c95e686de23654688d0d7c3182c6565a907b750
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612965"
---
# <a name="understand-event-data"></a>Förstå händelse data

Olika händelser i digitala Azure-meddelanden genererar **aviseringar**, vilket gör att lösningens Server del kan vara medveten när olika åtgärder sker. De [dirigeras](concepts-route-events.md) sedan till olika platser inuti och utanför Azures digitala dubbla, som kan använda den här informationen för att vidta åtgärder.

Det finns flera typer av meddelanden som kan genereras, och meddelanden kan se olika ut beroende på vilken typ av händelse som genererade dem. Den här artikeln innehåller information om olika typer av meddelanden och hur de kan se ut.

Det här diagrammet visar olika meddelande typer:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

I allmänhet består meddelanden av två delar: sidhuvud och brödtext. 

### <a name="event-notification-headers"></a>Händelse meddelande rubriker

Meddelande rubriker representeras med nyckel/värde-par. Beroende på vilket protokoll som används (MQTT, AMQP eller HTTP) kommer meddelandehuvuden att serialiseras på olika sätt. I det här avsnittet beskrivs allmän rubrik information för aviserings meddelanden oavsett vilket protokoll och vilka serialiseringar som valts.

Vissa meddelanden uppfyller CloudEvents-standarden. CloudEvents-överensstämmelse är som följer.
* Meddelanden som släpps från enheter fortsätter att följa de befintliga specifikationerna för meddelanden
* Meddelanden som bearbetas och genereras av IoT Hub fortsätta att följa de befintliga specifikationerna för meddelanden, förutom där IoT Hub väljer att stödja CloudEvents, till exempel via Event Grid
* Meddelanden som släpps från [digitala garn](concepts-twins-graph.md) med en [modell](concepts-models.md) som överensstämmer med CloudEvents
* Meddelanden som bearbetas och genereras av Azure Digitals dubblare följer CloudEvents

Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. Meddelanden som skickas av digitala Digital-meddelanden i Azure till Event Grid formateras i Event Grid schemat tills Event Grid har stöd för CloudEvents. Tilläggets attribut för rubriker läggs till som egenskaper i Event Grid-schemat i nytto lasten. 

### <a name="event-notification-bodies"></a>Händelse meddelande texter

Organen i aviserings meddelanden beskrivs här i JSON. Beroende på vilken serialisering som önskas för meddelande texten (till exempel med JSON, CBOR, protobuf osv.) kan meddelande texten serialiseras på olika sätt.

Uppsättningen fält som texten innehåller varierar beroende på olika meddelande typer. Här är två exempel på meddelande texter, för att få en uppfattning om vad de vanligt vis ser ut och kan innehålla.

Telemetri-meddelande:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.iot.telemetry", 
    "source": "myhub.westus2.azuredigitaltwins.net", 
    "subject": "thermostat.vav-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a",
    "dataschema": "dtmi:com:contoso:DigitalTwins:VAV;1",
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "data":  
      {
          "temp": 70,
          "humidity": 40 
      }
}
```

Meddelande om livs cykel meddelande:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.digitaltwins.twin.create", 
    "source": "mydigitaltwins.westus2.azuredigitaltwins.net", 
    "subject": "device-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a", 
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "dataschema": "dtmi:com:contoso:DigitalTwins:Device;1",           
    "data":  
      { 
        "$dtId": "room-123", 
        "property": "value",
        "$metadata": { 
                //...
        } 
      } 
}
```

## <a name="message-format-detail-for-different-event-types"></a>Meddelande format information för olika händelse typer

I det här avsnittet får du mer information om de olika typerna av meddelanden som genereras av IoT Hub och Azure Digitals dubbla (eller andra Azure IoT-tjänster). Du kommer att läsa om de saker som utlöser varje meddelande typ och de fält som ingår i varje typ av meddelande text.

### <a name="digital-twin-life-cycle-notifications"></a>Digitala dubbla livs cykel meddelanden

Alla [digitala](concepts-twins-graph.md) meddelanden om generering av digitala meddelanden, oavsett om de representerar [IoT Hub enheter i Azure Digitals](how-to-ingest-iot-hub-data.md) eller inte. Detta beror på ett **livs cykel meddelande**, som är cirka det digitala företaget.

Meddelanden om livs cykel utlöses när:
* En Digitals fläta skapas
* En Digitals fläta tas bort

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett meddelande om livs cykel.

| Name | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id`är unikt för varje distinkt händelse. |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Create`<br>`Microsoft.DigitalTwins.TwinProxy.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Attach`<br>`Microsoft.DigitalTwins.TwinProxy.Detach` |
| `datacontenttype` | application/json |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den dubbla |
| `sequence` | Värde som uttrycker händelsens position i den större sorterade sekvensen av händelser. Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. Serie numret ökar med varje meddelande. Det kommer att återställas till 1 om objektet tas bort och återskapas med samma ID. |
| `sequencetype` | Mer information om hur fältet Sequence används. Den här egenskapen kan till exempel ange att värdet måste vara ett signerat 32-bitars heltal som börjar vid 1 och ökar med 1 varje tillfälle. |

#### <a name="body-details"></a>Information om brödtext

Texten är den digitala dubbla, som visas i JSON-format. Schemat för det här är *Digitals sammanflätade resurs 7,1*.

För att skapa händelser visar nytto lasten statusen för den dubbla efter att resursen har skapats, så den bör innehålla alla systemgenererade element precis som ett `GET` anrop.

Här är ett exempel på en brödtext för en [IoT plug and Play-enhet (PNP)](../iot-pnp/overview-iot-plug-and-play.md) , med komponenter och inga egenskaper på den högsta nivån. Egenskaper som inte passar för enheter (t. ex. rapporterade egenskaper) ska utelämnas.

```json
{
  "$dtId": "device-digitaltwin-01",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Här är ett annat exempel på en digital, dubbel. Den här baseras på en [modell](concepts-models.md)och stöder inte komponenter:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "$kind": "DigitalTwin",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Digitala meddelanden om ändring av dubbla relationer

**Aviseringar om Relations ändringar** utlöses när en relation av en Digitals Tor skapas, uppdateras eller tas bort. 

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten för en Edge Change-avisering.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id`är unikt för varje distinkt händelse |
| `source` | Namnet på den digitala Azure-instansen, till exempel *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`<br>`datacontenttype    application/json for Relationship.Create`<br>`application/json-patch+json for Relationship.Update` |
| `subject` | ID för relationen, t. ex.`<twinID>/relationships/<relationshipName>/<edgeID>` |
| `time` | Tidsstämpel för när åtgärden utfördes för relationen |
| `sequence` | Värde som uttrycker händelsens position i den större sorterade sekvensen av händelser. Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. Serie numret ökar med varje meddelande. Det kommer att återställas till 1 om objektet tas bort och återskapas med samma ID. |
| `sequencetype` | Mer information om hur fältet Sequence används. Den här egenskapen kan till exempel ange att värdet måste vara ett signerat 32-bitars heltal som börjar vid 1 och ökar med 1 varje tillfälle. |

#### <a name="body-details"></a>Information om brödtext

Texten är nytto lasten i en relation, även i JSON-format. Den använder samma format som en `GET` begäran om en relation via DigitalTwins- [API: et](how-to-use-apis-sdks.md). 

"Uppdatering av en relation" innebär att egenskaper för relationen har ändrats. 

Här är ett exempel på ett uppdaterings Relations meddelande för att uppdatera en egenskap:

```json
[
  {
    "op": "replace",
    "path": "ownershipUser",
    "value": "user3"
  }
]
```

För `Relationship.Delete` är texten samma som `GET` begäran, och den hämtar det senaste läget innan det tas bort.

Här är ett exempel på ett meddelande om att skapa eller ta bort relationer:

```json
{
    "$relationshipId": "EdgeId1",
    "$sourceId": "building11",
    "$relationshipName": "Contains",
    "$targetId": "floor11",
    "ownershipUser": "user1",
    "ownershipDepartment": "Operations"
}
```

### <a name="digital-twin-model-change-notifications"></a>Digitala meddelanden om ändring av dubbla modeller

**Aviseringar om modell ändringar** utlöses när en digital DTDL- [modell](concepts-models.md) (endefinierad Definition Language) laddas upp, läses in, korrigeras, tas ur bruk eller tas bort.

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i en modell ändrings avisering.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id`är unikt för varje distinkt händelse |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Model.Upload`<br>`Microsoft.DigitalTwins.Model.Reload`(NAV-Specific)<br>`Microsoft.DigitalTwins.Model.Patch`(NAV-Specific)<br>`Microsoft.DigitalTwins.Model.Decom`<br>`Microsoft.DigitalTwins.Model.Delete` |
| `datacontenttype` | application/json |
| `subject` | ID för modellen i formatet`dtmi:<domain>:<unique model identifier>;<model version number>` |
| `time` | Tidsstämpel för när åtgärden utfördes på modellen |
| `sequence` | Värde som uttrycker händelsens position i den större sorterade sekvensen av händelser. Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. Serie numret ökar med varje meddelande. Det kommer att återställas till 1 om objektet tas bort och återskapas med samma ID. |
| `sequencetype` | Mer information om hur fältet Sequence används. Den här egenskapen kan till exempel ange att värdet måste vara ett signerat 32-bitars heltal som börjar vid 1 och ökar med 1 varje tillfälle. |
| `modelstatus` | Lösnings status för att lösa en modell. Möjliga värden: lyckades/NotFound/misslyckades (endast IoT Hub) | 
| `updatereason` | Uppdatera modell orsak i schemat. Möjliga värden: skapa/Återställ/Åsidosätt (endast IoT Hub) | 

#### <a name="body-details"></a>Information om brödtext

Det finns ingen meddelande text för åtgärder vid överföring, inläsning och uppdatering av modeller. Användaren måste göra ett `GET` anrop för att hämta modell innehållet. 

För och `Model.Decom` , kommer bröd texten i korrigeringen att vara i JSON patch-format, som alla andra korrigerings-API: er i Azure Digitals API-yta. Om du vill inaktivera en modell använder du:

```json
[
  {
    "op": "replace",
    "path": "/decommissionedState",
    "value": true
  }
]
```

För `Model.Delete` , är begär ande texten densamma som en `GET` begäran och den hämtar det senaste läget innan det tas bort.

### <a name="digital-twin-change-notifications"></a>Digitala dubbla ändrings meddelanden

**Digitala dubbla ändrings meddelanden** utlöses när en Digitals Tor håller på att uppdateras, t. ex.:
* När egenskaps värden eller metadata ändras.
* När digitala dubbla eller komponent-metadata ändras. Ett exempel på det här scenariot är att ändra modellen för en digital, dubbel.

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett digitalt meddelande om ändring av dubbla meddelanden.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id`är unikt för varje distinkt händelse |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | Application/JSON-patch + JSON |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den digitala, dubbla |
| `sequence` | Värde som uttrycker händelsens position i den större sorterade sekvensen av händelser. Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. Serie numret ökar med varje meddelande. Det kommer att återställas till 1 om objektet tas bort och återskapas med samma ID. |
| `sequencetype` | Mer information om hur fältet Sequence används. Den här egenskapen kan till exempel ange att värdet måste vara ett signerat 32-bitars heltal som börjar vid 1 och ökar med 1 varje tillfälle. |

#### <a name="body-details"></a>Information om brödtext

Bröd texten för `Twin.Update` meddelandet är ett JSON-dokument som innehåller uppdateringen av den digitala, dubbla.

Anta till exempel att en digital, dubbel har uppdaterats med följande korrigering.

```json
[
  {
    "op": "replace",
    "path": "/mycomp/prop1",
    "value": {"a":3}
  }
]
```

Motsvarande meddelande (om synkront utförd av tjänsten, t. ex. Azure Digitals, som uppdaterar en digital enhet), har en brödtext som:

```json
[
    { "op": "replace", "path": "/myComp/prop1", "value": {"a": 3}},
    { "op": "replace", "path": "/myComp/$metadata/prop1",
        "value": {
            "desiredValue": { "a": 3 },
            "desiredVersion": 2,
                "ackCode": 200,
            "ackVersion": 2 
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

Se så här skapar du slut punkter och vägar för att leverera händelser:
* [Anvisningar: hantera slut punkter och vägar](how-to-manage-routes.md)

Du kan också läsa mer om Azure Digitals dubbla API: er och SDK-alternativ:
* [Anvisningar: använda Azures digitala dubbla API: er och SDK: er](how-to-use-apis-sdks.md)