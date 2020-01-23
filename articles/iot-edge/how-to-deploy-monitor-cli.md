---
title: Distribuera moduler i skala med Azure CLI – Azure IoT Edge
description: Använd IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fffe1ebda0103b3ed2cd8f76642ecb2967d23069
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510302"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure CLI

Skapa en **IoT Edge automatisk distribution** med hjälp av Azures kommando rads gränssnitt för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

I den här artikeln kan du ställa in Azure CLI och IoT-tillägget. Du lär dig sedan att distribuera moduler till en uppsättning IoT Edge enheter och övervaka förloppet med hjälp av tillgängliga CLI-kommandon.

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen.
* [IoT Edge-enheter](how-to-register-device.md#prerequisites-for-the-azure-cli) med IoT Edge-körningen installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Azure CLI, spara manifestet distribution lokalt som en txt-fil. Du använder fil Sök vägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett manifest för grundläggande distribution med en modul som exempel:

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

## <a name="layered-deployment"></a>Lager distribution

Lager distributioner är en typ av automatisk distribution som kan staplas ovanpå varandra. Mer information om lager distributioner finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Lager distributioner kan skapas och hanteras med Azure CLI som en automatisk distribution, med bara några få skillnader. När en lager distribution har skapats fungerar samma Azure CLI för lager distributioner på samma sätt som alla distributioner. Om du vill skapa en lager distribution lägger du till flaggan `--layered` i kommandot CREATE.

Den andra skillnaden är att distributions manifestet byggs. Även om standard automatisk distribution måste innehålla systemets körnings moduler, förutom alla användare, kan lager distributioner bara innehålla användarattribut. I stället behöver lager distributioner en standard automatisk distribution på en enhet, för att tillhandahålla nödvändiga komponenter för varje IoT Edge enhet, t. ex. system runtime-moduler.

Här är ett grundläggande distributions manifest med lager med en modul som exempel:

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

I föregående exempel visades en lager distributions inställning som `properties.desired` för en modul. Om den här skiktade distributionen är riktad mot en enhet där samma modul redan tillämpades, skulle den skriva över alla befintliga önskade egenskaper. För att kunna uppdatera, i stället för att skriva över, önskade egenskaper, kan du definiera ett nytt underavsnitt. Ett exempel:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Mer information om hur du konfigurerar modul dubbla i lager distributioner finns i [lager distribution](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste ha för att kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning. Om du hanterar en campus av smarta byggnader kan du lägga till följande taggar till en enhet:

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

Mer information om enhetstvillingar och taggar finns i [förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

Du distribuerar moduler till ditt målenheter genom att skapa en distribution som består av distribution manifestet samt andra parametrar.

Använd kommandot [AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) för att skapa en distribution:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Använd samma kommando med flaggan `--layered` för att skapa en lager deploymet.

Kommandot för att skapa distribution tar följande parametrar:

* **--skiktad** – en valfri flagga för att identifiera distributionen som en lager distribution.
* **– id för distributionstyp** -namnet på distributionen som skapas i IoT hub. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`. Obligatorisk parameter.
* **--innehåll** -filsökväg till distributionen manifest JSON. Obligatorisk parameter.
* **--hubbnamn** -namnet på IoT-hubben distributionen kommer att skapas. Hubben måste finnas i den aktuella prenumerationen. Ändra den aktuella prenumerationen med kommandot `az account set -s [subscription name]`.
* **--etiketter** – lägga till etiketter för att spåra dina distributioner. Etiketter är namn/värde-par som beskriver distributionen. Etiketter tar JSON-formatering för namn och värden. Till exempel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--Målvillkor** -ange ett Målvillkor som bestämmer vilka enheter som ska användas med den här distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioritet** -ett positivt heltal. I händelse av att två eller fler distributioner är inriktade på samma enhet, gäller distribution med det högsta numeriska värdet för prioritet.
* **--mått** – skapa mått som frågar edgeHub-rapporterade egenskaper för att spåra statusen för en distribution. Måtten tar JSON-inmatade eller en fil Sök väg. Till exempel `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd kommandot [AZ IoT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) för att visa information om en enda distribution:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot Deployment show tar följande parametrar:

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub. Obligatorisk parameter.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

Kontrollera distributionen i kommandofönstret. Egenskapen **mått** visar ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** -ett system-mått som anger antalet enhetstvillingar i IoT Hub som matchar villkoret målobjekt.
* **appliedCount** -ett system mått anger hur många enheter som har haft distributionens innehåll som tillämpas på deras modultvillingar i IoT Hub.
* **reportedSuccessfulCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporten som lyckas från IoT Edge klient körning.
* **reportedFailedCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporterings felen från IoT Edge klient körningen.

Du kan visa en lista över enhets-ID: n eller objekt för varje mått med hjälp av kommandot [AZ IoT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Kommandot Deployment show-Metric använder följande parametrar:

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--Metric-ID** – namnet på det mått som du vill se listan över enhets-ID: n för, till exempel `reportedFailedCount`.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter.

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhet inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här distributionen är den högsta prioriteten för enheten, tillämpas den här distributionen på enheten.
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret, avinstallerar den här distributionen och tar på nästa distributionen med högst prioritet.
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra dess aktuella moduler i det aktuella tillståndet, men hanteras inte som en del av den här distributionen längre. När den uppfyller målvillkoret för alla andra distributioner, avinstallerar den här distributionen och tar på den nya servern.

Det går inte att uppdatera innehållet i en distribution, som innehåller de moduler och vägar som definierats i distributions manifestet. Om du vill uppdatera innehållet i en distribution gör du det genom att skapa en ny distribution som riktar sig mot samma enheter med högre prioritet. Du kan ändra vissa egenskaper för en befintlig modul, inklusive mål villkor, etiketter, mått och prioritet.

Använd kommandot [AZ IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) för att uppdatera en-distribution:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Kommandot för distributions uppdatering tar följande parametrar:

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdaterar en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition - exempel `targetCondition=tags.location.state='Oregon'`
  * etiketter
  * prioritet
* **--Lägg** till en ny egenskap för distributionen, inklusive mål villkor eller etiketter.
* **--ta bort** en befintlig egenskap, inklusive mål villkor eller etiketter.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om din enhet inte uppfyller målvillkoret för alla andra distributioner, tas sedan moduler inte bort när distributionen har tagits bort.

Använd kommandot [AZ IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) för att ta bort en distribution:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot för att ta bort distribution tar följande parametrar:

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).
