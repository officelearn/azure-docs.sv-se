---
title: Automatisk enhetshantering i stor skala med Azure IoT Hub (CLI) | Microsoft-dokument
description: Använda automatiska Azure IoT Hub-konfigurationer för att hantera flera IoT-enheter eller-moduler
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 76728b99f1e9309294079d4df88e563054e780e7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767567"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatisk IoT-enhet och modulhantering med Hjälp av Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhetshantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna för att hantera stora enhetsparker. Med automatisk enhetshantering kan du rikta in dig på en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och sedan låta IoT Hub uppdatera enheterna när de kommer in i omfånget. Den här uppdateringen görs med hjälp av en _automatisk enhetskonfiguration_ eller _automatisk modulkonfiguration_, som låter dig sammanfatta slutförande och efterlevnad, hantera sammanslagning och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhetshantering fungerar genom att uppdatera en uppsättning enhetstvillingar eller modultvillingar med önskade egenskaper och rapportera en sammanfattning som baseras på dubbla rapporterade egenskaper.  Den introducerar en ny klass och JSON dokument som kallas en *konfiguration* som har tre delar:

* **Målvillkoret** definierar omfattningen av enhetstvillingar eller modultvillingar som ska uppdateras. Målvillkoret anges som en fråga på enhetstvillingtaggar och/eller rapporterade egenskaper.

* **Målinnehållet** definierar önskade egenskaper som ska läggas till eller uppdateras i de riktade enhetstvillingarna eller modultvillingarna. Innehållet innehåller en sökväg till avsnittet med önskade egenskaper som ska ändras.

* Måtten **definierar sammanfattningsantalet** för olika konfigurationstillstånd som **Framgång,** **Pågår**och **Fel**. Anpassade mått anges som frågor om dubbla rapporterade egenskaper.  Systemmått är standardmått som mäter dubbla uppdateringsstatus, till exempel antalet tvillingar som är riktade och antalet tvillingar som har uppdaterats.

Automatiska konfigurationer körs för första gången strax efter att konfigurationen har skapats och sedan med fem minuters intervall. Måttfrågor körs varje gång den automatiska konfigurationen körs.

## <a name="cli-prerequisites"></a>CLI-förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 

* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementera tvillingar

Automatiska enhetskonfigurationer kräver användning av enhetstvillingar för att synkronisera tillståndet mellan molnet och enheterna.  Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

