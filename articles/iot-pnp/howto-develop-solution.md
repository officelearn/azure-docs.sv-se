---
title: Interagera med en IoT Plug and Play Preview-enhet från en Azure IoT-lösning | Microsoft-dokument
description: Som lösningsutvecklare kan du läsa om hur du använder tjänsten SDK för att interagera med IoT Plug and Play-enheter.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159225"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Ansluta till och interagera med en IoT Plug and Play Preview-enhet

Den här guiden visar hur du använder exemplen i Nod-tjänsten SDK som visar hur din IoT-lösning kan interagera med IoT Plug and Play Preview-enheter.

Om du inte har slutfört [snabbstarten Anslut en IoT Plug and Play-enhet till din lösning](quickstart-connect-pnp-device-solution-node.md) bör du göra det nu. Snabbstarten visar hur du hämtar och installerar SDK och kör några av exemplen.

Innan du kör tjänstexemplen öppnar du en ny terminal, går till rotmappen i den klonade databasen, navigerar till mappen **digitaltwins/quickstarts/service** och kör sedan följande kommando för att installera beroenden:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Kör tjänstexempel

Använd följande exempel för att utforska funktionerna i Node.js-tjänsten SDK. Kontrollera att `IOTHUB_CONNECTION_STRING` miljövariabeln är inställd i det skal du använder:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Hämta en digital tvilling och lista gränssnitten

**get_digital_twin.js** får den digitala tvillingen som är associerad med enheten och skriver ut komponenten på kommandoraden. Det kräver inte ett löpprov för att lyckas.

**get_digital_twin_interface_instance.js** får en enda gränssnittsinstans av digital tvilling som är associerad med enheten och skriver ut den på kommandoraden. Det kräver inte att enhetsprovet körs.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Hämta och ange egenskaper med nodtjänsten SDK

**update_digital_twin.js** uppdaterar en skrivbar egenskap på din digitala tvilling på enheten med en fullständig korrigeringsfil. Du kan uppdatera flera egenskaper på flera gränssnitt om du vill. För att det ska lyckas måste enhetsprovet köras samtidigt. Framgång ser ut som enheten provet är att skriva ut något om att uppdatera en egenskap tjänsten provet skriva ut en uppdaterad digital tvilling i terminalen.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Skicka ett kommando och hämta svaret med nodtjänsten SDK

**invoke_command.js** anropar ett synkront kommando på enhetens digitala tvilling. För att det ska lyckas måste enhetsprovet köras samtidigt. Framgång ser ut som om enhetsprovet skriver ut något om att bekräfta ett kommando och serviceklienten skriver ut resultatet av kommandot i terminalen.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Ansluta till den offentliga databasen och hämta en modelldefinition med nodtjänsten SDK

Med samma instruktioner som för service- och enhetsexemplen måste du ställa in följande miljövariabel:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Du hittar den här anslutningssträngen i [Azure Certified for IoT-portalen](https://preview.catalog.azureiotsolutions.com) på fliken **Anslutningssträngar** för **företagsdatabasen**.

Anslutningssträngen ser ut som följande exempel:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

När du har angett dessa fyra miljövariabler kör du exemplet på samma sätt som du körde de andra exemplen:

```cmd/sh
node model_repo.js
```

Det här exemplet hämtar **Gränssnittet ModelDiscovery** och skriver ut den här modellen i terminalen.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Kör frågor i IoT Hub baserat på kapacitetsmodeller och gränssnitt

IoT Hub-frågespråket stöder `HAS_INTERFACE` och `HAS_CAPABILITYMODEL` som visas i följande exempel:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Skapa digitala tvillingvägar

Din lösning kan få meddelanden om digitala dubbla förändringshändelser. Om du vill prenumerera på dessa meddelanden använder du [routningsfunktionen för IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) för att skicka meddelandena till en slutpunkt, till exempel blob storage, Event Hubs eller en Service Bus-kö.

Så här skapar du en digital tvillingväg:

1. Gå till din IoT Hub-resurs i Azure-portalen.
1. Välj **Meddelanderoutning**.
1. På fliken **Rutter** väljer du **Lägg till**.
1. Ange ett värde i fältet **Namn** och välj en **slutpunkt**. Om du inte har konfigurerat en slutpunkt väljer du **Lägg till slutpunkt**.
1. I listrutan **Datakälla** väljer du **Händelser för ändring av digital tvilling**.
1. Välj **Spara**.

Följande JSON visar ett exempel på en digital twin change-händelse:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om servicelösningar som interagerar med dina IoT Plug and Play-enheter är ett förslag på nästa steg att lära dig mer om [modellidentifiering.](concepts-model-discovery.md)
