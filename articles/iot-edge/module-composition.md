---
title: Azure IoT kant modulsammansättningen | Microsoft Docs
description: Lär dig vad hamnar i Azure IoT kant-moduler och hur de kan återanvändas
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c886d1d9dea120a243693c12ae861a58126daadc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631691"
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Förstå hur IoT kant moduler kan användas, konfigurerad, och återanvänds - förhandsgranskning

Varje IoT gränsenheten kör minst två moduler: $edgeAgent och $edgeHub, som utgör IoT kant-körningsmiljön. Förutom dessa standard två köra alla IoT-enhet flera moduler för att utföra valfritt antal processer. När du distribuerar dessa moduler till en enhet på samma gång behöver du ett sätt att deklarera vilka moduler ingår hur de samverkar med varandra. 

Den *distributionsmanifestet* är ett JSON-dokument som beskriver:

* Vilka IoT kant-moduler måste distribueras tillsammans med deras alternativ för skapande och hantering.
* Konfiguration av Edge hubben, vilket innefattar hur meddelanden flödar mellan moduler och slutligen till IoT-hubb.
* Du kan också värdena som anges i modulen twins, önskade egenskaper att konfigurera enskilda modulen program.

Alla IoT-gränsenheterna måste konfigureras med en distributionsmanifestet. En nyligen installerade IoT kant runtime rapporterar en felkod förrän konfigurerats med ett giltigt manifest. 

I Azure IoT kant självstudier skapar du en distributionsmanifestet genom att gå via en guide i Azure IoT kant-portalen. Du kan också använda en distributionsmanifestet programmässigt med hjälp av REST- eller IoT-hubb Service SDK. Referera till [distribuera och övervaka] [ lnk-deploy] mer information om IoT kant-distributioner.

## <a name="create-a-deployment-manifest"></a>Skapa en distributionsmanifestet

Distributionsmanifestet konfigurerar en modul dubbla egenskaper för IoT kant-moduler som har distribuerats på en IoT-enhet på en hög nivå. Två av dessa moduler finns alltid: Edge-agenten och Edge-hubben.

Distributionsmanifestet som innehåller endast IoT kant körningen (agent och hubb) är giltig.

Manifestet följer den här strukturen:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
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

Utöver etablera önskade egenskaper för alla moduler som du vill distribuera, behöver du hur IoT kant-körningen ska installeras. Konfiguration och hantering av information för alla moduler går inuti den **$edgeAgent** önskade egenskaper. Informationen omfattar konfigurationsparametrarna för själva Edge-agenten. 

En fullständig lista över egenskaper som kan eller måste tas med, se [egenskaper för Edge agent och Edge hubb](module-edgeagent-edgehub.md).

Egenskaperna $edgeAgent följande struktur:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
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

Det är viktigt att notera att Edge hubb tillhandahåller garantier för på-minst en gång, vilket innebär att meddelanden lagras lokalt om en väg går inte att leverera meddelandet till dess mottagare, till exempel Edge-hubb kan inte ansluta till IoT-hubb eller mål-modulen är inte ansluten.

Edge hubb lagrar meddelanden upp till den tid som anges i den `storeAndForwardConfiguration.timeToLiveSecs` -egenskapen för den [kant hubb önskade egenskaper](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera egenskaper 

Distributionsmanifestet kan ange egenskaper för modulen dubbla för varje modul som distribuerats till enheten IoT kant. När egenskaperna har angetts i distributionsmanifestet över de eventuella egenskaper som finns i modulen dubbla.

Om du inte anger en modul dubbla önskade egenskaper i distributionsmanifestet IoT-hubb kan inte ändra modulen dubbla på något sätt och du kommer att kunna ange de önskade egenskaperna programmässigt.

Av samma metoder som gör det möjligt att ändra enheten twins används för att ändra modulen twins. Referera till den [enheten dubbla Utvecklarhandbok](../iot-hub/iot-hub-devguide-device-twins.md) för ytterligare information.   

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
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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
