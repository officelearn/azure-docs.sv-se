---
title: Konfigurera och övervaka IoT-enheter i stor skala med Azure IoT Hub | Microsoft Docs
description: Använd Azure IoT Hub automatisk enhetskonfigurationer för att tilldela en konfiguration till flera enheter
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 2edde122b109779794bb86752d69a5318edb9235
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42060219"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-portal"></a>Konfigurera och övervaka IoT-enheter i stor skala med Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatisk enhetshantering i Azure IoT Hub automatiserar repetitiva och komplexa uppgifter för att hantera stora enheten fjärranläggning över hela deras livscykler. Med automatisk enhetshantering du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de träder i omfånget.  Detta utförs med hjälp av en automatisk konfiguration, som också kan du sammanfatta slutförande och efterlevnad, sammanslagning av referensen och konflikter och distribuera konfigurationer i en stegvis metod.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk konfigurationer arbete genom att uppdatera en uppsättning enhetstvillingar med önskade egenskaper och rapporterar en sammanfattning av enhetstvillingen rapporterade egenskaper.  Det inför en ny klass och JSON-dokument som kallas en *Configuration* som består av tre delar:

* Den **rikta villkor** definierar omfattningen av enhetstvillingar som ska uppdateras. Målvillkoret har angetts som en fråga på enheten twin taggar och/eller rapporterade egenskaper.

* Den **rikta innehåll** definierar önskade egenskaper för att läggas till eller uppdateras i de aktuella enhetstvillingar. Innehållet inkluderar en sökväg till avsnittet i önskade egenskaper som ska ändras.

* Den **mått** definiera sammanfattning av antalet olika konfigureringstillstånd som **lyckades**, **pågår**, och **fel**. Anpassade mått har angetts som frågor på enhet enhetstvillingens egenskaper.  Systemmått är standard-mått som mäter twin uppdateringsstatus, till exempel antalet enhetstvillingar som angetts som mål och antal twins som har uppdaterats. 

## <a name="implement-device-twins-to-configure-devices"></a>Implementera enhetstvillingar för att konfigurera enheter

Automatisk enhetskonfigurationer kräver användning av enhetstvillingar för att synkronisera tillstånd mellan molnet och enheter. Referera till [förstå och använda enhetstvillingar i IoT Hub](iot-hub-devguide-device-twins.md) för hjälp med att använda enhetstvillingar.

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en konfiguration, måste du ange vilka enheter som du vill påverka. Azure IoT Hub identifierar enheter med hjälp av taggar i enhetstvillingen. Varje enhet kan ha flera taggar och definiera ett sätt som passar för din lösning. Om du hanterar enheter på olika platser kan du till exempel lägga till följande taggar till en enhetstvilling:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Skapa en konfiguration