Automatiska modulkonfigurationer kräver användning av modultvillingar för att synkronisera tillstånd mellan molnet och modulerna. Mer information finns i [Förstå och använda modultvillingar i IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Använd taggar för att rikta tvillingar

Innan du skapar en konfiguration måste du ange vilka enheter eller moduler du vill påverka. Azure IoT Hub identifierar enheter och använder taggar i enhetstvillingen och identifierar moduler med hjälp av taggar i modultvillingen. Varje enhet eller moduler kan ha flera taggar, och du kan definiera dem på något sätt som är meningsfullt för din lösning. Om du till exempel hanterar enheter på olika platser lägger du till följande taggar i en enhetstvilling:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definiera målinnehåll och mått

Målinnehållet och måttfrågorna anges som JSON-dokument som beskriver enhetstvillingens eller modultvillingens önskade egenskaper för att ange och rapporterade egenskaper som ska mätas.  Om du vill skapa en automatisk konfiguration med Azure CLI sparar du målinnehållet och måtten lokalt som TXT-filer. Du använder filsökvägarna i ett senare avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande exempel på målinnehåll för en automatisk enhetskonfiguration:

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

Automatiska modulkonfigurationer fungerar mycket `moduleContent` på `deviceContent`samma sätt, men du riktar dig i stället för .

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Här är exempel på måttfrågor:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Måttfrågor för moduler liknar också frågor för enheter, men `moduleId` `devices.modules`du väljer för från . Ett exempel: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Skapa en konfiguration

Du konfigurerar målenheter genom att skapa en konfiguration som består av målinnehållet och måtten. 

Använd följande kommando för att skapa en konfiguration:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - Namnet på konfigurationen som ska skapas i IoT-hubben. Ge din konfiguration ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande `& ^ [ ] { } \ | " < > /`ogiltiga tecken: .

* --**etiketter** - Lägg till etiketter för att spåra konfigurationen. Etiketter är Namn, Värdepar som beskriver distributionen. Det kan till exempel vara `HostPlatform, Linux` eller `Version, 3.0.1`

* --**innehåll** - Infogad JSON eller filsökväg till målinnehållet som ska anges som dubbla önskade egenskaper. 

* --**hubbnamn** - Namnet på IoT-hubben där konfigurationen ska skapas. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot`az account set -s [subscription name]`

* --**målvillkor** - Ange ett målvillkor för att avgöra vilka enheter eller moduler som ska riktas med den här konfigurationen.För automatisk enhetskonfiguration baseras villkoret på enhetstvillingtaggar eller enhetstvilling önskade egenskaper och bör matcha uttrycksformatet.Exempel: `tags.environment='test'` eller `properties.desired.devicemodel='4000x'`.För automatisk modulkonfiguration baseras villkoret på modultvillingtaggar eller modultvilling önskade egenskaper.. Exempel: `from devices.modules where tags.environment='test'` eller `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioritet** - Ett positivt heltal. I händelse av att två eller flera konfigurationer är inriktade på samma enhet eller modul gäller konfigurationen med det högsta numeriska värdet för Prioritet.

* --**mått** - Filsökväg till måttfrågorna. Mått ger sammanfattningar av de olika tillstånd som en enhet eller modul kan rapportera tillbaka efter att ha tillämpat konfigurationsinnehåll. Du kan till exempel skapa ett mått för väntande inställningsändringar, ett mått för fel och ett mått för lyckade inställningsändringar. 

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande kommando för att visa innehållet i en konfiguration:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - Namnet på konfigurationen som finns i IoT-hubben.

* --**hubbnamn** - Namnet på IoT-hubben där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot`az account set -s [subscription name]`

Kontrollera konfigurationen i kommandofönstret.Egenskapen **metrics listar** ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** - Ett systemmått som anger antalet enhetstvillingar eller modultvillingar i IoT Hub som matchar inriktningen.

* **appliedCount** - Ett systemmått anger antalet enheter eller moduler som har tillämpat målinnehållet.

* **Ditt anpassade mått** - Alla mått som du har definierat är användarmått.

Du kan visa en lista över enhets-ID: er, modul-ID eller objekt för vart och ett av måtten med hjälp av följande kommando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - Namnet på distributionen som finns i IoT-hubben.

* --**metric-id** - Namnet på det mått som du vill visa listan över enhets-ID eller modul-ID: n, till exempel `appliedCount`.

* --**hubbnamn** - Namnet på IoT-hubben där distributionen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration `az account set -s [subscription name]`med kommandot .

* --**måtttyp** - Måtttyp `system` kan `user`vara eller .  Systemmått är `targetedCount` `appliedCount`och . Alla andra mått är användarmått.

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration replikeras ändringarna omedelbart till alla riktade enheter. 

Om du uppdaterar målvillkoret sker följande uppdateringar:

* Om en tvilling inte uppfyllde det gamla målvillkoret, men uppfyller det nya målvillkoret och den här konfigurationen är den högsta prioriteten för den tvillingen, tillämpas den här konfigurationen. 

* Om en tvilling som för närvarande kör den här konfigurationen inte längre uppfyller målvillkoret tas inställningarna från konfigurationen bort och tvillingen ändras av den näst högsta prioritetskonfigurationen. 

* Om en tvilling som för närvarande kör den här konfigurationen inte längre uppfyller målvillkoret och inte uppfyller målvillkoret för andra konfigurationer, tas inställningarna från konfigurationen bort och inga andra ändringar görs på tvillingen. 

Använd följande kommando för att uppdatera en konfiguration:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - Namnet på konfigurationen som finns i IoT-hubben.

* --**hubbnamn** - Namnet på IoT-hubben där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration `az account set -s [subscription name]`med kommandot .

* --**-** Uppdatera en egenskap i konfigurationen. Du kan uppdatera följande egenskaper:

    * targetCondition - till exempel`targetCondition=tags.location.state='Oregon'`

    * Etiketter 

    * prioritet

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration får alla enhetstvillingar eller modultvillingar sin nästa konfiguration med högsta prioritet. Om tvillingar inte uppfyller målvillkoret för någon annan konfiguration tillämpas inga andra inställningar. 

Använd följande kommando för att ta bort en konfiguration:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - Namnet på konfigurationen som finns i IoT-hubben.

* --**hubbnamn** - Namnet på IoT-hubben där konfigurationen finns. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration `az account set -s [subscription name]`med kommandot .

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar och övervakar IoT-enheter i stor skala. Följ de här länkarna om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Hantera dina IoT Hub-enhetsidentiteter i grupp](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Information om hur du använder etableringstjänsten för IoT Hub Device För att aktivera nollpunkts-etablering, just-in-time-etablering finns i: 

* [Azure IoT Hub Device Provisioning-tjänst](/azure/iot-dps)
