---
title: Databearbetning och användardefinierade funktioner med Azure Digital Twins | Microsoft Docs
description: Översikt över databearbetning, matchers och användardefinierade funktioner med Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531750"
---
# <a name="data-processing-and-user-defined-functions"></a>Databearbetning och användardefinierade funktioner

Azure Digital Twins erbjuder avancerade beräkning. Utvecklare kan definiera och köra egna funktioner mot inkommande telemetrimeddelanden att skicka händelser till fördefinierade slutpunkter.

## <a name="data-processing-flow"></a>Bearbetning av flöde

När enheterna skickar telemetridata till Azure Digital Twins, utvecklare kan bearbeta data i fyra faser: *Validera*, *matchar*, *compute*, och *dispatch* .

![Azure Digital Twins bearbetning av flöde][1]

1. Verifiera fas omvandlar inkommande telemetri-meddelande till en lätt igenkännliga [dataöverföringsobjekt](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) format. Det här steget körs också verifiering av enheten och sensorn.
1. Matchning fasen hittar de användardefinierade funktionerna ska köras. Fördefinierade matchers hitta de användardefinierade funktioner baserat på enheten, sensor och information om diskutrymme från det inkommande meddelandet telemetri.
1. Compute-fas körs de användardefinierade funktioner som matchas i det föregående steget. Dessa funktioner kan läsa och uppdatera beräknade värden på spatial graph noder och kan generera anpassade meddelanden.
1. Fasen dispatch dirigerar alla anpassade meddelanden från fasen beräkning till slutpunkter som definierats i diagrammet.

## <a name="data-processing-objects"></a>Bearbetning av objekt

Databearbetning i Azure Digital Twins genom att definiera tre objekt: *matchers*, *användardefinierade funktioner*, och *rolltilldelningar*.

![Azure Digital Twins bearbetning av objekt][2]

<div id="matcher"></div>

### <a name="matchers"></a>Matchers

Matchers definierar en uppsättning villkor som utvärderas vad äga rum baserat på inkommande sensor telemetri. Villkor för att bestämma matchningen kan innehålla egenskaper från sensorn, den sensorn överordnade enheter och den sensorn överordnade utrymme. Villkor som är uttryckt i jämförelser mot en [JSON-sökvägen](https://jsonpath.com/) som beskrivs i det här exemplet:

- Alla sensorer på datatype **temperatur** representeras av strängvärdet undantagna (escaped) `\"Temperature\"`
- Med `01` i sina port
- Som hör till enheter med den utökade Egenskapsnyckeln **tillverkare** inställd strängvärdet undantagna (escaped) `\"GoodCorp\"`
- Som tillhör blanksteg av typen som angetts av strängen som hoppats över `\"Venue\"`
- Som är underordnade överordnade **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

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
> - JSON-nyttolasten är samma som den nyttolast som returneras av:
>   - `/sensors/{id}?includes=properties,types` för sensorn.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` för den sensorn överordnade enheter.
>   - `/spaces/{id}?includes=properties,types,location,timezone` för den sensorn överordnade utrymme.
> - Jämförelser är skiftlägeskänsliga.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

En användardefinierad funktion är en anpassad funktion som körs i en isolerad Azure Digital Twins-miljö. Användardefinierade funktioner ha åtkomst till rå sensor telemetrimeddelanden som hämtar togs emot. Användardefinierade funktioner har också åtkomst till spatial graph och dispatcher-tjänsten. När den användardefinierade funktionen har registrerats i en graf, en matcher (detaljerade [ovan](#matcher)) måste skapas om du vill ange när funktionen körs. När Azure Digital Twins tar emot nya telemetri från en viss sensor, till exempel beräkna matchade användardefinierad funktion ett glidande medelvärde för de senaste få sensoravläsningar.

Användardefinierade funktioner kan skrivas i JavaScript. Hjälpmetoder interagera med graph i en användardefinierad körningsmiljön. Utvecklare kan köra anpassade fragment för kod mot sensor telemetrimeddelanden. Exempel:

- Ange sensorn läser direkt till sensorn objekt i diagrammet.
- Utföra en åtgärd baserat på olika sensoravläsningar inom ett blanksteg i diagrammet.
- Skapa ett meddelande när vissa villkor uppfylls för en inkommande sensor läsning.
- Koppla graph metadata till sensorn läsning innan du skickar ut ett meddelande.

Mer information finns i [hur du använder användardefinierade funktioner](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Exempel

Den [GitHub-lagringsplatsen för Digital Twins C# exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp/) innehåller några exempel på användardefinierade funktioner:
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) söker efter koldioxid rörelse och temperaturvärden att avgöra om ett rum är tillgängliga med dessa värden i intervallet. Den [självstudier för Digital Twins](tutorial-facilities-udf.md) Utforska den här funktionen i mer information. 
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) söker efter data från flera rörelsesensorer och avgör att området är tillgänglig om ingen av dem kan identifiera eventuella rörelse. Du kan enkelt ersätta den användardefinierade funktionen som används i antingen den [Snabbstart](quickstart-view-occupancy-dotnet.md), eller [självstudier](tutorial-facilities-setup.md), genom att göra de ändringar som nämns i kommentarsavsnittet i filen. 



### <a name="role-assignment"></a>Rolltilldelning

En användardefinierad funktion åtgärder omfattas Azure Digital Twins [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) att skydda data i tjänsten. Rolltilldelningar definierar vilka användardefinierade funktioner har rätt behörighet för att interagera med spatial diagrammet och entiteter. En användardefinierad funktion kan till exempel ha möjligheten och behörighet att *skapa*, *läsa*, *uppdatering*, eller *ta bort* diagramdata under en viss utrymme. En användardefinierad funktion åtkomstnivå kontrolleras när den användardefinierade funktionen efterfrågar data i diagrammet eller försöker en åtgärd. Mer information finns i [rollbaserad åtkomstkontroll](./security-create-manage-role-assignments.md).

Det är möjligt för en matcher att utlösa en användardefinierad funktion som har inga rolltilldelningar. I det här fallet den användardefinierade funktionen kan inte läsa data från diagrammet.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du dirigera händelser och telemetrimeddelanden till andra Azure-tjänster [dirigera händelser och meddelanden](./concepts-events-routing.md).

- Mer information om hur du skapar matchers, användardefinierade funktioner och rolltilldelningar [Guide för att använda användardefinierade funktioner](./how-to-user-defined-functions.md).

- Granska den [referensdokumentation för användardefinierad funktion klienten biblioteket](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