1. I den [Azure-portalen](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT enhetskonfiguration**.

3. Välj **Lägg till konfiguration**.

Det finns fem steg för att skapa en konfiguration. I följande avsnitt beskriver var och en. 

### <a name="name-and-label"></a>Namn och etikett

1. Ge konfigurationen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.

2. Lägga till etiketter för att spåra dina konfigurationer. Etiketter är **namn**, **värdet** par som beskriver din konfiguration. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`.

3. Välj **nästa** att flytta till nästa steg. 

### <a name="specify-settings"></a>Ange inställningar

Det här avsnittet anger målet innehållet ska ställas in riktade enhetstvillingar. Det finns två indata för varje uppsättning inställningar. Först är enhetens twin sökväg är sökvägen till JSON-avsnittet i enhetstvillingens egenskaper som kan ställas in.  Andra är JSON-innehåll som ska infogas i avsnittet. Till exempel enhetens Twin sökväg och innehåll på följande:

![Ange enhetens Twin sökväg och innehåll](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Du kan också ange enskilda inställningar genom att ange hela sökvägen i enhetens Twin sökväg och värdet i innehåll med inga hakparenteser. Till exempel enhetens Twin sökväg `properties.desired.chiller-water.temperature` och ange innehållet till `66`.

Om två eller flera konfigurationer riktar samma enhetens Twin sökväg kan innehållet från den högsta priority-konfigurationen ska gälla (prioritet definieras i steg 4).

Om du vill ta bort en egenskap, ange egenskapens värde till `null`.

Du kan lägga till ytterligare inställningar genom att välja **lägga till Twin enhetsinställning**.

### <a name="specify-metrics-optional"></a>Ange mått (valfritt)

Mått ger sammanfattning av antalet olika tillstånd som en enhet kan rapportera tillbaka till följd av tillämpa innehåll. Du kan till exempel skapa ett mått för väntande ändringar av inställningar, ett mått för fel och ett mått för lyckad ändras.

1. Ange ett namn för **måttet namnet**.

2. Ange en fråga för **mått kriterier**.  Frågan är baserad på enheten enhetstvillingens egenskaper.  Mätvärdet visar antalet rader som returneras av frågan.

Exempel:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Du kan inkludera en sats att konfigurationen har tillämpats, till exempel: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

### <a name="target-devices"></a>Målenheter

Använd egenskapen taggar från dina enhetstvillingar för att fokusera på specifika enheter som ska ta emot den här konfigurationen.  Du kan också ange enheter per enhet enhetstvillingens egenskaper.

Eftersom flera konfigurationer kan samma enhet som mål, bör du ge varje konfiguration flera prioritet. Om det uppstår en konflikt, konfigurationen med den högsta prioritet vinner. 

1. Ange ett positivt heltal för konfigurationen **prioritet**. Det högsta numeriska värdet anses vara högst prioritet. Om två konfigurationer har samma prioritetsnummer, det som har skapats i de flesta WINS-nyligen. 

2. Ange en **rikta villkor** att avgöra vilka enheter som ska användas med den här konfigurationen. Villkoret är baserat på enhet twin taggar eller enhetstvillingen rapporterade egenskaper och måste matcha uttrycket-format. Till exempel `tags.environment='test'` eller `properties.reported.chillerProperties.model='4000x'`. Du kan ange `*` att fokusera på alla enheter.

3. Välj **nästa** att gå vidare till det sista steget.

### <a name="review-configuration"></a>Granska konfiguration

Granska dina konfigurationsinformation och välj sedan **skicka**.

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande steg för att visa information om en konfiguration och övervakning av enheter som kör den:

1. I den [Azure-portalen](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT enhetskonfiguration**.

3. Granska konfigurationslistan. För varje konfiguration kan visa du följande information:

   * **ID** -namnet på konfigurationen.

   * **Rikta villkor** -fråga som används för att definiera målenheter.

   * **Prioritet** -prioritetsnummer för konfigurationen.

   * **Skapandetid** -tidsstämpel från när konfigurationen har skapats. Den här tidsstämpeln används för att bryta ties när två konfigurationer har samma prioritet. 

   * **Systemmått** -mått som beräknas av IoT Hub och kan inte anpassas av utvecklare. Mål Anger hur många enhetstvillingar som matchar målvillkoret. Gäller anges antalet enhetstvillingar som har ändrats av konfigurationen, vilket kan innefatta partiella ändringar i händelse av att en separat, högre prioritet-konfiguration har också gjort ändringar. 

   * **Anpassade mått** -mått som har angetts av utvecklaren som frågor mot enhetstvillingar rapporterade egenskaper.  Upp till fem anpassade mått kan definieras per konfiguration. 
   
4. Välj den konfiguration som du vill övervaka.  

5. Granska konfigurationsinformationen. Du kan använda flikarna för att visa specifik information om de enheter som tas emot konfigurationen.

   * **Rikta villkor** -enheter som matchar målvillkoret. 

   * **Mått** – en lista över systemmått och anpassade mått.  Du kan visa en lista över enheter som räknas för varje mått genom att markera måttet i listrutan och sedan välja **Visa enheter**.

   * **Enhetsinställningar för Twin** -twin Enhetsinställningar som anges av konfigurationen. 

   * **Konfiguration av etiketter** -nyckel / värde-par som används för att beskriva en konfiguration.  Etiketter har ingen inverkan på funktionen. 

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhetstvilling inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här konfigurationen är högsta prioritet för den enhetstvillingen, används den här konfigurationen till enhetstvillingen. 

* Om en enhetstvilling inte längre uppfyller målvillkoret, inställningarna från konfigurationen tas bort och enhetstvillingen kommer att ändras av nästa högsta prioritet konfigurationen. 

* Om en enhetstvilling som körs på den här konfigurationen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra inställningar, inställningar från konfigurationen tas bort och inga andra ändringar görs på läsningen. 

Om du vill ändra en konfiguration, använder du följande steg: 

1. I den [Azure-portalen](https://portal.azure.com)går du till din IoT-hubb. 

2. Välj **IoT enhetskonfiguration**. 

3. Välj den konfiguration som du vill ändra. 

4. Gör uppdateringar i följande fält: 

   * Målvillkor 
   * Etiketter 
   * Prioritet 
   * Mått

4. Välj **Spara**.

5. Följ stegen i [övervaka en konfiguration](#monitor-a-configuration) och se ändringarna lanseras. 

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration kan ta några enhetstvillingar på deras nästa högsta prioritet konfiguration. Om enhetstvillingar inte uppfyller målvillkoret av en annan konfiguration, tillämpas inga andra inställningar. 

1. I den [Azure-portalen](https://portal.azure.com) gå till din IoT-hubb. 

2. Välj **IoT enhetskonfiguration**. 

3. Använd kryssrutan för att välja den konfiguration som du vill ta bort. 

4. Välj **Ta bort**.

5. En uppmaning ber dig att bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur konfigurerar och övervakar IoT-enheter i stor skala. Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Hantera din IoT Hub-enhetsidentiteter i grupp](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Utforska använder IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch och just-in-time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](/azure/iot-dps)
