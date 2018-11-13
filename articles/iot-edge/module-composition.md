---
title: Azure IoT Edge-modulsammansättningen | Microsoft Docs
description: Lär dig hur ett manifest för distributionen anger vilka moduler för att distribuera, hur du distribuerar dem och hur du skapar meddelandevägar varandra.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3201e8509e7c63bb0d9b607d26292bd85e2b605d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569241"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge

Minst två moduler som körs för varje IoT Edge-enhet: $edgeAgent och $edgeHub, som utgör IoT Edge-körningen. Alla IoT Edge-enhet kan dessutom köra flera moduler för att genomföra flera olika processer. När du distribuerar dessa moduler till en enhet på samma gång, behöver du ett sätt att deklarera vilka moduler som ingår och hur de interagerar med varandra. 

Den *distribution manifest* är ett JSON-dokument som beskriver:

* Konfiguration av Edge-agenten, som innehåller behållaravbildningen för varje modul, autentiseringsuppgifter för åtkomst till privata behållarregister och instruktioner för hur varje modul ska skapas och hanteras.
* Konfiguration av Edge hub, vilket innefattar hur meddelanden mellan moduler och till slut till IoT Hub.
* Du kan också önskade egenskaper för modultvillingar.

Alla IoT Edge-enheter måste vara konfigurerad med ett manifest för distribution. En nyinstallerad IoT Edge-körning rapporterar en felkod tills konfigurerats med ett giltigt manifest. 

I Azure IoT Edge-självstudier skapar du ett manifest för distributionen genom att gå igenom en guide i Azure IoT Edge-portalen. Du kan också använda ett manifest för distribution via programmering med hjälp av REST- eller IoT Hub Service SDK. Mer information finns i [förstå IoT Edge-distributioner](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Skapa ett manifest för distribution

Manifestet distribution konfigurerar en modultvilling önskade egenskaper för IoT Edge-moduler som distribuerats på en IoT Edge-enhet på en hög nivå. Två av dessa moduler finns alltid: `$edgeAgent`, och `$edgeHub`.

Ett manifest för distribution som innehåller endast IoT Edge-körningen (agenten och hubbegenskaper) är giltig.

Manifestet följer den här strukturen:

```json
{
    "modulesContent": {
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

Du måste instruera IoT Edge-körningen så här installerar moduler i din distribution. Konfiguration och hantering av information för alla moduler som går i den **$edgeAgent** önskade egenskaper. Den här informationen innehåller konfigurationsparametrarna för själva Edge-agenten. 

En fullständig lista över egenskaper som kan eller måste tas finns i [egenskaper för Edge-agenten och Edge hub](module-edgeagent-edgehub.md).

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

Edge hub ger ett sätt att deklarativt dirigera meddelanden mellan moduler och mellan moduler och IoT Hub. Edge hub hanterar all kommunikation flödesinformation går inuti den **$edgeHub** önskade egenskaper. Du kan ha flera vägar inom samma distribution.

Vägar har deklarerats i den **$edgeHub** önskade egenskaper med följande syntax:

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

Varje väg behöver en källa och mottagare, men villkoret är en valfri typ som du kan använda för att filtrera meddelanden. 


### <a name="source"></a>Källa
Källan anger var meddelanden kommer från. Det kan vara något av följande värden:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enheten till molnet från en enhet eller en modul |
| `/messages/*` | Valfri enhet-till-moln-meddelanden som skickas från en enhet eller en modul via vissa eller inga utdata |
| `/messages/modules/*` | Valfri enhet-till-moln-meddelanden som skickas från en modul via vissa eller inga utdata |
| `/messages/modules/{moduleId}/*` | Valfri enhet-till-moln-meddelanden som skickas från {moduleId} med inga utdata |
| `/messages/modules/{moduleId}/outputs/*` | Valfri enhet-till-moln-meddelanden som skickas från {moduleId} med vissa utdata |
| `/messages/modules/{moduleId}/outputs/{output}` | Alla enhet-till-moln-meddelanden som skickas med hjälp av {moduleId} {utdata} |

### <a name="condition"></a>Tillstånd
Villkoret är valfri i en väg deklaration. Om du vill skicka alla meddelanden från mottagaren till källan, lämnar du bara den **där** satsen helt och hållet. Du kan också använda den [IoT Hub-frågespråk](../iot-hub/iot-hub-devguide-routing-query-syntax.md) filtervärde för vissa meddelanden eller typer av meddelanden som uppfyller villkoret.

Meddelandena som skickas mellan moduler i IoT Edge har formaterats samma som meddelandena som skickas mellan dina enheter och Azure IoT Hub. Alla meddelanden som är formaterade som JSON och har **systemProperties**, **appProperties**, och **brödtext** parametrar. 

Du kan skapa frågor om alla tre parametrar med följande syntax: 

* Systemegenskaper: `$<propertyName>` eller `{$<propertyName>}`
* Egenskaper för program: `<propertyName>`
* Egenskaper för rapportinnehåll: `$body.<propertyName>` 

Exempel om hur du skapar frågor för meddelandeegenskaper finns [enhet-till-moln-meddelande vägar fråga uttryck](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Ett exempel som är specifik för IoT Edge är när du vill filtrera efter meddelanden som anlänt på en gateway-enhet från en löv-enhet. Meddelanden som kommer från moduler innehåller en systemegenskap som kallas **connectionModuleId**. Så om du vill skicka meddelanden från lövenheter direkt till IoT Hub, Använd följande väg för att undanta modulen meddelanden:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Kanalmottagare
Mottagaren definierar där meddelanden skickas. Det kan vara något av följande värden:

| Kanalmottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Skicka meddelandet till indata `{input}` av modul `{moduleId}` |

IoT Edge gör på minst en gång garantier. Edge hub lagrar meddelanden lokalt om en väg det går inte att leverera meddelandet till dess mottagare. Till exempel om Edge hub inte kan ansluta till IoT Hub eller mål-modulen är inte ansluten.

Edge hub lagrar meddelanden upp till den tid som anges i den `storeAndForwardConfiguration.timeToLiveSecs` egenskapen för den [Edge hub önskade egenskaper](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiera eller uppdatera önskade egenskaper 

Manifestet distribution kan ange önskade egenskaper för modultvillingen för varje modul som distribueras till IoT Edge-enhet. När önskade egenskaper har angetts i manifestet distribution, de skriver över alla önskade egenskaper för närvarande i modultvillingen.

Om du inte anger en modultvilling önskade egenskaper i manifestet distribution, IoT Hub kan inte ändra modultvilling på något sätt och du kommer att kunna ange önskade egenskaper programmässigt.

Samma metoder som gör det möjligt att ändra enhetstvillingar används för att ändra modultvillingar. Mer information finns i den [utvecklarguide för modulen twin](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exempel på distribution manifest

Den här ett exempel på distribution manifest JSON-dokument.

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

* En fullständig lista över egenskaper som kan eller måste inkluderas i $edgeAgent och $edgeHub finns i [egenskaper för Edge-agenten och Edge hub](module-edgeagent-edgehub.md).

* Nu när du vet hur IoT Edge-moduler används [förstå de krav och verktyg för att utveckla IoT Edge-moduler](module-development.md).
