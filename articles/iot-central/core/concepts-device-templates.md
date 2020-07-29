---
title: Vad är Device templates i Azure IoT Central | Microsoft Docs
description: Med Azure IoT Central Device templates kan du ange beteendet för de enheter som är anslutna till ditt program.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1eab1022e9b0f03597c108c3c927909aa9bb2712
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337117"
---
# <a name="what-are-device-templates"></a>Vad är enhetsmallar?

_Den här artikeln gäller enhets utvecklare och Solution Builder._

En enhets mall i Azure IoT Central är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ditt program. Till exempel definierar enhets mal len den telemetri som en enhet skickar så att IoT Central kan skapa visualiseringar som använder rätt enheter och data typer.

En Solution Builder lägger till enhets mallar i ett IoT Central-program. En enhets utvecklare skriver enhets koden som implementerar de beteenden som definierats i enhets mal len.

En enhets mall innehåller följande avsnitt:

- _En enhets funktions modell (DCM)_. Den här delen av enhets mal len definierar hur enheten interagerar med ditt program. En enhets utvecklare implementerar beteenden som definierats i DCM.
- _Moln egenskaper_. I den här delen av enhets mal len kan utvecklare av lösningar ange alla enhets-metadata som ska lagras. Moln egenskaper synkroniseras aldrig med enheter och finns bara i programmet. Moln egenskaper påverkar inte den kod som en enhets utvecklare skriver för att implementera DCM.
- _Anpassningar_. Den här delen av enhets mal len gör att lösningen-utvecklare kan åsidosätta några av definitionerna i DCM. Anpassningar är användbara om lösnings utvecklaren vill förfina hur programmet hanterar ett värde, t. ex. ändra visnings namnet för en egenskap eller färgen som används för att visa ett telemetri-värde. Anpassningar påverkar inte den kod som en enhets utvecklare skriver för att implementera DCM.
- _Vyer_. Den här delen av enhets mal len gör att lösnings utvecklaren kan definiera visualiseringar för att visa data från enheten och formulär för att hantera och kontrol lera en enhet. Vyerna använder DCM, moln egenskaper och anpassningar. Vyer påverkar inte den kod som en enhets utvecklare skriver för att implementera DCM.

> [!NOTE]
> [Iot plug and Play offentlig för hands version uppdatera release](../../iot-pnp/overview-iot-plug-and-play.md) -målen enhets utvecklare och OEM-tillverkare för att börja bygga enheter som de kan certifiera för IoT plug and Play före ga-lanseringen.

## <a name="device-capability-models"></a>Modeller för enhetskapacitet

I DCM definieras hur en enhet interagerar med ditt IoT Central-program. Enhets utvecklaren måste se till att enheten implementerar de beteenden som definierats i DCM så att IoT Central kan övervaka och hantera enheten. Ett DCM-gränssnitt består av ett eller flera _gränssnitt_, och varje gränssnitt kan definiera en samling typer av _telemetri_ , _enhets egenskaper_och _kommandon_. En lösnings utvecklare kan importera en JSON-fil som definierar DCM till en enhets mall eller använda webb gränssnittet i IoT Central för att skapa eller redigera ett DCM. Ändringar i ett DCM-dokument som skapats med webb gränssnittet kräver att [enhets mal len har versions hantering](./howto-version-device-template.md).

En lösnings utvecklare kan också exportera en JSON-fil som innehåller DCM. En enhets utvecklare kan använda det här JSON-dokumentet för att förstå hur enheten ska kommunicera med IoT Central-programmet.

