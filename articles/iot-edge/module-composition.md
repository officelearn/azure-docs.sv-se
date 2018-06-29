---
title: Azure IoT kant modulsammansättningen | Microsoft Docs
description: Lär dig hur en distributionsmanifestet deklarerar vilka moduler för att distribuera, hur du distribuerar dem och hur du skapar meddelandevägar mellan dem.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 209f159d9003838edb36728828758b76730118ff
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098472"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>Lär dig hur du använder distributionsmanifest att distribuera moduler och upprätta vägar

Varje IoT gränsenheten kör minst två moduler: $edgeAgent och $edgeHub, som utgör IoT kant-körningsmiljön. Förutom dessa standard två köra alla IoT-enhet flera moduler för att utföra valfritt antal processer. När du distribuerar dessa moduler till en enhet på samma gång behöver du ett sätt att deklarera vilka moduler ingår och hur de samverkar med varandra. 

Den *distributionsmanifestet* är ett JSON-dokument som beskriver:

* Konfiguration av agenten kant som innehåller bilden behållare för varje modul, autentiseringsuppgifter för åtkomst till privata behållare register och instruktioner för hur varje modul ska skapas och hanteras.
* Konfiguration av Edge hubben, vilket innefattar hur meddelanden flödar mellan moduler och slutligen till IoT-hubb.
* Du kan också egenskaperna för modulen twins.

Alla IoT-gränsenheterna måste konfigureras med en distributionsmanifestet. En nyligen installerade IoT kant runtime rapporterar en felkod förrän konfigurerats med ett giltigt manifest. 

I Azure IoT kant självstudier skapar du en distributionsmanifestet genom att gå via en guide i Azure IoT kant-portalen. Du kan också använda en distributionsmanifestet programmässigt med hjälp av REST- eller IoT-hubb Service SDK. Mer information finns i [förstå IoT kant distributioner][lnk-deploy].

## <a name="create-a-deployment-manifest"></a>Skapa en distributionsmanifestet

Distributionsmanifestet konfigurerar en modul dubbla egenskaper för IoT kant-moduler som har distribuerats på en IoT-enhet på en hög nivå. Två av dessa moduler finns alltid: `$edgeAgent`, och `$edgeHub`.

Distributionsmanifestet som innehåller endast IoT kant körningen (agent och hubb) är giltig.

Manifestet följer den här strukturen:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurera moduler

Du behöver hur IoT kant-körningsmiljön att installera modulerna i distributionen. Konfiguration och hantering av information för alla moduler går inuti den **$edgeAgent** önskade egenskaper. Informationen omfattar konfigurationsparametrarna för själva Edge-agenten. 

En fullständig lista över egenskaper som kan eller måste tas med, se [egenskaper för Edge agent och Edge hubb](module-edgeagent-edgehub.md).

Egenskaperna $edgeAgent följande struktur:

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
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarera vägar

Edge-hubben är ett sätt att deklarativt vidarebefordra meddelanden mellan moduler och mellan moduler och IoT-hubb. Edge-hubben hanterar all kommunikation så att väginformation försätts i den **$edgeHub** önskade egenskaper. Du kan ha flera vägar inom samma distribution.

Vägar deklareras i den **$edgeHub** önskade egenskaper med följande syntax:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Varje väg behöver en källa och en mottagare, men villkoret är en valfri typ som kan användas för att filtrera meddelanden. 


### <a name="source"></a>Källa
Källan anger om meddelanden kommer från. Det kan vara något av följande värden:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enhet till moln från en enhet eller en modul |
| `/messages/*` | Varje enhet till moln-meddelande som skickas av en enhet eller en modul via vissa eller inga utdata |
| `/messages/modules/*` | Varje enhet till moln-meddelande som skickas av en modul via vissa eller inga utdata |
| `/messages/modules/{moduleId}/*` | Varje enhet till moln-meddelande som skickas av {moduleId} med inga utdata |
| `/messages/modules/{moduleId}/outputs/*` | Varje enhet till moln-meddelande som skickas av {moduleId} med vissa utdata |
| `/messages/modules/{moduleId}/outputs/{output}` | Alla enhet till moln-meddelanden som skickas med hjälp av {moduleId} {utdata} |

### <a name="condition"></a>Tillstånd
Villkoret är valfritt i en deklaration för vägen. Om du vill skicka alla meddelanden från sink till källan lämna den **där** satsen helt. Eller så kan du använda den [IoT-hubb frågespråket] [ lnk-iothub-query] för vissa filtrera meddelanden eller meddelandetyper som uppfyller villkoret.

Meddelanden som skickas mellan moduler i IoT kant formateras samma som de meddelanden som skickas mellan enheter och Azure IoT Hub. Alla meddelanden som är formaterade som JSON och har **systemProperties**, **appProperties**, och **brödtext** parametrar. 

Du kan bygga frågor kring alla tre parametrar med följande syntax: 

* Systemegenskaper: `$<propertyName>` eller `{$<propertyName>}`
* Egenskaper för program: `<propertyName>`
* Brödtext egenskaper: `$body.<propertyName>` 

Exempel om hur du skapar frågor för meddelandeegenskaper finns [enhet till moln meddelandet vägar fråga uttryck](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Ett exempel som är specifik för IoT-gräns är när du vill filtrera efter meddelanden som anlänt på en gateway-enhet från en lägsta enhet. Meddelanden som kommer från moduler innehåller en systemegenskap som kallas **connectionModuleId**. Så om du vill skicka meddelanden från löv enheter direkt till IoT-hubb, Använd följande väg för att utesluta modulen meddelanden:

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Mottagare
Sink definierar där meddelanden skickas. Det kan vara något av följande värden:

| Mottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT-hubb |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Skicka meddelandet till indata `{input}` för modulen `{moduleId}` |

IoT-Edge tillhandahåller garantier för på-minst en gång. Edge-hubben lagrar meddelanden lokalt om en väg går inte att leverera meddelandet till dess mottagare. Till exempel om kant-hubb inte kan ansluta till IoT-hubb eller mål-modulen är inte anslutet.

Edge hubb lagrar meddelanden upp till den tid som anges i den `storeAndForwardConfiguration.timeToLiveSecs` -egenskapen för den [kant hubb önskade egenskaper](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera egenskaper 

Distributionsmanifestet kan ange egenskaper för modulen dubbla för varje modul som distribuerats till enheten IoT kant. När egenskaperna har angetts i distributionsmanifestet över de eventuella egenskaper som finns i modulen dubbla.

Om du inte anger en modul dubbla önskade egenskaper i distributionsmanifestet IoT-hubb kan inte ändra modulen dubbla på något sätt och du kommer att kunna ange de önskade egenskaperna programmässigt.

Av samma metoder som gör det möjligt att ändra enheten twins används för att ändra modulen twins. Mer information finns i [enheten dubbla Utvecklarhandbok](../iot-hub/iot-hub-devguide-device-twins.md).   

## <a name="deployment-manifest-example"></a>Exempel på distribution manifest

Detta exempel på distribution manifestet JSON-dokument.

```json
{
  "moduleContent": {
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
                "password": "{password}",
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
          "tempSensor": {
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

* En fullständig lista över egenskaper som kan eller måste ingå i $edgeAgent och $edgeHub finns [egenskaper för Edge agent och Edge hubb](module-edgeagent-edgehub.md).

* Nu när du vet hur IoT kant moduler används [förstå de krav och verktyg för att utveckla IoT kant moduler][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
