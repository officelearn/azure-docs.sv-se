---
title: Konfigurera och övervaka IoT-enheter i skala med Azure IoT Hub (CLI) | Microsoft Docs
description: Använda Azure IoT Hub automatisk enhetskonfigurationer för att tilldela en konfiguration till flera enheter
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: c12a07aabdecb070cfa99f8851f907499599a1fc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036725"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurera och övervaka IoT-enheter med hjälp av Azure CLI skala

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhetshantering i Azure IoT Hub automatiserar många av återkommande och komplicerade uppgifter för att hantera stora enheten flottor via deras livscykler i sin helhet. Med automatisk enhetshantering du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT-hubb uppdatera enheter när de kommer inom omfånget.  Detta görs med hjälp av en automatisk enhetskonfiguration kommer också att du kan sammanfatta slutförande och efterlevnad, sammanslagning av referensen och konflikter och distribuera konfigurationer stegvis.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhet konfigurationer arbete med uppdatering av enheten twins med egenskaper och rapportering för en sammanfattning av enheten dubbla rapporterade egenskaper.  Det inför en ny klass och JSON-dokument som kallas en _Configuration_ som består av tre delar:

* Den **mål villkoret** definierar omfattningen av enheten twins som ska uppdateras. Målvillkoren har angetts som en fråga på enheten dubbla taggar och/eller rapporteras egenskaper.

* Den **rikta innehåll** definierar egenskaperna som ska läggas till eller uppdateras i de aktuella enheten twins. Innehållet innehåller en sökväg till avsnittet för egenskaper som ska ändras.

* Den **mått** definiera sammanfattning antal olika konfigureringstillstånd som **lyckade**, **pågår**, och **fel**. Anpassade mått har angetts som frågor på enheten dubbla rapporterade egenskaper.  Systemet är standard mått som mäter dubbla uppdateringsstatus, till exempel antalet enheten twins som riktas och antalet twins som har uppdaterats. 

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägg för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementera enheten twins för att konfigurera enheter

Automatisk enhetskonfigurationer kräver användning av twins enheten ska synkroniseras mellan moln- och enheter.  Referera till [förstå och använda enheten twins i IoT-hubb] [ lnk-device-twin] anvisningar om hur du använder enheten twins.

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en konfiguration, måste du ange vilka enheter som du vill påverka. Azure IoT-hubb identifierar enheter med hjälp av taggar i dubbla för enheten. Varje enhet kan ha flera etiketter och definiera ett sätt som passar för din lösning. Om du hanterar enheter på olika platser kan du exempelvis lägga till följande taggar till en enhet dubbla:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```
## <a name="define-the-target-content-and-metrics"></a>Definiera innehållet för mål och mått

Rikta innehåll och mått frågor har angetts som JSON-dokument som beskriver enheten dubbla önskade egenskaper för att ange och rapporterade som ska mätas.  Om du vill skapa en automatisk enhetskonfiguration som använder Azure CLI 2.0, spara rikta innehåll och mått lokalt som .txt-filer. Du använder sökvägarna i ett senare nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten. 

Här är ett exempel på en grundläggande mål innehåll:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Här följer exempel på mått frågor:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Skapa en konfiguration

Du kan konfigurera målenheterna genom att skapa en konfiguration som består av innehåll för mål och mått. 

Använd följande kommando för att skapa en konfiguration:

   ```cli
   az iot hub configuration create --config-id [configuration id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int] --metrics [metric queries]
   ```

* **--config-id** -namnet på den konfiguration som ska skapas i IoT-hubben. Ge din konfiguration för ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
* **--etiketter** -lägga till etiketter för att spåra din konfiguration. Etiketter är namn/värde-par som beskriver distributionen. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`
* **--innehåll** -Inline JSON eller filsökväg till mål-innehåll ska anges som dubbla önskade egenskaper. 
* **--hubbnamn** -namnet på IoT-hubben konfigurationen kommer att skapas. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`
* **--målvillkoren** -ange ett mål villkor för att avgöra vilka enheter som ska gälla för med den här konfigurationen. Villkoret är baserad på enheten dubbla taggar eller enheten dubbla önskade egenskaper och ska matcha uttrycket-format. Till exempel `tags.environment='test'` eller `properties.desired.devicemodel='4000x'`. 
* **--prioritet** -ett positivt heltal. I händelse av att minst två konfigurationer är inriktade på samma enhet, gäller konfigurationen med det största numeriska värdet för prioritet.
* **--mått** -filsökväg till mått-frågor. Mått Ange sammanfattning antal olika tillstånden som en enhet kan rapportera tillbaka till följd av tillämpa konfigurationen innehåll. Du kan till exempel skapa ett mått för väntande ändringar av inställningar, ett mått för fel och ett mått för lyckad ändringarna. 

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande kommando för att visa innehållet i en konfiguration:

   ```cli
