---
title: Automatisk enhetshantering i stor skala med Azure IoT Hub (CLI) | Microsoft Docs
description: Använda automatisk Azure IoT Hub-enhetshantering för att tilldela en konfiguration för flera IoT-enheter
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 0302146634904ccf1d87220d3a24553149e10372
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322589"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Automatisk IoT-enhetshantering i stor skala med Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhetshantering i Azure IoT Hub automatiserar repetitiva och komplexa uppgifter för att hantera stora enheten fjärranläggning. Med automatisk enhetshantering du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och därefter låta IoT Hub uppdatera vilka enheter när de kommer inom omfånget. Den här uppdateringen är klar med hjälp av en _automatisk enhetskonfiguration_, vilka kan du sammanfatta slutförande och efterlevnad, sammanslagning av referensen och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk fungerar genom att uppdatera en uppsättning enhetstvillingar med önskade egenskaper och rapporterar en sammanfattning som baseras på enhetstvilling rapporterade egenskaper.  Det inför en ny klass och JSON-dokument som kallas en *Configuration* som består av tre delar:

* Den **rikta villkor** definierar omfattningen av enhetstvillingar som ska uppdateras. Målvillkoret har angetts som en fråga på enheten twin taggar och/eller rapporterade egenskaper.

* Den **rikta innehåll** definierar önskade egenskaper för att läggas till eller uppdateras i de aktuella enhetstvillingar. Innehållet inkluderar en sökväg till avsnittet i önskade egenskaper som ska ändras.

* Den **mått** definiera sammanfattning av antalet olika konfigureringstillstånd som **lyckades**, **pågår**, och **fel**. Anpassade mått har angetts som frågor på enhet enhetstvillingens egenskaper.  Systemmått är standard-mått som mäter twin uppdateringsstatus, till exempel antalet enhetstvillingar som angetts som mål och antal twins som har uppdaterats. 

## <a name="cli-prerequisites"></a>CLI-krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementera enhetstvillingar för att konfigurera enheter

Automatisk enhetskonfigurationer kräver användning av enhetstvillingar för att synkronisera tillstånd mellan molnet och enheter.  Referera till [förstå och använda enhetstvillingar i IoT Hub](iot-hub-devguide-device-twins.md) för hjälp med att använda enhetstvillingar.

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du skapar en konfiguration, måste du ange vilka enheter som du vill påverka. Azure IoT Hub identifierar enheter med hjälp av taggar i enhetstvillingen. Varje enhet kan ha flera taggar och definiera ett sätt som passar för din lösning. Till exempel om du hanterar enheter på olika platser, Lägg till följande taggar i enhetstvillingen:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definiera rikta innehåll och mått

Rikta innehåll och mått frågor har angetts som JSON-dokument som beskriver enheten twin önskade egenskaper i uppsättningen och rapporterade egenskaper ska mätas.  Om du vill skapa en automatisk konfiguration med hjälp av Azure CLI, spara rikta innehåll och mått lokalt som .txt-filer. Du kan använda sökvägarna i ett senare avsnitt när du kör kommandot för att tillämpa konfigurationen på din enhet. 

Här är ett grundläggande mål innehåll exempel:

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

Här följer exempel på måttfrågor:

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

Du kan konfigurera målenheter genom att skapa en konfiguration som består av rikta innehåll och mått. 

Använd följande kommando för att skapa en konfiguration:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** -namnet på den konfiguration som ska skapas i IoT hub. Ge konfigurationen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.

* --**etiketter** – lägga till etiketter för att spåra din konfiguration. Etiketter är namn/värde-par som beskriver distributionen. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`

* --**innehåll** -Inline JSON eller filsökväg till mål-innehåll anges som twin önskade egenskaper. 

* --**hubbnamn** -namnet på IoT-hubben konfigurationen kommer att skapas. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

* --**målvillkoret** -ange ett Målvillkor som bestämmer vilka enheter som ska användas med den här konfigurationen. Villkoret är baserat på enhet twin taggar eller enhetstvillingen önskade egenskaper och måste matcha uttrycket-format. Exempel: `tags.environment='test'` eller `properties.desired.devicemodel='4000x'`. 

* --**prioritet** -ett positivt heltal. I händelse av att två eller flera konfigurationer är inriktade på samma enhet, gäller konfigurationen med det högsta numeriska värdet för prioritet.

* --**mått** -filsökväg till måttfrågor. Mått ger sammanfattning av antalet olika tillstånd som en enhet kan rapportera tillbaka efter att de innehåll. Du kan till exempel skapa ett mått för väntande ändringar av inställningar, ett mått för fel och ett mått för lyckad ändras. 

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande kommando för att visa innehållet i en konfiguration:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** -namnet på den konfiguration som finns i IoT hub.

* --**hubbnamn** -namnet på IoT hub där konfigurationen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`

Kontrollera konfigurationen i kommandofönstret. Den **mått** egenskapen innehåller ett antal för varje mått som utvärderas av varje hub:

* **targetedCount** -ett system-mått som anger antalet enhetstvillingar i IoT Hub som matchar villkoret målobjekt.

* **appliedCount** -ett system mått anger hur många enheter som har haft målet innehåll tillämpas.

* **Din anpassade mått** – alla mått som du har definierat är användarmått.

Du kan visa en lista över enhets-ID eller objekt för varje mått med hjälp av följande kommando:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** -namnet på distributionen som finns i IoT hub.

* --**mått-id** – namnet på det mått som du vill se en lista över enhets-ID, till exempel `appliedCount`.

* --**hubbnamn** -namnet på IoT hub där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`.

* --**Måttyp** -Måttyp kan vara `system` eller `user`.  Systemmått är `targetedCount` och `appliedCount`. Alla andra mått är användarmått.

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhetstvilling inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här konfigurationen är högsta prioritet för den enhetstvillingen, används den här konfigurationen till enhetstvillingen. 

* Om en enhetstvilling inte längre uppfyller målvillkoret, inställningarna från konfigurationen tas bort och enhetstvillingen kommer att ändras av nästa högsta prioritet konfigurationen. 

* Om en enhetstvilling som körs på den här konfigurationen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra inställningar, inställningar från konfigurationen tas bort och inga andra ändringar görs på läsningen. 

Använd följande kommando för att uppdatera en konfiguration:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** -namnet på den konfiguration som finns i IoT hub.

* --**hubbnamn** -namnet på IoT hub där konfigurationen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`.

* --**Ange** – uppdaterar en egenskap i konfigurationen. Du kan uppdatera följande egenskaper:

    * targetCondition - exempel `targetCondition=tags.location.state='Oregon'`

    * etiketter 

    * prioritet

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration kan ta några enhetstvillingar på deras nästa högsta prioritet konfiguration. Om enhetstvillingar inte uppfyller målvillkoret av en annan konfiguration, tillämpas inga andra inställningar. 

Använd följande kommando för att ta bort en konfiguration:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** -namnet på den konfiguration som finns i IoT hub.

* --**hubbnamn** -namnet på IoT hub där konfigurationen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]`.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att konfigurera och övervaka IoT-enheter i stor skala. Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Hantera dina IoT Hub-enhetsidentiteter i grupp](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Utforska använder IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch och just-in-time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](/azure/iot-dps)
