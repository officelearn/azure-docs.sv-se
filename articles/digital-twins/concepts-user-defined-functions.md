---
title: Databehandling och användardefinierade funktioner - Azure Digital Twins| Microsoft-dokument
description: Översikt över databearbetning, matchningar och användardefinierade funktioner med Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265186"
---
# <a name="data-processing-and-user-defined-functions"></a>Databearbetning och användardefinierade funktioner

Azure Digital Twins erbjuder avancerade beräkningsfunktioner. Utvecklare kan definiera och köra anpassade funktioner mot inkommande telemetrimeddelanden för att skicka händelser till fördefinierade slutpunkter.

## <a name="data-processing-flow"></a>Databehandlingsflöde

När enheter har skickat telemetridata till Azure Digital Twins kan utvecklare bearbeta data i fyra faser: *validera*, *matcha,* *beräkna*och *skicka*.

[![Azure Digital Twins databehandlingsflöde](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Valideringsfasen omvandlar det inkommande telemetrimeddelandet till ett allmänt förstått [objektformat för dataöverföring.](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) Den här fasen kör även enhets- och sensorvalidering.
1. Matchningsfasen hittar lämpliga användardefinierade funktioner som ska köras. Fördefinierade matchningars hitta användardefinierade funktioner baserat på enheten, sensorn och utrymmesinformation från det inkommande telemetrimeddelandet.
1. Beräkningsfasen kör de användardefinierade funktioner som matchas i föregående fas. Dessa funktioner kan läsa och uppdatera beräknade värden på rumsliga grafnoder och kan avge anpassade meddelanden.
1. Leveransfasen dirigerar alla anpassade meddelanden från beräkningsfasen till slutpunkter som definierats i diagrammet.

## <a name="data-processing-objects"></a>Objekt för databehandling

Databearbetning i Azure Digital Twins består av att definiera tre objekt: *matchningar,* *användardefinierade funktioner*och *rolltilldelningar*.

[![Databearbetningsobjekt för Azure Digital Twins](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchers (matcher)

Matchers definierar en uppsättning villkor som utvärderar vilka åtgärder som sker baserat på inkommande sensortelemetri. Villkor för att bestämma matchningen kan omfatta egenskaper från sensorn, sensorns överordnade enhet och sensorns överordnade utrymme. Villkoren uttrycks som jämförelser mot en [JSON-väg](https://jsonpath.com/) som beskrivs i det här exemplet:

- Alla sensorer av datatyp **Temperatur** representeras av det förrymda strängvärdet`\"Temperature\"`
- Att `01` ha i sin hamn
- Som tillhör enheter med den utökade egenskapsnyckeln **Tillverkare** inställd på det förrymde strängvärdet`\"Contoso\"`
- Som tillhör utrymmen av den typ som anges av den förrymde Strängen`\"Venue\"`
- Som är underordnade till överordnad **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
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
> - JSON-nyttolasten är densamma som nyttolasten som returneras av:
>   - `/sensors/{id}?includes=properties,types`för sensorn.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`för sensorns överordnade enhet.
>   - `/spaces/{id}?includes=properties,types,location,timezone`för sensorns föräldrautrymme.
> - Jämförelserna är skiftlägesokänsliga.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

En användardefinierad funktion är en anpassad funktion som körs i en isolerad Azure Digital Twins-miljö. Användardefinierade funktioner har tillgång till telemetrimeddelande för råsensor när det tas emot. Användardefinierade funktioner har också tillgång till den rumsliga grafen och avsändare tjänsten. När den användardefinierade funktionen har registrerats i ett diagram måste en matchning (som beskrivs [ovan)](#matchers)skapas för att ange när funktionen körs. När Azure Digital Twins till exempel tar emot ny telemetri från en viss sensor kan den matchade användardefinierade funktionen beräkna ett glidande medelvärde av de senaste sensoravläsningarna.

Användardefinierade funktioner kan skrivas i JavaScript. Hjälpmetoderna interagerar med diagrammet i den användardefinierade körningsmiljön. Utvecklare kan köra anpassade kodavsnitt mot sensortelemetrimeddelanden. Exempel på rekommendationer:

- Ställ in sensoravläsningen direkt på sensorobjektet i diagrammet.
- Utför en åtgärd baserat på olika sensoravläsningar inom ett utrymme i diagrammet.
- Skapa ett meddelande när vissa villkor är uppfyllda för en inkommande sensoravläsning.
- Bifoga grafmetadata till sensoravläsningen innan du skickar ut ett meddelande.

Mer information finns i [Så här använder du användardefinierade funktioner](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exempel

[GitHub-repo för Exemplet Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/) innehåller några exempel på användardefinierade funktioner:
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) letar efter koldioxid-, rörelse- och temperaturvärden för att avgöra om ett rum är tillgängligt med dessa värden inom räckhåll. [Självstudierna för Digital Twins](tutorial-facilities-udf.md) utforskar denna funktion mer detaljer. 
- [Den här funktionen](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) söker efter data från flera rörelsesensorer och bestämmer att utrymmet är tillgängligt om ingen av dem upptäcker någon rörelse. Du kan enkelt ersätta den användardefinierade funktion som används i antingen [snabbstart](quickstart-view-occupancy-dotnet.md)eller [självstudier](tutorial-facilities-setup.md), genom att göra de ändringar som nämns i kommentarerna i filen. 

### <a name="role-assignment"></a>Rolltilldelning

En användardefinierad funktions åtgärder omfattas av Azure Digital Twins [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) för att skydda data inom tjänsten. Rolltilldelningar definierar vilka användardefinierade funktioner som har rätt behörighet att interagera med det rumsliga diagrammet och dess entiteter. En användardefinierad funktion kan till exempel ha möjlighet och behörighet att *skapa*, *läsa*, *uppdatera*eller *ta bort* diagramdata under ett visst utrymme. En användardefinierad funktions åtkomstnivå kontrolleras när den användardefinierade funktionen frågar diagrammet efter data eller försöker en åtgärd. Mer information finns i [Rollbaserad åtkomstkontroll](./security-create-manage-role-assignments.md).

Det är möjligt för en matchning att utlösa en användardefinierad funktion som inte har några rolltilldelningar. I det här fallet kan den användardefinierade funktionen inte läsa några data från diagrammet.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur du dirigerar händelser och telemetrimeddelanden till andra Azure-tjänster läser du [Dirigera händelser och meddelanden](./concepts-events-routing.md).

- Om du vill veta mer om hur du skapar matchningar, användardefinierade funktioner och rolltilldelningar läser du [Guide för att använda användardefinierade funktioner](./how-to-user-defined-functions.md).

- Granska [referensdokumentationen för användardefinierat funktionsklientbibliotek](./reference-user-defined-functions-client-library.md).
