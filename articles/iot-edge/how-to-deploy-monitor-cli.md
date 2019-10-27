---
title: Skapa automatiska distributioner från kommando raden – Azure IoT Edge | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper med IoT Edge enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7ba38e1aa7196263b0ac64a6c92984cc3e7416a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964802"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge moduler i skala med hjälp av Azure CLI

Skapa en **IoT Edge automatisk distribution** med hjälp av Azures kommando rads gränssnitt för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs. 

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

I den här artikeln ställer du in Azure CLI och IoT-tillägget. Du lär dig sedan att distribuera moduler till en uppsättning IoT Edge enheter och övervaka förloppet med hjälp av tillgängliga CLI-kommandon.

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 
* [IoT Edge enheter](how-to-register-device.md#prerequisites-for-the-azure-cli) med IoT Edge Runtime installerat.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Som minst måste din Azure CLI-version vara 2.0.24 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributions manifestet lokalt som en txt-fil. Du använder fil Sök vägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten. 

Här är ett grundläggande distributions manifest med en modul som exempel:

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
                "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Identifiera enheter som använder Taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enheten med dubbla. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning. Om du till exempel hanterar ett campus-område med smarta byggnader kan du lägga till följande taggar till en enhet:

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

Mer information om enhets sammanflätade och taggar finns i [förstå och använda enhets uppIoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

Du distribuerar moduler till mål enheterna genom att skapa en distribution som består av distributions manifestet och andra parametrar. 

Använd kommandot [AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) för att skapa en distribution:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Kommandot för att skapa distribution tar följande parametrar: 

* **--Deployment-ID** – namnet på den distribution som ska skapas i IoT Hub. Ge din distribution ett unikt namn som består av upp till 128 små bokstäver. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen ska skapas i. Navet måste finnas i den aktuella prenumerationen. Ändra den aktuella prenumerationen med kommandot `az account set -s [subscription name]`.
* **--innehålls** -sökväg till distributions manifest-JSON. 
* **--Etiketter** – Lägg till etiketter som hjälper dig att spåra dina distributioner. Etiketter är namn, värdepar som beskriver din distribution. Etiketter tar JSON-formatering för namn och värden. Till exempel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--mål villkor** – ange ett mål villkor för att avgöra vilka enheter som ska vara mål för distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--prioritet** -ett positivt heltal. I händelse av att två eller flera distributioner är riktade mot samma enhet gäller distributionen med det högsta numeriska värdet för prioritet.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd kommandot [AZ IoT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) för att visa information om en enda distribution:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot Deployment show tar följande parametrar:
* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

Granska distributionen i kommando fönstret. Egenskapen **mått** visar ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** – ett system mått som anger antalet enheter i IoT Hub som matchar mål villkoret.
* **appliedCount** – ett system mått anger det antal enheter som har haft det distributions innehåll som tillämpas på deras modul är dubbla i IoT Hub.
* **reportedSuccessfulCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporten som lyckas från IoT Edge klient körning.
* **reportedFailedCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporterings felen från IoT Edge klient körningen.

Du kan visa en lista över enhets-ID: n eller objekt för varje mått med hjälp av kommandot [AZ IoT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Kommandot Deployment show-Metric använder följande parametrar: 
* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--Metric-ID** – namnet på det mått som du vill se listan över enhets-ID: n för, till exempel `reportedFailedCount`
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla mål enheter. 

Om du uppdaterar mål villkoret inträffar följande uppdateringar:

* Om en enhet inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här distributionen är den högsta prioriteten för den enheten, tillämpas den här distributionen på enheten. 
* Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret avinstalleras distributionen och börjar med den näst högsta prioriterade distributionen. 
* Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i det aktuella läget, men hanteras inte som en del av distributionen längre. När den uppfyller mål villkoren för en annan distribution avinstallerar den distributionen och tar den nya. 

Använd kommandot [AZ IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) för att uppdatera en-distribution:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Kommandot för distributions uppdatering tar följande parametrar:
* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdatera en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition – till exempel `targetCondition=tags.location.state='Oregon'`
  * Etiketter 
  * prioritet


## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution tar alla enheter med den näst högsta prioritets distributionen. Om enheterna inte uppfyller mål villkoren för någon annan distribution, tas modulerna inte bort när distributionen tas bort. 

Använd kommandot [AZ IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) för att ta bort en distribution:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Kommandot för att ta bort distribution tar följande parametrar: 
* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).
