---
title: Skapa automatiska distributioner från kommandoraden – Azure IoT Edge | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f93d9eaefe18dd012a639cd26636b56b9eb09249
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "56427644"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge kan du flytta analys vid gränsen ger ett molngränssnitt så att du kan hantera och övervaka din IoT Edge-enheter via fjärranslutning. Möjlighet att hantera enheter blir allt viktigare som IOT-lösningar växer större och mer komplexa. Azure IoT Edge är utformat för att stödja dina affärsmål, oavsett hur många enheter som du lägger till.

Du kan hantera enskilda enheter och distribuera moduler till dem i taget. Om du vill göra ändringar på enheter i stor skala, du kan dock skapa en **IoT Edge automatisk distribution**, vilket är en del av automatisk hantering av enheter i IoT Hub. Distributioner är dynamisk processer som gör att du kan distribuera flera moduler till flera enheter på samma gång, spåra status och hälsa moduler och göra ändringar när det behövs. 

I den här artikeln kan du ställa in Azure CLI och IoT-tillägget. Sedan kan du lära dig hur du distribuerar moduler till en uppsättning IoT Edge-enheter och övervaka förloppet med hjälp av de tillgängliga CLI-kommandona.

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen. 
* [IoT Edge-enheter](how-to-register-device-cli.md) med IoT Edge-körningen installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI, spara manifestet distribution lokalt som en txt-fil. Du använder sökvägen till filen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på din enhet. 

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
                   "registryCredentials": {
                     "registryName": {
                       "username": "",
                       "password": "",
                       "address": ""
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
                 "tempSensor": {
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
           "tempSensor": {
             "properties.desired": {}
           }
         }
       }
   }
   ```

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste ha för att kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar och definiera ett sätt som passar för din lösning. Om du hanterar en campus av smarta byggnader kan du lägga till följande taggar till en enhet:

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

Använd följande kommando för att skapa en distribution:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
   ```

* **– id för distributionstyp** -namnet på distributionen som skapas i IoT hub. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
* **--hubbnamn** -namnet på IoT-hubben distributionen kommer att skapas. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`.
* **--innehåll** -filsökväg till distributionen manifest JSON. 
* **--etiketter** – lägga till etiketter för att spåra dina distributioner. Etiketter är namn/värde-par som beskriver distributionen. Etiketter ta JSON formatering för namn och värden. Till exempel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--Målvillkor** -ange ett Målvillkor som bestämmer vilka enheter som ska användas med den här distributionen. Villkoret är baserat på enhet twin taggar eller enhetstvillingen rapporterade egenskaper och måste matcha uttrycket-format. Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--prioritet** -ett positivt heltal. I händelse av att två eller fler distributioner är inriktade på samma enhet, gäller distribution med det högsta numeriska värdet för prioritet.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande kommando för att visa innehållet i en distribution:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

Kontrollera distributionen i kommandofönstret. Den **mått** egenskapen innehåller ett antal för varje mått som utvärderas av varje hub:

* **targetedCount** -ett system-mått som anger antalet enhetstvillingar i IoT Hub som matchar villkoret målobjekt.
* **appliedCount** -ett system mått anger hur många enheter som har haft distributionens innehåll som tillämpas på deras modultvillingar i IoT Hub.
* **reportedSuccessfulCount** -ett mått för enheten som anger hur många av Edge-enheter i distributionen rapporterar lyckade från klienten IoT Edge-körningen.
* **reportedFailedCount** -ett mått för enheten som anger hur många av Edge-enheter i distributionen rapporterar fel från klienten IoT Edge-körningen.

Du kan visa en lista över enhets-ID eller objekt för varje mått med hjälp av följande kommando:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--mått-id** – namnet på det mått som du vill se en lista över enhets-ID, till exempel `reportedFailedCount`
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhet inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här distributionen är den högsta prioriteten för enheten, tillämpas den här distributionen på enheten. 
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret, avinstallerar den här distributionen och tar på nästa distributionen med högst prioritet. 
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra dess aktuella moduler i det aktuella tillståndet, men hanteras inte som en del av den här distributionen längre. När den uppfyller målvillkoret för alla andra distributioner, avinstallerar den här distributionen och tar på den nya servern. 

Använd följande kommando för att uppdatera en distribution:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdaterar en egenskap i distributionen. Du kan uppdatera följande egenskaper:
  * targetCondition - exempel `targetCondition=tags.location.state='Oregon'`
  * etiketter 
  * prioritet


## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om din enhet inte uppfyller målvillkoret för alla andra distributioner, tas sedan moduler inte bort när distributionen har tagits bort. 

Använd följande kommando för att ta bort en distribution:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```

* **– id för distributionstyp** -namnet på distributionen som finns i IoT hub.
* **--hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Läs mer om [distribuerar moduler till gränsenheter](module-deployment-monitoring.md).
