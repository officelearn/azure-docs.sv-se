---
title: Distribuera modul & vägar med distributions manifest – Azure IoT Edge
description: Lär dig hur ett manifest för distributionen anger vilka moduler för att distribuera, hur du distribuerar dem och hur du skapar meddelandevägar varandra.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f50b7a53d739073ced7ea590a9a6da2eceb8bda1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548653"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge

Minst två moduler som körs för varje IoT Edge-enhet: $edgeAgent och $edgeHub, som ingår i IoT Edge-körningen. IoT Edge enhet kan köra flera ytterligare moduler för valfritt antal processer. Använd ett distributions manifest för att berätta för enheten vilka moduler som ska installeras och hur de ska konfigureras för att fungera tillsammans.

Den *distribution manifest* är ett JSON-dokument som beskriver:

* **IoT Edge agent** -modul, som innehåller tre komponenter:
  * Behållar avbildningen för varje modul som körs på enheten.
  * Autentiseringsuppgifterna för åtkomst till privata behållar register som innehåller modul bilder.
  * Instruktioner för hur varje modul ska skapas och hanteras.
* Den **IoT Edge hub** modultvilling, vilket innefattar hur meddelanden mellan moduler och till slut till IoT Hub.
* De önskade egenskaperna för någon ytterligare modul är dubbla (valfritt).

Alla IoT Edge-enheter måste konfigureras med ett manifest för distribution. En nyinstallerad IoT Edge-körning rapporterar en felkod tills konfigurerats med ett giltigt manifest.

