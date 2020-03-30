---
title: Distribuera modul & vägar med distributionsmanifest – Azure IoT Edge
description: Lär dig hur ett distributionsmanifest deklarerar vilka moduler som ska distribueras, hur du distribuerar dem och hur du skapar meddelandevägar mellan dem.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271400"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge

Varje IoT Edge-enhet kör minst två moduler: $edgeAgent och $edgeHub, som ingår i IoT Edge-körningen. IoT Edge-enhet kan köra flera ytterligare moduler för valfritt antal processer. Använd ett distributionsmanifest för att tala om för enheten vilka moduler som ska installeras och hur de ska konfigureras så att de fungerar tillsammans.

*Distributionsmanifestet* är ett JSON-dokument som beskriver:

* **IoT Edge** agentmodultvilling, som innehåller tre komponenter:
  * Behållaravbildningen för varje modul som körs på enheten.
  * Autentiseringsuppgifterna för att komma åt privata behållarregister som innehåller modulavbildningar.
  * Instruktioner för hur varje modul ska skapas och hanteras.
* **IoT Edge-hubmodulen** twin, som innehåller hur meddelanden flödar mellan moduler och så småningom till IoT Hub.
* De önskade egenskaperna för ytterligare modultvillingar (tillval).

Alla IoT Edge-enheter måste konfigureras med ett distributionsmanifest. En nyligen installerad IoT Edge-körning rapporterar en felkod tills den har konfigurerats med ett giltigt manifest.

