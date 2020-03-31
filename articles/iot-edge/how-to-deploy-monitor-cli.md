---
title: Distribuera moduler i stor skala med Azure CLI - Azure IoT Edge
description: Använda IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271478"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Hjälp av Azure CLI

Skapa en **automatisk distribution i IoT Edge** med hjälp av Azure-kommandoradsgränssnittet för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge är en del av den [automatiska enhetshanteringsfunktionen](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsotillstånd för modulerna och göra ändringar vid behov.

Mer information finns i [Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

I den här artikeln konfigurerar du Azure CLI och IoT-tillägget. Du lär dig sedan hur du distribuerar moduler till en uppsättning IoT Edge-enheter och övervakar förloppet med hjälp av tillgängliga CLI-kommandon.

## <a name="cli-prerequisites"></a>CLI-förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [IoT Edge-enheter](how-to-register-device.md#prerequisites-for-the-azure-cli) med IoT Edge-körningen installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Mer information finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributionsmanifestet lokalt som en TXT-fil. Du använder filsökvägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributionsmanifest med en modul som exempel:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Distribution i lager

Distributioner med lager är en typ av automatisk distribution som kan staplas ovanpå varandra. Mer information om distributioner i flera lager finns i [Förstå IoT Edge-automatiska distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

Distributioner med lager kan skapas och hanteras med Azure CLI som alla automatiska distributioner, med bara några skillnader. När en distribution i flera lager har skapats fungerar samma Azure CLI-arbete för distributioner i flera lager på samma sätt som alla distributioner. Om du vill skapa en `--layered` distribution i flera lager lägger du till flaggan i kommandot Skapa.

Den andra skillnaden är i byggandet av distributionsmanifestet. Även om standard automatisk distribution måste innehålla systemkörningsmodulerna utöver alla användarmoduler, kan distributioner i flera lager bara innehålla användarmoduler. I stället behöver distributioner i flera lager en vanlig automatisk distribution även på en enhet, för att leverera de nödvändiga komponenterna för varje IoT Edge-enhet, till exempel systemkörningsmodulerna.

Här är ett grundläggande distributionsmanifest med en modul i flera lager som exempel:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

I föregående exempel visades en `properties.desired` distributionsinställning i flera lager för en modul. Om den här distributionen i flera lager riktades mot en enhet där samma modul redan har tillämpats, skulle den skriva över alla befintliga önskade egenskaper. Om du vill uppdatera önskade egenskaper kan du definiera ett nytt underavsnitt i stället för att skriva över önskade egenskaper. Ett exempel:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Mer information om hur du konfigurerar modultvillingar i distributioner i flera lager finns i [Distribution med lager](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar som du definierar på något sätt som är meningsfullt för din lösning. Om du till exempel hanterar ett campus med smarta byggnader kan du lägga till följande taggar på en enhet:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Mer information om enhetstvillingar och -taggar finns i [Förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

Du distribuerar moduler till dina målenheter genom att skapa en distribution som består av distributionsmanifestet samt andra parametrar.

Använd kommandot [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) för att skapa en distribution:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Använd samma kommando `--layered` med flaggan för att skapa ett lager deploymet.

Kommandot distributionsskapande tar följande parametrar:

* **--layered** - En valfri flagga för att identifiera distributionen som en distribution i flera lager.
* **--deployment-id** - Namnet på distributionen som ska skapas i IoT-hubben. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande `& ^ [ ] { } \ | " < > /`ogiltiga tecken: . Obligatorisk parameter.
* **--content** - Filsökväg till distributionsmanifestet JSON. Obligatorisk parameter.
* **--hub-name** - Namnet på IoT-hubben där distributionen ska skapas. Navet måste finnas i den aktuella prenumerationen. Ändra din nuvarande `az account set -s [subscription name]` prenumeration med kommandot.
* **--etiketter** - Lägg till etiketter för att spåra dina distributioner. Etiketter är Namn, Värdepar som beskriver distributionen. Etiketter tar JSON-formatering för namn och värden. Till exempel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - Ange ett målvillkor för att avgöra vilka enheter som ska riktas med den här distributionen.Villkoret baseras på enhetstvillingtaggar eller enhetstvillingrapporterade egenskaper och bör matcha uttrycksformatet.Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioritet** - Ett positivt heltal. I händelse av att två eller flera distributioner är inriktade på samma enhet gäller distributionen med det högsta numeriska värdet för Prioritet.
* **--mått** - Skapa mått som frågar edgeHub rapporterade egenskaper för att spåra status för en distribution. Mått tar JSON-indata eller en filsökväg. Till exempel `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd kommandot [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) för att visa information om en enda distribution:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot Distributionsvisning tar följande parametrar:

* **--deployment-id** - Namnet på distributionen som finns i IoT-hubben. Obligatorisk parameter.
* **--hub-name** - Namnet på IoT-hubben där distributionen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot`az account set -s [subscription name]`

Kontrollera distributionen i kommandofönstret.Egenskapen **metrics listar** ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** - Ett systemmått som anger antalet enhetstvillingar i IoT Hub som matchar inriktningen.
* **appliedCount** - Ett systemmått anger antalet enheter som har haft distributionsinnehållet tillämpat på deras modultvillingar i IoT Hub.
* **reportedSuccessfulCount** - Ett enhetsmått som anger antalet IoT Edge-enheter i distributionsrapporteringen som lyckas från IoT Edge-klientkörningen.
* **reportedFailedCount** - Ett enhetsmått som anger antalet IoT Edge-enheter i distributionsrapportfelet från IoT Edge-klientkörningen.

Du kan visa en lista över enhets-ID:er eller objekt för vart och ett av måtten med hjälp av kommandot [visa mått för az iot edge-distribution:](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Kommandot distributionsvisningsstatistik tar följande parametrar:

* **--deployment-id** - Namnet på distributionen som finns i IoT-hubben.
* **--metric-id** - Namnet på det mått som du vill visa listan över `reportedFailedCount`enhets-ID: n, till exempel .
* **--hub-name** - Namnet på IoT-hubben där distributionen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration `az account set -s [subscription name]`med kommandot .

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla riktade enheter.

Om du uppdaterar målvillkoret sker följande uppdateringar:

* Om en enhet inte uppfyllde det gamla målvillkoret, men uppfyller det nya målvillkoret och den här distributionen har högsta prioritet för den enheten, tillämpas den här distributionen på enheten.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret avinstalleras den här distributionen och får den näst högsta prioritetsdistributionen.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret och inte uppfyller målvillkoret för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i sitt aktuella tillstånd, men hanteras inte längre som en del av den här distributionen. När den uppfyller målvillkoret för en annan distribution avinstallerar den den här distributionen och tar sig an den nya.

Du kan inte uppdatera innehållet i en distribution, som innehåller moduler och vägar som definierats i distributionsmanifestet. Om du vill uppdatera innehållet i en distribution gör du det genom att skapa en ny distribution som riktar sig till samma enheter med högre prioritet. Du kan ändra vissa egenskaper för en befintlig modul, inklusive målvillkor, etiketter, mått och prioritet.

Använd kommandot [az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) för att uppdatera en distribution:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Kommandot distributionsuppdatering tar följande parametrar:

* **--deployment-id** - Namnet på distributionen som finns i IoT-hubben.
* **--hub-name** - Namnet på IoT-hubben där distributionen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot`az account set -s [subscription name]`
* **--set** - Uppdatera en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition - till exempel`targetCondition=tags.location.state='Oregon'`
  * Etiketter
  * prioritet
* **--add** - Lägg till en ny egenskap i distributionen, inklusive målvillkor eller etiketter.
* **--remove** - Ta bort en befintlig egenskap, inklusive målvillkor eller etiketter.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution får alla enheter sin nästa distribution med högsta prioritet. Om dina enheter inte uppfyller målvillkoret för någon annan distribution tas modulerna inte bort när distributionen tas bort.

Använd kommandot [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) för att ta bort en distribution:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot distributionsborttagning tar följande parametrar:

* **--deployment-id** - Namnet på distributionen som finns i IoT-hubben.
* **--hub-name** - Namnet på IoT-hubben där distributionen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot`az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Läs mer om [att distribuera moduler till IoT Edge-enheter](module-deployment-monitoring.md).
