---
title: Databearbetning och användardefinierade funktioner med Azure Digital Twins | Microsoft Docs
description: Översikt över databearbetning, matchers och användardefinierade funktioner med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210133"
---
# <a name="data-processing-and-user-defined-functions"></a>Databearbetning och användardefinierade funktioner

Azure Digital Twins erbjuder avancerade beräkning. Utvecklare kan definiera och köra egna funktioner mot inkommande telemetrimeddelanden att skicka händelser till fördefinierade slutpunkter.

## <a name="data-processing-flow"></a>Bearbetning av flöde

När enheterna skickar telemetridata till Digital Twins, utvecklare kan bearbeta data i fyra faser: _Validera_, _matchar_, _compute_, och _dispatch_:

![Digitala Twins bearbetning av flöde][1]

1. Den _Validera_ fas omvandlar inkommande telemetri-meddelande till en lätt igenkännliga [ **dataöverföringsobjekt** ](https://en.wikipedia.org/wiki/Data_transfer_object) format. Det här steget körs också verifiering av enheten och sensorn.
1. Den _matchar_ fas söker efter den lämpliga användardefinierade funktioner ska köras. Fördefinierade matchers hittar användardefinierade funktioner baserat på enheten, sensor och information om diskutrymme från det inkommande meddelandet telemetri.
1. Den _compute_ fas körs den användardefinierade funktioner som matchas i det föregående steget. Dessa funktioner kan läsa och uppdatera beräknade värden på spatial diagramnoder och kan skapa anpassade meddelanden.
1. Den _dispatch_ fas dirigerar alla anpassade meddelanden från fasen beräkning till slutpunkter som definierats i diagrammet.

## <a name="data-processing-objects"></a>Bearbetning av objekt

Databearbetning i Azure Digital Twins genom att definiera tre objekt: _matchers_, _användardefinierade funktioner_, och _rolltilldelningar_:

![Digitala Twins bearbetning av flöde][2]

### <a name="matchers"></a>Matchers

_Matchers_ definierar en uppsättning villkor som utvärdera vilka åtgärder ska äga rum baserat på inkommande sensor telemetri. Dessa villkor för att bestämma matchningen kan omfatta egenskaper från sensorn, den sensorn överordnade enheter och den sensorn överordnade utrymme. Villkor som är uttryckt i jämförelser mot en [JSON-sökvägen](http://jsonpath.com/) som beskrivs i exemplet nedan:

- Alla sensorer på datatype **temperatur**.
- Med `01` i sina port.
- Som hör till enheter med den utökade Egenskapsnyckeln **tillverkare** inställd på värdet `"GoodCorp"`.
- Som tillhör blanksteg av typen `"Venue"`.
- Som är underordnade överordnade **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON-sökvägar är skiftlägeskänsliga.
> - JSON-nyttolasten är samma som den nyttolast som returneras via:
>   - `/sensors/{id}?includes=properties,types` för sensorn.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` för den sensorn överordnade enheter.
>   - `/spaces/{id}?includes=properties,types,location,timezone` för den sensorn överordnade utrymme.
> - Jämförelser är skiftlägeskänsliga.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

En _användardefinierad funktion_, eller _UDF_, en egen funktion som körs i en isolerad miljö i Azure Digital Twins. Användardefinierade funktioner ha åtkomst till både på raw sensor telemetrimeddelanden som det togs emot, även om tjänsten spatial graph och dispatcher. När en användardefinierad funktion är registrerad i diagrammet, måste du skapa en matcher (se ovan) om du vill ange när du ska köra en användardefinierad funktion. När digitala Twins tar emot nya telemetri från en viss sensor, beräkna matchade UDF ett glidande medelvärde för de senaste få sensoravläsning, till exempel.

Användardefinierade funktioner kan vara skriven i JavaScript och att utvecklare kan köra anpassad kod mot sensor telemetrimeddelanden-kodfragment. Det finns även hjälpmetoder att interagera med graph i en användardefinierad körningsmiljön. Med en UDF göra utvecklare följande:

- Ange sensorn läser direkt till sensorn objekt i diagrammet.
- Utföra en åtgärd baserat på olika sensoravläsningar inom ett blanksteg i diagrammet.
- Skapa ett meddelande när vissa villkor uppfylls för en inkommande sensor läsning.
- Koppla graph metadata till sensorn läsning innan du skickar ut ett meddelande.

Referera till [så User User-Defined Functions](how-to-user-defined-functions.md) för mer information.

### <a name="role-assignment"></a>Rolltilldelning

En UDF åtgärder omfattas digitala Twins rollbaserad åtkomstkontroll till att skydda data i tjänsten. Rolltilldelningar Kontrollera att en viss UDF har rätt behörighet för att interagera med spatial graph. Till exempel kan en UDF försöka skapa, läsa, uppdatera eller ta bort diagramdata under en viss utrymme. En UDF åtkomstnivå kontrolleras när en användardefinierad funktion efterfrågar data i diagrammet eller försöker en åtgärd. Mer information finns i [Role-Based Access Control](security-create-manage-role-assignments.md).

Det är möjligt för en matcher att utlösa en UDF som har inga rolltilldelningar. I det här fallet skulle UDF-filen kan inte läsa data från diagrammet.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du routning händelser och telemetrimeddelanden till andra Azure-tjänster [routning händelser och meddelanden](concepts-events-routing.md).

Mer information om hur du skapar matchers, användardefinierade funktioner och rolltilldelningar [Guide för att använda användardefinierade funktioner](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
