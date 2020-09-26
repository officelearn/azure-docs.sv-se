---
title: Använda egenskaper i en Azure IoT Central-lösning
description: Använda skrivskyddade och skrivbara egenskaper i Azure IoT Central-lösningen
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346965"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Använda egenskaper i en Azure IoT Central-lösning

Den här artikeln visar hur du använder enhets egenskaper som definierats i en enhets mall i ditt Azure IoT Central-program.

Egenskaperna representerar tidpunkts värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Med egenskaper kan du också synkronisera tillstånd mellan enheten och IoT Central programmet.  Du kan ange skrivbara egenskaper från IoT Central.

Du kan också definiera moln egenskaper i ett IoT Central-program. Moln egenskaps värden utbyts aldrig med en enhet och omfattas inte av omfånget för den här artikeln.

## <a name="define-your-properties"></a>Definiera dina egenskaper

Egenskaper är data fält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens varaktiga tillstånd, till exempel vid en enhets tillstånd. Egenskaper kan också representera grundläggande enhets egenskaper, till exempel program varu versionen av enheten. Du kan deklarera egenskaper som skrivskyddade eller skrivbara.

Följande skärm bild visar en egenskaps definition i Azure IoT Central Application

![Definiera egenskap](./media/howto-use-properties/property-definition.png)

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Fält           | Beskrivning                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visningsnamn    | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär.                                                                                                                                                              |
| Name            | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt.                                                 |
| Typ av kapacitet | Immaterialrätt.                                                                                                                                                                                                                          |
| Semantisk typ   | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga.                                                                       |
| Schema          | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd.                                               |
| Skrivbar       | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten. |
| Allvarlighetsgrad        | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information**eller **Varning**.                                                                                                                         |
| Tillstånds värden    | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde.                                                                                   |
| Enhet            | En enhet för egenskap svärdet, till exempel **mph**, **%** eller ** &deg; C**.                                                                                                                                                              |
| Visa enhet    | En visnings enhet för användning på instrument paneler och formulär.                                                                                                                                                                                    |
| Kommentar         | Kommentarer om egenskaps funktionen.                                                                                                                                                                                        |
| Description     | En beskrivning av egenskaps funktionen.                                                                                                                                                                                          |

Egenskaperna kan också definieras i ett gränssnitt i en enhets mall enligt nedan:

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

I det här exemplet visas fem egenskaper, som kan vara relaterade till egenskaps definitionen i användar gränssnittet enligt nedan:

* `@type` Ange typ av funktion: `Property`
* `name` för egenskap svärdet.
* `schema` Ange egenskapens datatyp. Det här värdet kan vara en primitiv typ, till exempel Double, Integer, Boolean eller String. Komplexa objekt typer, matriser och Maps stöds också.
* `writable` Som standard är egenskaperna skrivskyddade. Du kan markera en egenskap som skrivbar, genom att använda det här fältet

Valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

När du skapar en egenskap kan du ange de komplexa **schema** typerna som objekt, Fasttext osv.

![Lägg till en funktion](./media/howto-use-properties/property.png)

När du väljer det komplexa schemat, till exempel **objekt**, måste du även definiera objektet.

![Definiera objekt](./media/howto-use-properties/object.png)

Följande kod visar definitionen av en objekt egenskaps typ. Det här objektet har två fält med typerna sträng och heltal:

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementera skrivskyddade egenskaper

Som standard är egenskaperna skrivskyddade. Skrivskyddade egenskaper innebär att enhets rapportens egenskaps värde uppdateras till ditt IoT Central-program. Ditt IoT Central program kan inte ange värdet för en skrivskyddad egenskap.

IoT Central använder enheten för att synkronisera egenskaps värden mellan enheten och IoT Central programmet. Enhetens egenskaps värden använder enhetens dubbla rapporterade egenskaper. Mer information finns i [enhets uppflätade](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

Följande kodfragment från en enhets kapacitets modell visar definitionen av en skrivskyddad egenskaps typ:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Skrivskyddade egenskaper skickas av enheten till IoT Central. Egenskaperna skickas som JSON-nyttolast, mer information finns i [nytto laster](./concepts-telemetry-properties-commands.md).

Du kan använda Azure IoT-enhetens SDK för att skicka en egenskaps uppdatering till ditt IoT Central-program.

Enhetens dubbla egenskaper kan skickas till ditt Azure IoT Central-program med hjälp av funktionen nedan:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Den här artikeln använder Node.js för enkelhetens skull, för fullständig information om enhets program exempel, se avsnittet [skapa och ansluta ett klient program till ditt azure IoT Central-program (Node.js)](tutorial-connect-device-nodejs.md) och [skapa och Anslut ett klient program till dina Azure IoT Central program (python)](tutorial-connect-device-python.md) -självstudier.

I följande vy i Azure IoT Central program visas egenskaperna. du kan se vyn automatiskt gör att enhets modell egenskapen är en _skrivskyddad enhets egenskap_.

![Vy över skrivskyddad egenskap](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementera skrivbara egenskaper

Skrivbara egenskaper anges av en operator i IoT Central programmet i ett formulär. IoT Central skickar egenskapen till enheten. IoT Central förväntar sig en bekräftelse från enheten. 

Följande kodfragment från en enhets kapacitets modell visar definitionen av en skrivbar egenskaps typ:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

``` json
{ "Brightness Level": 2 }
```

Om du vill definiera och hantera de skrivbara egenskaper som enheten svarar på kan du använda följande kod.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Svars meddelandet ska innehålla `ac` fälten och `av` . Fältet `ad` är valfritt. Se följande kodfragment för exempel.

* `ac` är ett numeriskt fält som använder värdena i följande tabell:

* `av` är versions numret som skickas till enheten.

* `ad` är en beskrivning av alternativ strängen.

| Värde | Etikett | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Slutförd | Egenskaps ändrings åtgärden har slutförts. |
| `'ac': 202`  eller `'ac': 201` | Väntar | Egenskaps ändrings åtgärden väntar eller pågår |
| `'ac': 4xx` | Fel | Den begärda egenskaps ändringen var inte giltig eller innehöll ett fel |
| `'ac': 5xx` | Fel | Ett oväntat fel uppstod i enheten vid bearbetning av den begärda ändringen. |


Mer information finns i [enhets uppflätade](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

När operatorn ställer in en skrivbar egenskap i IoT Central-programmet använder programmet en enhet med dubbla önskade egenskaper för att skicka värdet till enheten. Enheten svarar sedan med en enhets dubbla rapporterad egenskap. När IoT Central tar emot det rapporterade egenskap svärdet uppdateras egenskaps läget med statusen **accepterad**.

I följande vy visas skrivbara egenskaper. När du anger värdet och **sparar**, är den ursprungliga statusen **väntande**, när enheten accepterar ändringen ändras statusen till **accepterad**.

![Väntande status](./media/howto-use-properties/status-pending.png)

![Godkänd egenskap](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder egenskaper i ditt Azure IoT Central-program kan du se [nytto laster](concepts-telemetry-properties-commands.md) och [skapa och ansluta ett klient program till ditt Azure IoT Central-program (Node.js)](tutorial-connect-device-nodejs.md).
