---
title: Automatisk enhetshantering i stor skala med Azure IoT Hub | Microsoft-dokument
description: Använda automatiska Azure IoT Hub-konfigurationer för att hantera flera IoT-enheter och -moduler
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 1de7c34d8f8cbfa139212f29ca6be5d4eca64655
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767501"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatisk IoT-enhet och modulhantering med Azure-portalen

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhetshantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna för att hantera stora enhetsparker. Med automatisk enhetshantering kan du rikta in dig på en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och sedan låta IoT Hub uppdatera enheterna när de kommer in i omfånget. Den här uppdateringen görs med hjälp av en _automatisk enhetskonfiguration_ eller _automatisk modulkonfiguration_, som låter dig sammanfatta slutförande och efterlevnad, hantera sammanslagning och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhetshantering fungerar genom att uppdatera en uppsättning enhetstvillingar eller modultvillingar med önskade egenskaper och rapportera en sammanfattning som baseras på dubbla rapporterade egenskaper.  Den introducerar en ny klass och JSON dokument som kallas en *konfiguration* som har tre delar:

* **Målvillkoret** definierar omfattningen av enhetstvillingar eller modultvillingar som ska uppdateras. Målvillkoret anges som en fråga på dubbla taggar och/eller rapporterade egenskaper.

* **Målinnehållet** definierar önskade egenskaper som ska läggas till eller uppdateras i de riktade enhetstvillingarna eller modultvillingarna. Innehållet innehåller en sökväg till avsnittet med önskade egenskaper som ska ändras.

* Måtten **definierar sammanfattningsantalet** för olika konfigurationstillstånd som **Framgång,** **Pågår**och **Fel**. Anpassade mått anges som frågor om dubbla rapporterade egenskaper.  Systemmått är standardmått som mäter dubbla uppdateringsstatus, till exempel antalet tvillingar som är riktade och antalet tvillingar som har uppdaterats.

Automatiska konfigurationer körs för första gången strax efter att konfigurationen har skapats och sedan med fem minuters intervall. Måttfrågor körs varje gång den automatiska konfigurationen körs.

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

## <a name="create-a-configuration"></a>Skapa en konfiguration

