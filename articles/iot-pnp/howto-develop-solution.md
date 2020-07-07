---
title: Interagera med en IoT-Plug and Play förhands gransknings enhet från en Azure IoT-lösning | Microsoft Docs
description: 'Som en lösnings utvecklare kan du läsa om hur du använder tjänst-SDK: n för att interagera med IoT Plug and Play-enheter.'
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159225"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Ansluta till och interagera med en IoT Plug and Play förhands gransknings enhet

Den här instruktions guiden visar hur du använder exemplen i Node service SDK som visar hur din IoT-lösning kan interagera med IoT Plug and Play Preview-enheter.

Om du inte har slutfört [Anslut en IoT plug and Play-enhet till din lösnings](quickstart-connect-pnp-device-solution-node.md) snabb start bör du göra det nu. Snabb starten visar hur du hämtar och installerar SDK och kör några av exemplen.

Innan du kör tjänst exemplen öppnar du en ny terminal, går till rotmappen för den klonade lagrings platsen, navigerar till mappen **digitaltwins/snabb starter/service** och kör sedan följande kommando för att installera beroendena:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Kör tjänst exemplen

Använd följande exempel för att utforska funktionerna i Node.js service SDK. Kontrol lera att `IOTHUB_CONNECTION_STRING` miljövariabeln har angetts i gränssnittet som du använder:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Hämta en digital, och visa en lista över gränssnitten

**get_digital_twin.js** hämtar den digitala enhet som är kopplad till enheten och skriver ut dess komponent på kommando raden. Ett enhets exempel som inte körs krävs för att lyckas.

**get_digital_twin_interface_instance.js** hämtar en enda gränssnitts instans av digital, som är kopplad till enheten och skriver ut den på kommando raden. Det kräver inte att enhets exemplet körs.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Hämta och ange egenskaper med hjälp av Node service SDK

**update_digital_twin.js** uppdaterar en skrivbar egenskap på enheten digitalt med en fullständig korrigering. Du kan uppdatera flera egenskaper på flera gränssnitt om du vill. För att det ska fungera måste enhets exemplet köras på samma gång. Lyckad ser ut som om enhets exemplet skriver ut något om att uppdatera en egenskap som tjänst exemplet skriver ut ett uppdaterat digitalt objekt i terminalen.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Skicka ett kommando och hämta svaret med Node service SDK

**invoke_command.js** anropar ett synkront kommando på enheten digitalt. För att det ska fungera måste enhets exemplet köras på samma gång. Lyckad ser ut som enhets exemplet skriver ut något om att bekräfta ett kommando och tjänst klienten skriver ut resultatet av kommandot i terminalen.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Anslut till den offentliga lagrings platsen och hämta en modell definition med hjälp av Node service SDK

Med samma instruktioner som för tjänst-och enhets exemplen måste du ange följande miljö variabel:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Du hittar den här anslutnings strängen i [Azure-certifierad för IoT-portalen](https://preview.catalog.azureiotsolutions.com) på fliken **anslutnings strängar** för din **företags lagrings plats**.

Anslutnings strängen ser ut som i följande exempel:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

När du har angett dessa fyra miljövariabler kör du exemplet på samma sätt som du körde de andra exemplen:

```cmd/sh
node model_repo.js
```

Det här exemplet laddar ned **ModelDiscovery** -gränssnittet och skriver ut den här modellen i terminalen.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Köra frågor i IoT Hub baserade på kapacitets modeller och gränssnitt

IoT Hub frågespråk stöder `HAS_INTERFACE` och `HAS_CAPABILITYMODEL` som visas i följande exempel:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Skapa digitala dubbla vägar

Din lösning kan ta emot aviseringar om digitala dubbla ändrings händelser. Om du vill prenumerera på dessa meddelanden använder du [funktionen IoT Hub routning](../iot-hub/iot-hub-devguide-endpoints.md) för att skicka meddelanden till en slut punkt, till exempel Blob storage, Event Hubs eller en Service Bus kö.

Så här skapar du en digital, dubbel väg:

1. I Azure Portal går du till din IoT Hub-resurs.
1. Välj **meddelanderoutning**.
1. Välj **Lägg till**på fliken **vägar** .
1. Ange ett värde i fältet **namn** och välj en **slut punkt**. Om du inte har konfigurerat någon slut punkt väljer du **Lägg till slut punkt**.
1. I list rutan **data källa** väljer du **digitala dubbla ändrings händelser**.
1. Välj **Spara**.

Följande JSON visar ett exempel på en digital, dubbel ändrings händelse:

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

Nu när du har lärt dig om tjänst lösningar som interagerar med IoT Plug and Play-enheter är ett förslag till nästa steg att lära dig mer om [modell identifiering](concepts-model-discovery.md).