az iot hub configuration show --config-id [configuration id] --hub-name [hub name]
   ```
* **--config-id** -namnet på den konfiguration som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubb som konfigurationen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`

Kontrollera konfigurationen i kommandofönstret. Den **mått** egenskapen innehåller ett antal för varje mått som ska utvärderas i varje hub:
* **targetedCount** -ett system mått som anger antalet enheten twins i IoT-hubb som matchar villkoret målobjekt.
* **appliedCount** -en system-mått som anger antalet enheter som har haft målet innehåll tillämpas.
* **Din anpassade mått** -några mått som du har definierat beaktas användaren mått.

Du kan visa en lista över enhets-ID eller objekt för var och en av mätvärdena som med hjälp av följande kommando:

   ```cli
az iot hub configuration show-metric --config-id [configuration id] --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
   ```

* **--config-id** -namnet på distributionen som finns i IoT-hubben.
* **--mått-id** – namnet på måttet som du vill se en lista över enhets-ID, till exempel `appliedCount`
* **--hubbnamn** -namnet på IoT-hubben där distributionen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`
* **--Måttyp** -måttypen kan vara `system` eller `user`.  Systemet är `targetedCount` och `appliedCount`. Alla andra mått är användaren mått.

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoren sker följande uppdateringar:
* Om dubbla en enhet inte uppfyller villkoret gamla mål men uppfyller villkoret mål och den här konfigurationen är den högsta prioriteten för den enheten dubbla, används den här konfigurationen på enheten dubbla. 
* Om en enhet dubbla uppfyller inte längre målvillkoren, inställningarna från konfigurationen tas bort och enheten dubbla kommer att ändras av nästa högsta prioritet konfigurationen. 
* Om en enhet dubbla som körs på den här konfigurationen inte längre uppfyller villkoret mål och inte uppfyller villkoret mål för andra konfigurationer, inställningarna från konfigurationen tas bort och inga andra ändringar görs på dubbla. 

Använd följande kommando för att uppdatera en konfiguration:

   ```cli
az iot hub configuration update --config-id [configuration id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--config-id** -namnet på den konfiguration som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubb som konfigurationen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`
* **--Ange** – uppdatera en egenskap i konfigurationen. Du kan uppdatera följande egenskaper:
    * targetCondition - exempel `targetCondition=tags.location.state='Oregon'`
    * etiketter 
    * prioritet

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration vidta någon enhet twins på deras nästa högsta prioritet konfiguration. Om enheten twins inte uppfyller villkoret mål av någon annan konfiguration kan tillämpas inga andra inställningar. 

Använd följande kommando för att ta bort en konfiguration:

   ```cli
az iot hub configuration delete --config-id [configuration id] --hub-name [hub name] 
   ```
* **--config-id** -namnet på den konfiguration som finns i IoT-hubben.
* **--hubbnamn** -namnet på IoT-hubb som konfigurationen finns. Hubben måste vara i den aktuella prenumerationen. Växla till önskad prenumerationen med kommandot `az account set -s [subscription name]`

## <a name="next-steps"></a>Nästa steg
I den här artikeln får du lära dig hur konfigurera och övervaka IoT-enheter i större skala. Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Hantera din IoT-hubb enheten identiteter i grupp][lnk-bulkIDs]
* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

Om du vill utforska använda Etableringstjänsten IoT-hubb enheten för att aktivera zero touch se just-in-time-etablering: 

* [Azure IoT-hubb enheten etablering av tjänst][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
