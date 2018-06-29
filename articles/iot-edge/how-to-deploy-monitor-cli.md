---
title: Distribuera och övervaka moduler för Azure IoT kant (CLI) | Microsoft Docs
description: Hantera de moduler som körs på enheter
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 705f7bfa62154bff62b2357bd8f33c01e97404d1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036539"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Distribuera och övervaka IoT kant moduler i stor skala med hjälp av Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT-gräns kan du flytta analytics kant och tillhandahåller ett gränssnitt för molnet så att du kan hantera och övervaka dina kant för IoT-enheter utan att behöva fysiskt kommer åt den. Möjlighet att hantera enheter blir allt viktigare som Internet of Things lösningar växer större och mer komplexa. Azure IoT-gräns är utformad för att stödja affärsmålen, oavsett hur många enheter som du lägger till.

Du kan hantera enskilda enheter och distribuera moduler till dem i taget. Men om du vill göra ändringar i enheter på en stor skala kan du skapa en **IoT kant automatisk distribution**, vilket är en del av automatisk hantering av enheter i IoT-hubb. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter samtidigt, spåra statusen och hälsan på moduler och göra ändringar när det behövs. 

I den här artikeln konfigurerar du Azure CLI 2.0 och IoT-tillägget. Sedan får du lära dig hur du distribuerar moduler till en uppsättning med IoT-gränsenheterna och övervaka förloppet med hjälp av de tillgängliga CLI-kommandona.

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 
* [IoT-gränsenheterna](how-to-register-device-cli.md) med den IoT kant som är installerad.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägg för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera en distributionsmanifestet

En distributionsmanifestet är en JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modulen twins. Mer information om hur distributionen visar fungerar och hur du skapar dem finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI 2.0, spara distributionsmanifestet lokalt som en txt-fil. Sökvägen till filen används i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten. 

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
                     "image": "microsoft/azureiotedge-agent:1.0-preview",
                     "createOptions": "{}"
                   }
                 },
                 "edgeHub": {
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "microsoft/azureiotedge-hub:1.0-preview",
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

Du måste kunna ange vilka enheter som du vill påverka innan du kan skapa en distribution. Azure IoT-Edge identifierar enheter med hjälp av **taggar** i dubbla för enheten. Varje enhet kan ha flera etiketter och definiera ett sätt som passar för din lösning. Om du hanterar en plats för smart byggnader kan du lägga till följande taggar till en enhet:

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

Mer information om enheten twins och taggar finns [förstå och använda enheten twins i IoT-hubb][lnk-device-twin].

## <a name="create-a-deployment"></a>Skapa en distribution

Du kan distribuera moduler till målenheter genom att skapa en distribution som består av distributionsmanifestet samt andra parametrar. 

Använd följande kommando för att skapa en distribution:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **--distribution-id** -namnet på distributionen som skapas i IoT-hubben. Ge ett unikt namn som är upp till 128 gemener för din distribution. Undvik blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
* **--etiketter** -lägga till etiketter om du vill spåra dina distributioner. Etiketter är namn/värde-par som beskriver distributionen. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`
* **--innehåll** -filsökväg till distributionen manifest JSON. 
* **--hubbnamn** -namnet på IoT-hubben distributionen kommer att skapas. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`
* **--målvillkoren** -ange ett mål villkor för att avgöra vilka enheter som ska gälla för den här distributionen. Villkoret är baserad på enheten dubbla taggar eller enheten dubbla önskade egenskaper och ska matcha uttrycket-format. Till exempel `tags.environment='test'` eller `properties.desired.devicemodel='4000x'`. 
* **--prioritet** -ett positivt heltal. I händelse av att två eller fler distributioner är inriktade på samma enhet, gäller distribution med det största numeriska värdet för prioritet.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande kommando för att visa innehållet i en distribution:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **--distribution-id** -namnet på distributionen som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubben där distributionen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`

Kontrollera distributionen i kommandofönstret. Den **mått** egenskapen innehåller ett antal för varje mått som ska utvärderas i varje hub:
* **targetedCount** -ett system mått som anger antalet enheten twins i IoT-hubb som matchar villkoret målobjekt.
* **appliedCount** -en system-mått som anger antalet enheter som har haft distributionens innehåll som tillämpas på deras modulen twins i IoT-hubb.
* **reportedSuccessfulCount** -ett mått för enheten som anger antal enheter i distributionen reporting framgång fån IoT kant klienten runtime.
* **reportedFailedCount** -ett mått för enheten som anger antal enheter i distributionen rapporterar fel från IoT kant klienten runtime.

Du kan visa en lista över enhets-ID eller objekt för var och en av mätvärdena som med hjälp av följande kommando:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--distribution-id** -namnet på distributionen som finns i IoT-hubben.
* **--mått-id** – namnet på måttet som du vill se en lista över enhets-ID, till exempel `reportedFailedCount`
* **--hubbnamn** -namnet på IoT-hubben där distributionen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoren sker följande uppdateringar:
* Om en enhet inte uppfyller villkoret gamla mål men uppfyller villkoret mål och den här distributionen är den högsta prioriteten för enheten, används den här distributionen till enheten. 
* Om en enhet som körs på den här distributionen inte längre uppfyller villkoret mål, avinstallerar den här distributionen och tar på sig nästa högsta prioritet distributionen. 
* Om en enhet som körs på den här distributionen inte längre uppfyller villkoret mål och inte uppfyller villkoret mål för andra distributioner, sker ingen förändring på enheten. Enheten fortsätter att köra de aktuella modulerna i det aktuella tillståndet, men hanteras inte som en del av distributionen längre. När den uppfyller villkoret mål av andra distribution avinstallerar den här distributionen och går till den nya. 

Använd följande kommando för att uppdatera en distribution:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--distribution-id** -namnet på distributionen som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubben där distributionen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdatera en egenskap i distributionen. Du kan uppdatera följande egenskaper:
    * targetCondition - exempel `targetCondition=tags.location.state='Oregon'`
    * etiketter 
    * prioritet


## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om dina enheter inte uppfyller villkoret mål av andra distribution, tas sedan moduler inte bort när distributionen har tagits bort. 

Använd följande kommando för att ta bort en distribution:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **--distribution-id** -namnet på distributionen som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubben där distributionen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [distribuerar moduler till gränsenheterna][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