I Azure IoT Edge-självstudierna skapar du ett distributionsmanifest genom att gå igenom en guide i Azure IoT Edge-portalen. Du kan också använda ett distributionsmanifest programmässigt med REST eller IoT Hub Service SDK. Mer information finns i [Förstå IoT Edge-distributioner](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Skapa ett distributionsmanifest

På en hög nivå är ett distributionsmanifest en lista över modultvillingar som är konfigurerade med önskade egenskaper. Ett distributionsmanifest talar om för en IoT Edge-enhet (eller en grupp enheter) vilka moduler som ska installeras och hur de ska konfigureras. Distributionsmanikursar innehåller *önskade egenskaper* för varje modultvilling. IoT Edge-enheter rapporterar tillbaka de *rapporterade egenskaperna* för varje modul.

Två moduler krävs i varje `$edgeAgent`distributionsmanifest: och `$edgeHub`. Dessa moduler är en del av IoT Edge-körningen som hanterar IoT Edge-enheten och de moduler som körs på den. Mer information om dessa moduler finns [i Förstå IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Förutom de två runtime-modulerna kan du lägga till upp till 20 egna moduler för att köras på en IoT Edge-enhet.

Ett distributionsmanifest som bara innehåller IoT Edge-körningen (edgeAgent och edgeHub) är giltigt.

Distributionsmanifest följer den här strukturen:

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

Definiera hur IoT Edge-körningen installerar modulerna i distributionen. IoT Edge-agenten är den körningskomponent som hanterar installation, uppdateringar och statusrapportering för en IoT Edge-enhet. Därför innehåller $edgeAgent modultvillingen konfigurations- och hanteringsinformation för alla moduler. Den här informationen innehåller konfigurationsparametrarna för själva IoT Edge-agenten.

En fullständig lista över egenskaper som kan eller måste inkluderas finns i [Egenskaper för IoT Edge-agenten och IoT Edge-hubben](module-edgeagent-edgehub.md).

Egenskaperna $edgeAgent följer den här strukturen:

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

## <a name="declare-routes"></a>Deklarera rutter

IoT Edge-hubben hanterar kommunikation mellan moduler, IoT Hub och alla lövenheter. Därför innehåller $edgeHub modultvillingen en önskad egenskap som kallas *vägar* som förklarar hur meddelanden skickas inom en distribution. Du kan ha flera vägar inom samma distribution.

Vägar deklareras i **de $edgeHub** önskade egenskaperna med följande syntax:

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

Varje rutt behöver en källa och en diskho, men villkoret är en valfri pjäs som du kan använda för att filtrera meddelanden.

### <a name="source"></a>Källa

Källan anger varifrån meddelandena kommer. IoT Edge kan dirigera meddelanden från moduler eller lövenheter.

Med hjälp av IoT SDK:er kan moduler deklarera specifika utdataköer för sina meddelanden med klassen ModuleClient. Utdataköer är inte nödvändiga, men är användbara för att hantera flera vägar. Leaf-enheter kan använda klassen DeviceClient för IoT-SDK:er för att skicka meddelanden till IoT Edge-gatewayenheter på samma sätt som de skulle skicka meddelanden till IoT Hub. Mer information finns i [Förstå och använda Azure IoT Hub SDK:er](../iot-hub/iot-hub-devguide-sdks.md).

Källegenskapen kan vara något av följande värden:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enhet till moln eller meddelanden om dubbla ändringar från en modul eller lövenhet |
| `/twinChangeNotifications` | Alla dubbla ändringar (rapporterade egenskaper) som kommer från någon modul eller lövenhet |
| `/messages/*` | Alla meddelanden från enhet till moln som skickas av en modul via vissa eller inga utdata, eller av en lövenhet |
| `/messages/modules/*` | Alla meddelanden från enhet till moln som skickas av en modul via vissa eller inga utdata |
| `/messages/modules/<moduleId>/*` | Alla meddelanden från enhet till moln som skickas av en viss modul via vissa eller inga utdata |
| `/messages/modules/<moduleId>/outputs/*` | Alla meddelanden från enhet till moln som skickas av en viss modul via vissa utdata |
| `/messages/modules/<moduleId>/outputs/<output>` | Alla meddelanden från enhet till moln som skickas av en viss modul via en viss utdata |

### <a name="condition"></a>Villkor

Villkoret är valfritt i en flödesdeklaration. Om du vill skicka alla meddelanden från källan till diskbänken, bara utelämna **WHERE-satsen** helt. Du kan också använda [frågespråket IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) för att filtrera efter vissa meddelanden eller meddelandetyper som uppfyller villkoret. IoT Edge-vägar stöder inte filtrering av meddelanden baserat på dubbla taggar eller egenskaper.

Meddelandena som skickas mellan moduler i IoT Edge formateras på samma sätt som de meddelanden som skickas mellan dina enheter och Azure IoT Hub. Alla meddelanden formateras som JSON och har **systemProperties,** **appProperties**och kroppsparametrar. **body**

Du kan skapa frågor kring någon av de tre parametrarna med följande syntax:

* Systemegenskaper: `$<propertyName>` eller`{$<propertyName>}`
* Programegenskaper:`<propertyName>`
* Kroppsegenskaper:`$body.<propertyName>`

Exempel på hur du skapar frågor för meddelandeegenskaper finns i [Frågauttryck](../iot-hub/iot-hub-devguide-routing-query-syntax.md)för meddelandeflöden från enhet till moln .

Ett exempel som är specifikt för IoT Edge är när du vill filtrera efter meddelanden som har anlänt till en gateway-enhet från en lövenhet. Meddelanden som kommer från moduler inkluderar en systemegenskap som kallas **connectionModuleId**. Så om du vill dirigera meddelanden från lövenheter direkt till IoT Hub använder du följande väg för att utesluta modulmeddelanden:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Kanalmottagare

Handfatet definierar var meddelandena skickas. Endast moduler och IoT Hub kan ta emot meddelanden. Meddelanden kan inte dirigeras till andra enheter. Det finns inga jokerteckenalternativ i diskbänksegenskapen.

Sink-egenskapen kan vara något av följande värden:

| Kanalmottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Skicka meddelandet till en viss inmatning av en viss modul |

IoT Edge ger minst en gång garantier. IoT Edge-hubben lagrar meddelanden lokalt om en rutt inte kan leverera meddelandet till diskhon. Om IoT Edge-hubben till exempel inte kan ansluta till IoT Hub eller om målmodulen inte är ansluten.

IoT Edge-hubben lagrar meddelandena upp till `storeAndForwardConfiguration.timeToLiveSecs` den tid som anges i egenskapen för de [önskade egenskaperna för IoT Edge-hubben](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera önskade egenskaper

Distributionsmanifestet anger önskade egenskaper för varje modul som distribueras till IoT Edge-enheten. Önskade egenskaper i distributionsmanifestet skriver över önskade egenskaper som för närvarande finns i modultvillingen.

Om du inte anger en modultvillings önskade egenskaper i distributionsmanifestet ändrar IoT Hub inte modultvillingen på något sätt. I stället kan du ställa in önskade egenskaper programmässigt.

Samma mekanismer som gör att du kan ändra enhetstvillingar används för att ändra modultvillingar. Mer information finns i [utvecklarguiden för modultvilling .](../iot-hub/iot-hub-devguide-module-twins.md)

## <a name="deployment-manifest-example"></a>Exempel på distributionsmanifest

I följande exempel visas hur ett giltigt distributionsmanifest kan se ut.

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
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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

* En fullständig lista över egenskaper som kan eller måste inkluderas i $edgeAgent och $edgeHub finns i [Egenskaper för IoT Edge-agenten och IoT Edge-hubben](module-edgeagent-edgehub.md).

* Nu när du vet hur IoT Edge-moduler används, [Förstå kraven och verktygen för att utveckla IoT Edge-moduler](module-development.md).