JSON-filen som definierar DCM använder sig av [DTDL (Digital Definition Language) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central förväntar sig att JSON-filen ska innehålla DCM med de gränssnitt som definierats i stället för separata filer.

En typisk IoT-enhet består av:

- Anpassade delar, vilka är de saker som gör din enhet unik.
- Standard delar, som är saker som är gemensamma för alla enheter.

Dessa delar kallas _gränssnitt_ i ett DCM. Gränssnitt definierar information om varje del din enhet implementerar. Gränssnitt är återanvändbara i DCMs.

I följande exempel visas dispositionen för enhets kapacitets modellen för en miljö sensor enhet med två gränssnitt:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

En kapacitets modell har vissa obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt enhetligt resurs namn.
- `@type`: deklarerar att det här objektet är en kapacitets modell.
- `@context`: anger den DTDL-version som används för kapacitets modellen.
- `implements`: visar de gränssnitt som enheten implementerar.

Varje post i listan över gränssnitt i avsnittet implementeringar har en:

- `name`: gränssnittets programmerings namn.
- `schema`: gränssnittets funktions modell implementeras.

Ett gränssnitt har vissa obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt enhetligt resurs namn.
- `@type`: deklarerar att objektet är ett gränssnitt.
- `@context`: anger den DTDL-version som används för gränssnittet.
- `contents`: visar de egenskaper, telemetri och kommandon som utgör din enhet.

Det finns några valfria fält som du kan använda för att lägga till mer information i kapacitets modellen, till exempel visnings namn och beskrivning.

### <a name="interface"></a>Gränssnitt

Med DTDL kan du beskriva enhetens funktioner. Relaterade funktioner grupperas i gränssnitt. Gränssnitt beskriver egenskaper, telemetri och kommandon som en del av din enhet implementerar:

- `Properties`. Egenskaper är data fält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens varaktiga tillstånd, till exempel vid inaktive ring av en kyl pump. Egenskaper kan också representera grundläggande enhets egenskaper, till exempel enhetens inbyggda program vara. Du kan deklarera egenskaper som skrivskyddade eller skrivbara.
- `Telemetry`. Telemetridata representerar mått från sensorer. När din enhet tar ett sensor mått bör den skicka en telemetri-händelse som innehåller sensor data.
- `Commands`. Kommandon representerar metoder som användarna av enheten kan köra på enheten. Till exempel ett återställnings kommando eller ett kommando för att aktivera eller inaktivera en fläkt.

I följande exempel visas definitionen av miljö sensor gränssnittet:

```json
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
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

I det här exemplet visas två egenskaper, en typ av telemetri och två kommandon. En minimal fält Beskrivning har:

- `@type`för att ange typen av funktion: `Telemetry` , `Property` , eller `Command` .  I vissa fall innehåller typen en semantisk typ som gör det möjligt för IoT Central att göra vissa antaganden om hur värdet ska hanteras.
- `name`för telemetri-värdet.
- `schema`för att ange data typen för Telemetrin eller egenskapen. Det här värdet kan vara en primitiv typ, till exempel Double, Integer, Boolean eller String. Komplexa objekt typer, matriser och Maps stöds också.
- `commandType`anger hur kommandot ska hanteras.

Valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

### <a name="properties"></a>Egenskaper

Som standard är egenskaperna skrivskyddade. Skrivskyddade egenskaper innebär att enhets rapportens egenskaps värde uppdateras till ditt IoT Central-program. Ditt IoT Central program kan inte ange värdet för en skrivskyddad egenskap.

Du kan också markera en egenskap som skrivbar i ett gränssnitt. En enhet kan ta emot en uppdatering av en skrivbar egenskap från ditt IoT Central-program samt att rapportera egenskaps värde uppdateringar till ditt program.

Enheter behöver inte vara anslutna för att ange egenskaps värden. De uppdaterade värdena överförs när enheten sedan ansluter till programmet. Det här beteendet gäller både skrivskyddade och skrivbara egenskaper.

Använd inte egenskaper för att skicka telemetri från enheten. En ReadOnly-egenskap, till exempel, `temperatureSetting=80` ska betyda att enhetens temperatur har angetts till 80 och att enheten försöker komma till eller stanna vid den här temperaturen.

För skrivbara egenskaper, returnerar enhets programmet en önskad status kod, version och beskrivning för att indikera om den har tagit emot och applicerat egenskap svärdet.

### <a name="telemetry"></a>Telemetri

Med IoT Central kan du Visa telemetri om instrument paneler och diagram och använda regler för att utlösa åtgärder när tröskelvärden uppnås. IoT Central använder informationen i DCM, t. ex. data typer, enheter och visnings namn, för att avgöra hur du ska Visa telemetri-värden.

Du kan använda funktionen IoT Central data export för att strömma telemetri till andra destinationer, till exempel lagring eller Event Hubs.

### <a name="commands"></a>Kommandon

Kommandon är antingen synkrona eller asynkrona. Ett synkront kommando måste köras inom 30 sekunder som standard och enheten måste vara ansluten när kommandot anländer. Om enheten svarar i tid, eller om enheten inte är ansluten, Miss lyckas kommandot.

Använd asynkrona kommandon för långvariga åtgärder. Enheten skickar förlopps information med telemetri-meddelanden. Dessa förlopps meddelanden har följande huvud egenskaper:

- `iothub-command-name`: kommando namnet, till exempel `UpdateFirmware` .
- `iothub-command-request-id`: det begär ande-ID som genereras på Server sidan och skickas till enheten i det första anropet.
- `iothub-interface-id`: ID: t för det gränssnitt som detta kommando är definierat på, till exempel `urn:example:AssetTracker:1` .
 `iothub-interface-name`: instans namnet för det här gränssnittet, till exempel `myAssetTracker` .
- `iothub-command-statuscode`: status koden som returnerades från enheten, till exempel `202` .

## <a name="cloud-properties"></a>Molnegenskaper

Moln egenskaper tillhör enhets mal len, men är inte en del av DCM. Moln egenskaper gör att lösnings utvecklaren anger alla metadata för enheten som ska lagras i IoT Central-programmet. Moln egenskaper påverkar inte den kod som en enhets utvecklare skriver för att implementera DCM.

En lösnings utvecklare kan lägga till moln egenskaper till instrument paneler och vyer, tillsammans med enhets egenskaper för att göra det möjligt för en operatör att hantera de enheter som är anslutna till programmet. En lösnings utvecklare kan också använda moln egenskaper som en del av en regel definition för att göra ett tröskel värde redigerbart av en operator.

## <a name="customizations"></a>Anpassningar

Anpassningar är en del av enhets mal len, men är inte en del av DCM. Anpassningar låter Solution Developer förbättra eller åsidosätta några av definitionerna i DCM. En lösnings utvecklare kan till exempel ändra visnings namnet för en typ av telemetri eller egenskap. En lösnings utvecklare kan också använda anpassningar för att lägga till verifiering, till exempel en minimal eller maximal längd för en sträng enhets egenskap.

Anpassningar kan påverka koden som en enhets utvecklare skriver för att implementera DCM. En anpassning kan till exempel ange lägsta och högsta sträng längd eller lägsta och högsta numeriska värden för telemetri.

## <a name="views"></a>Vyer

En lösnings utvecklare skapar vyer som låter operatörer övervaka och hantera anslutna enheter. Vyer är en del av enhets mal len, så en vy är kopplad till en speciell enhets typ. En vy kan innehålla:

- Diagram för att rita telemetri.
- Paneler för att visa skrivskyddade enhets egenskaper.
- Paneler så att operatören kan redigera skrivbara enhets egenskaper.
- Paneler så att operatören kan redigera moln egenskaper.
- Paneler där operatören anropar kommandon, inklusive kommandon som förväntar sig en nytto Last.
- Paneler för att visa etiketter, bilder eller markdown text.

Telemetri, egenskaper och kommandon som du kan lägga till i en vy bestäms av DCM, moln egenskaper och anpassningar i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare är det nu viktigt att du har lärt dig om enhets mallar, ett förslag till nästa steg är att läsa för [telemetri, egenskaper och kommando nytto laster](./concepts-telemetry-properties-commands.md) för att lära dig mer om de data som en enhet utbyter med IoT Central.

Som en lösnings utvecklare är ett förslag till nästa steg att läsa [definiera en ny IoT-enhets typ i ditt Azure IoT Central-program](./howto-set-up-template.md) för att lära dig mer om hur du skapar en enhets mall.