1. Gå till din IoT-hubb i [Azure-portalen.](https://portal.azure.com) 

2. Välj **IoT-enhetskonfiguration**.

3. Välj **Lägg till enhetskonfiguration** eller **Lägg till modulkonfiguration**.

   ![Lägga till enhetskonfiguration eller modulkonfiguration](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Det finns fem steg för att skapa en konfiguration. Följande avsnitt går igenom var och en. 

### <a name="name-and-label"></a>Namn och etikett

1. Ge din konfiguration ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande `& ^ [ ] { } \ | " < > /`ogiltiga tecken: .

2. Lägg till etiketter som hjälper dig att spåra dina konfigurationer. Etiketter är **Namn,** **Värdepar** som beskriver din konfiguration. Exempel: `HostPlatform, Linux` eller `Version, 3.0.1`.

3. Välj **Nästa** om du vill gå till nästa steg. 

### <a name="specify-settings"></a>Ange inställningar

Det här avsnittet definierar innehållet som ska anges i riktade enhets- eller modultvillingar. Det finns två ingångar för varje uppsättning inställningar. Den första är den dubbla sökvägen, som är sökvägen till JSON-avsnittet inom de dubbla önskade egenskaper som ska ställas in.  Den andra är det JSON-innehåll som ska infogas i det avsnittet. 

Du kan till exempel ställa `properties.desired.chiller-water` in den dubbla sökvägen till och sedan ange följande JSON-innehåll: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Ange den dubbla sökvägen och innehållet](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Du kan också ange enskilda inställningar genom att ange hela den dubbla sökvägen och ange värdet utan parenteser. Med den dubbla sökvägen `properties.desired.chiller-water.temperature`anger du `66`till exempel innehållet på . Skapa sedan en ny tvillinginställning för tryckegenskapen. 

Om två eller flera konfigurationer är inriktade på samma dubbla sökväg gäller innehållet från konfigurationen med högsta prioritet (prioritet definieras i steg 4).

Om du vill ta bort en befintlig `null`egenskap anger du egenskapsvärdet till .

Du kan lägga till ytterligare inställningar genom att välja **Lägg till enhetstvillinginställning** eller **Lägg till modultvillinginställning**.

### <a name="specify-metrics-optional"></a>Ange mått (valfritt)

Mått ger sammanfattningar av de olika tillstånd som en enhet eller modul kan rapportera tillbaka efter att ha tillämpat konfigurationsinnehåll. Du kan till exempel skapa ett mått för väntande inställningsändringar, ett mått för fel och ett mått för lyckade inställningsändringar.

Varje konfiguration kan ha upp till fem anpassade mått. 

1. Ange ett namn **för Måttnamn**.

2. Ange en fråga för **måttvillkor**.  Frågan baseras på enhetstvillingrapporterade egenskaper.  Måttet representerar antalet rader som returneras av frågan.

Ett exempel:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Du kan inkludera en sats som konfigurationen tillämpades, till exempel: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Om du skapar ett mått som ska rapporteras `moduleId` `devices.modules`på konfigurerade moduler väljer du från . Ett exempel:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Målenheter

Använd egenskapen tags från dina tvillingar för att rikta in dig på de specifika enheter eller moduler som ska ta emot den här konfigurationen. Du kan också rikta in dig på dubbla rapporterade egenskaper.

Automatiska enhetskonfigurationer kan bara rikta in sig på enhetstvillingtaggar, och automatiska modulkonfigurationer kan bara rikta tvillingtaggar för målmodulen. 

Eftersom flera konfigurationer kan rikta in sig på samma enhet eller modul behöver varje konfiguration ett prioritetsnummer. Om det någonsin finns en konflikt vinner konfigurationen med högst prioritet. 

1. Ange ett positivt heltal för konfigurationen **Prioritet**. Det högsta numeriska värdet anses vara den högsta prioriteten. Om två konfigurationer har samma prioritetsnummer vinner den som skapades senast. 

2. Ange ett **målvillkor** för att avgöra vilka enheter eller moduler som ska riktas med den här konfigurationen. Villkoret baseras på dubbla taggar eller dubbla rapporterade egenskaper och ska matcha uttrycksformatet. 

   För automatisk enhetskonfiguration kan du ange bara taggen eller den rapporterade egenskapen som ska riktas. Exempel: `tags.environment='test'` eller `properties.reported.chillerProperties.model='4000x'`. Du kan `*` ange att rikta alla enheter. 
   
   För automatisk modulkonfiguration använder du en fråga för att ange taggar eller rapporterade egenskaper från modulerna som är registrerade i IoT-hubben. Exempel: `from devices.modules where tags.environment='test'` eller `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Jokertecknet kan inte användas för att rikta alla moduler. 

3. Välj **Nästa** om du vill gå vidare till det sista steget.

### <a name="review-configuration"></a>Granska konfiguration

Granska konfigurationsinformationen och välj sedan **Skicka**.

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Så här visar du information om en konfiguration och övervakar de enheter som kör den:

1. Gå till din IoT-hubb i [Azure-portalen.](https://portal.azure.com) 

2. Välj **IoT-enhetskonfiguration**.

3. Kontrollera konfigurationslistan. För varje konfiguration kan du visa följande information:

   * **ID** - namnet på konfigurationen.

   * **Målvillkor** - frågan som används för att definiera riktade enheter eller moduler.

   * **Prioritet** - prioritetsnumret som tilldelats konfigurationen.

   * **Skapa tid** - tidsstämpeln från när konfigurationen skapades. Den här tidsstämpeln används för att bryta band när två konfigurationer har samma prioritet. 

   * **Systemmått** - mått som beräknas av IoT Hub och som inte kan anpassas av utvecklare. Inriktning anger antalet enhetstvillingar som matchar målvillkoret. Tillämpar angivet antal enhetstvillingar som har ändrats av konfigurationen, vilket kan inkludera partiella ändringar i händelse av att en separat konfiguration med högre prioritet också har gjort ändringar. 

   * **Anpassade mått** – mått som har angetts av utvecklaren som frågor mot dubbla rapporterade egenskaper.  Upp till fem anpassade mått kan definieras per konfiguration. 
   
4. Välj den konfiguration som du vill övervaka.  

5. Kontrollera konfigurationsinformationen. Du kan använda flikar för att visa specifik information om de enheter som tog emot konfigurationen.

   * **Målvillkor** - de enheter eller moduler som matchar målvillkoret. 

   * **Mått** - en lista över systemmått och anpassade mått.  Du kan visa en lista över enheter eller moduler som räknas för varje mått genom att välja måttet i listrutan och sedan välja **Visa enheter** eller **Visa moduler**.

   * **Device Twin Settings** eller **Module Twin Settings** - de dubbla inställningar som ställs in av konfigurationen. 

   * **Konfigurationsetiketter** - nyckelvärdespar som används för att beskriva en konfiguration.  Etiketter har ingen inverkan på funktionaliteten. 

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration replikeras ändringarna omedelbart till alla riktade enheter eller moduler. 

Om du uppdaterar målvillkoret sker följande uppdateringar:

* Om en tvilling inte uppfyllde det gamla målvillkoret, men uppfyller det nya målvillkoret och den här konfigurationen är den högsta prioriteten för den tvillingen, tillämpas den här konfigurationen. 

* Om en tvilling som för närvarande kör den här konfigurationen inte längre uppfyller målvillkoret tas inställningarna från konfigurationen bort och tvillingen ändras av den näst högsta prioritetskonfigurationen. 

* Om en tvilling som för närvarande kör den här konfigurationen inte längre uppfyller målvillkoret och inte uppfyller målvillkoret för andra konfigurationer, tas inställningarna från konfigurationen bort och inga andra ändringar görs på tvillingen. 

Så här ändrar du en konfiguration: 

1. Gå till din IoT-hubb i [Azure-portalen.](https://portal.azure.com) 

2. Välj **IoT-enhetskonfiguration**. 

3. Välj den konfiguration som du vill ändra. 

4. Gör uppdateringar av följande fält: 

   * Målvillkor 
   * Etiketter 
   * Prioritet 
   * Mått

4. Välj **Spara**.

5. Följ stegen i [Övervaka en konfiguration](#monitor-a-configuration) för att se ändringarna rulla ut. 

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration får alla enhetstvillingar sin nästa konfiguration med högsta prioritet. Om enhetstvillingar inte uppfyller målvillkoret för någon annan konfiguration tillämpas inga andra inställningar. 

1. Gå till din IoT-hubb i [Azure-portalen.](https://portal.azure.com) 

2. Välj **IoT-enhetskonfiguration**. 

3. Använd kryssrutan för att markera den konfiguration som du vill ta bort. 

4. Välj **Ta bort**.

5. En uppmaning kommer att be dig att bekräfta.

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