I Azure IoT Edge-självstudier skapar du ett manifest för distributionen genom att gå igenom en guide i Azure IoT Edge-portalen. Du kan också använda ett manifest för distribution via programmering med hjälp av REST- eller IoT Hub Service SDK. Mer information finns i [förstå IoT Edge-distributioner](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Skapa ett manifest för distribution

På en hög nivå är en lista över modultvillingar som är konfigurerade med deras önskade egenskaper i ett manifest för distribution. Ett manifest för distribution anger en IoT Edge-enhet (eller en grupp av enheter) vilka moduler som bör installeras och hur du konfigurerar dem. Distribution manifest innehåller den *önskade egenskaper* för varje modultvilling. IoT Edge-enheter rapporterar tillbaka den *rapporterade egenskaper* för varje modul.

Två moduler som krävs för varje distribution manifest: `$edgeAgent`, och `$edgeHub`. De här modulerna är en del av IoT Edge-körningen som hanterar IoT Edge-enheten och de moduler som körs på den. Mer information om dessa moduler finns i [förstå IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Förutom de två modulerna för körning kan du lägga till upp till 20 moduler på egen hand ska köras på en IoT Edge-enhet.

Ett manifest för distribution som innehåller endast IoT Edge-körningen (edgeAgent och edgeHub) är giltig.

Distribution manifest följande struktur:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurera moduler

Definiera hur IoT Edge-körningen installerar moduler i distributionen. IoT Edge-agenten är runtime-komponent som hanterar installationen, uppdateringar och statusrapporteringen för en IoT Edge-enhet. Därför innehåller $edgeAgent modul dubbla konfigurations-och hanterings information för alla moduler. Den här informationen inkluderar konfigurations parametrar för själva IoT Edge agenten.

En fullständig lista över egenskaper som kan eller måste inkluderas finns i [Egenskaper för IoT Edge agent och IoT Edge Hub](module-edgeagent-edgehub.md).

Egenskaper för $edgeAgent följande struktur:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarera vägar

IoT Edge hub hanterar kommunikationen mellan moduler, IoT Hub och eventuella lövenheter. Därför modultvilling $edgeHub innehåller en önskad egenskap som kallas *vägar* som förklarar hur meddelanden som skickas i en distribution. Du kan ha flera vägar inom samma distribution.

Vägar har deklarerats i den **$edgeHub** önskade egenskaper med följande syntax:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Varje väg behöver en källa och mottagare, men villkoret är en valfri typ som du kan använda för att filtrera meddelanden.

### <a name="source"></a>Källa

Källan anger var meddelanden kommer från. IoT Edge kan dirigera meddelanden från moduler eller löv enheter.

Med IoT SDK: er kan moduler deklarera vissa utdataportar för sina meddelanden med hjälp av klassen ModuleClient. Utgående köer är inte nödvändiga, men är användbara för att hantera flera vägar. Löv enheter kan använda DeviceClient-klassen i IoT SDK: er för att skicka meddelanden till IoT Edge gateway-enheter på samma sätt som de skulle skicka meddelanden till IoT Hub. Mer information finns i [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md): er.

Egenskapen source kan vara något av följande värden:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enheten till molnet eller twin ändringsmeddelanden från vilken enhet som modulen eller lövmedlemmar |
| `/twinChangeNotifications` | Ändringar twin (rapporterade egenskaper) kommer från vilken enhet som helst modulen eller lövmedlemmar |
| `/messages/*` | Alla enhets-till-moln-meddelanden som skickas av en modul genom några eller inga utdata eller av en löv enhet |
| `/messages/modules/*` | Valfri enhet-till-moln-meddelanden som skickas från en modul via vissa eller inga utdata |
| `/messages/modules/<moduleId>/*` | Valfri enhet-till-moln-meddelanden som skickas från en specifik modul via vissa eller inga utdata |
| `/messages/modules/<moduleId>/outputs/*` | Valfri enhet-till-moln-meddelanden som skickas från en specifik modul via vissa utdata |
| `/messages/modules/<moduleId>/outputs/<output>` | Valfri enhet-till-moln-meddelanden som skickas från en specifik modul via en specifik utdata |

### <a name="condition"></a>Villkor

Villkoret är valfri i en väg deklaration. Om du vill skicka alla meddelanden från källan till mottagaren ska du bara lämna **WHERE** -satsen helt. Du kan också använda den [IoT Hub-frågespråk](../iot-hub/iot-hub-devguide-routing-query-syntax.md) filtervärde för vissa meddelanden eller typer av meddelanden som uppfyller villkoret. Vägar för IoT Edge stöder inte filtrera meddelanden baserat på enhetstvilling-taggar och egenskaper.

Meddelandena som skickas mellan moduler i IoT Edge har formaterats samma som meddelandena som skickas mellan dina enheter och Azure IoT Hub. Alla meddelanden som är formaterade som JSON och har **systemProperties**, **appProperties**, och **brödtext** parametrar.

Du kan skapa frågor om någon av de tre parametrarna med följande syntax:

* Systemegenskaper: `$<propertyName>` eller `{$<propertyName>}`
* Egenskaper för program: `<propertyName>`
* Egenskaper för rapportinnehåll: `$body.<propertyName>`

Exempel om hur du skapar frågor för meddelandeegenskaper finns [enhet-till-moln-meddelande vägar fråga uttryck](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Ett exempel som är specifik för IoT Edge är när du vill filtrera efter meddelanden som anlänt på en gateway-enhet från en löv-enhet. Meddelanden som kommer från moduler som inkluderar en systemegenskap som kallas **connectionModuleId**. Så om du vill skicka meddelanden från lövenheter direkt till IoT Hub, Använd följande väg för att undanta modulen meddelanden:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Kanalmottagare

Mottagaren definierar där meddelanden skickas. Endast moduler och IoT Hub kan ta emot meddelanden. Meddelanden kan inte dirigeras till andra enheter. Det finns inga alternativ för jokertecken i egenskapen mottagare.

Egenskapen mottagare kan vara något av följande värden:

| Kanalmottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Skicka meddelandet till en specifik indata för en specifik modul |

IoT Edge gör på minst en gång garantier. IoT Edge Hub lagrar meddelanden lokalt om en väg inte kan leverera meddelandet till mottagaren. Om IoT Edge hubb till exempel inte kan ansluta till IoT Hub eller om mål modulen inte är ansluten.

IoT Edge Hub lagrar meddelandena upp till den tid som anges i egenskapen `storeAndForwardConfiguration.timeToLiveSecs` för [önskade egenskaper i IoT Edge hubben](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera önskade egenskaper

Distribution av manifestet anger önskade egenskaper för varje modul som distribueras till IoT Edge-enhet. Önskade egenskaper i manifestet distribution över alla önskade egenskaper för närvarande i modultvillingen.

Om du inte anger en modultvilling önskade egenskaper i manifestet distribution, ändra IoT Hub inte modultvilling på något sätt. Du kan i stället ange önskade egenskaper programmässigt.

Samma metoder som gör det möjligt att ändra enhetstvillingar används för att ändra modultvillingar. Mer information finns i den [utvecklarguide för modulen twin](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Exempel på distribution manifest

I följande exempel visas hur en giltig distribution manifest dokumentet kan se ut.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över egenskaper som kan eller måste inkluderas i $edgeAgent och $edgeHub finns i [Egenskaper för IoT Edge agent och IoT Edge hubb](module-edgeagent-edgehub.md).

* Nu när du vet hur IoT Edge-moduler används [förstå de krav och verktyg för att utveckla IoT Edge-moduler](module-development.md).
