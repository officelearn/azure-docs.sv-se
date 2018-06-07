---
title: Konfigurera och övervaka IoT-enheter i skala och med Azure IoT Hub | Microsoft Docs
description: Använda Azure IoT Hub automatisk enhetskonfigurationer för att tilldela en konfiguration till flera enheter
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: fe5ce960663f39d4f2c87a7bbffa091d327e9559
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632456"
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Konfigurera och övervaka IoT-enheter i skala - förhandsgranskning

Automatisk enhetshantering i Azure IoT Hub automatiserar många av återkommande och komplicerade uppgifter för att hantera stora enheten flottor via deras livscykler i sin helhet. Med automatisk enhetshantering du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT-hubb uppdatera enheter när de kommer inom omfånget.  Detta görs med hjälp av en automatisk enhetskonfiguration kommer också att du kan sammanfatta slutförande och efterlevnad, sammanslagning av referensen och konflikter och distribuera konfigurationer stegvis.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatisk enhet konfigurationer arbete med uppdatering av enheten twins med egenskaper och rapportering för en sammanfattning av enheten dubbla rapporterade egenskaper.  Det inför en ny klass och JSON-dokument som kallas en _Configuration_ som består av tre delar:

* Den **mål villkoret** definierar omfattningen av enheten twins som ska uppdateras. Målvillkoren har angetts som en fråga på enheten dubbla taggar och/eller rapporteras egenskaper.

* Den **rikta innehåll** definierar egenskaperna som ska läggas till eller uppdateras i de aktuella enheten twins. Innehållet innehåller en sökväg till avsnittet för egenskaper som ska ändras.

* Den **mått** definiera sammanfattning antal olika konfigureringstillstånd som **lyckade**, **pågår**, och **fel**. Anpassade mått har angetts som frågor på enheten dubbla rapporterade egenskaper.  Systemet är standard mått som mäter dubbla uppdateringsstatus, till exempel antalet enheten twins som riktas och antalet twins som har uppdaterats. 

> [!Note]
> Den här funktionen är inte tillgänglig för IoT-hubbar i östra USA, västra USA, Norra Europa och Västeuropa regioner under förhandsgranskning.

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

## <a name="create-a-configuration"></a>Skapa en konfiguration

1. I den [Azure-portalen][lnk-portal], gå till din IoT-hubb. 
1. Välj **konfiguration för IoT-enheter (förhandsgranskning)**.
1. Välj **lägga till konfigurationen**.

Det finns fem steg för att skapa en konfiguration. Följande avsnitt beskriver hur vart och ett. 

### <a name="step-1-name-and-label"></a>Steg 1: Ange namnet och etiketten

1. Ge din konfiguration för ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
1. Lägga till etiketter om du vill spåra dina konfigurationer. Etiketter är **namn**, **värdet** par som beskriver konfigurationen. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`.
1. Välj **nästa** gå vidare till steg två. 

### <a name="step-2-specify-settings"></a>Steg 2: Ange inställningar

Det här avsnittet anger målet innehållet anges i målenheten twins. Det finns två indata för varje uppsättning inställningar. Först är enheten dubbla sökväg, vilket är sökvägen till JSON-avsnitt i den dubbla önskade egenskaper som kan ställas in.  Andra är JSON-innehåll som ska infogas i detta avsnitt. Till exempel sökvägen till dubbla och innehåll på följande:

![Ange sökvägen till dubbla och innehåll](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Du kan också ange enskilda inställningar genom att ange hela sökvägen i enhetens dubbla sökväg och värdet i innehåll med ingen hakparenteser. Till exempel enhetens dubbla sökväg `properties.desired.chiller-water.temperature` och ange innehållet till: `66`

Om två eller flera konfigurationer anpassar samma enhet dubbla sökväg, innehållet från den högsta priority konfigurationen gäller (prioritet definieras i steg 4).

Om du vill ta bort en egenskap, ange egenskapsvärdet till `null`.

Du kan lägga till ytterligare inställningar genom att välja **lägga till dubbla enhetsinställning**

### <a name="step-3-specify-metrics-optional"></a>Steg 3: Ange mått (valfritt)

Mått Ange sammanfattning antal olika tillstånden som en enhet kan rapportera tillbaka till följd av tillämpa konfigurationen innehåll. Du kan till exempel skapa ett mått för väntande ändringar av inställningar, ett mått för fel och ett mått för lyckad ändringarna.

1. Ange ett namn för **mått namn**
1. Ange en fråga för **mått kriterier**.  Frågan är baserat på enhetens dubbla rapporterade egenskaper.  Mätvärdet visar antalet rader som returneras av frågan.

Exempel: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Du kan inkludera en sats att konfigurationen tillämpades, till exempel: `SELECT deviceId FROM devices WHERE configurations.[[yourconfigname]].status='Applied'` inklusive dubbla hakparenteser.


### <a name="step-4-target-devices"></a>Steg 4: Målenheter

Använd egenskapen taggar från din enhet twins för att fokusera på specifika enheter som ska ta emot den här konfigurationen.  Du kan också ange enheter per enhet dubbla rapporterade egenskaper.

Eftersom flera konfigurationer kan mål på samma enhet, bör du ge varje konfiguration av en prioritetsnivå. Om det uppstår en konflikt med den högsta prioritet vinner konfigurationen. 

1. Ange ett positivt heltal för konfigurationen **prioritet**. Högsta numeriskt värde anses vara högst prioritet. Om två konfigurationer har samma prioritetsnummer, som har skapats i de flesta WINS-nyligen. 
1. Ange en **mål villkoret** att avgöra vilka enheter som ska gälla för med den här konfigurationen. Villkoret är baserad på enheten dubbla taggar eller enheten dubbla rapporterade egenskaper och ska matcha uttrycket-format. Till exempel `tags.environment='test'` eller `properties.reported.chillerProperties.model='4000x'`. 
1. Välj **nästa** att gå vidare till det sista steget.

### <a name="step-5-review-configuration"></a>Steg 5: Granska konfigurationen

Granska konfigurationsinformationen om och sedan välja **skicka**.

## <a name="monitor-a-configuration"></a>Övervaka en konfiguration

Använd följande steg om du vill visa information om en konfiguration och övervakning av enheter som kör det:

1. I den [Azure-portalen][lnk-portal], gå till din IoT-hubb. 
1. Välj **konfiguration för IoT-enheter (förhandsgranskning)**.
1. Granska konfigurationslistan. Du kan visa följande information för varje konfiguration:
   * **ID** -namnet på konfigurationen.
   * **Rikta villkoret** -fråga som används för att definiera målenheter.
   * **Prioritet** -prioritetsnivå som tilldelats till konfigurationen.
   * **Skapandetid** -tidsstämpeln från när konfigurationen har skapats. Den här tidsstämpel används för att bryta ties när två konfigurationer har samma prioritet. 
   * **System mått** -mått som ska beräknas genom IoT-hubb och kan inte anpassas av utvecklare. Mål Anger hur många enheter twins som matchar villkoret mål. Gäller angivet antal twins för enheter som har ändrats av konfigurationen, vilket kan innefatta ändringar av delar i händelse av att en separat, högre prioritet konfiguration också gjort ändringar. 
   * **Anpassade mått** -mått som har angetts av utvecklaren som frågor mot enheten dubbla rapporterade egenskaper.  Upp till fem anpassade mått kan definieras per konfiguration. 
   
1. Välj den konfiguration som du vill övervaka.  
1. Granska konfigurationsinformationen. Du kan använda flikarna för att visa specifik information om de enheter som tagits emot konfigurationen: 
   * **Rikta villkoret** -enheter som matchar villkoret mål. 
   * **Mått** -en lista över system mått och anpassade mått.  Du kan visa en lista över enheter som ska räknas för varje mått genom att välja mått i listrutan och sedan välja **Visa enheter**.
   * **Dubbla Enhetsinställningar** -dubbla Enhetsinställningar som anges av konfigurationen. 
   * **Konfigurationen etiketter** -nyckel-värdepar som används för att beskriva en konfiguration.  Etiketter har ingen inverkan på funktionen. 

## <a name="modify-a-configuration"></a>Ändra en konfiguration

När du ändrar en konfiguration kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoren sker följande uppdateringar:
* Om dubbla en enhet inte uppfyller villkoret gamla mål men uppfyller villkoret mål och den här konfigurationen är den högsta prioriteten för den enheten dubbla, används den här konfigurationen på enheten dubbla. 
* Om en enhet dubbla uppfyller inte längre målvillkoren, inställningarna från konfigurationen tas bort och enheten dubbla kommer att ändras av nästa högsta prioritet konfigurationen. 
* Om en enhet dubbla som körs på den här konfigurationen inte längre uppfyller villkoret mål och inte uppfyller villkoret mål för andra konfigurationer, inställningarna från konfigurationen tas bort och inga andra ändringar görs på dubbla. 

Om du vill ändra en konfiguration, använder du följande steg: 

1. I den [Azure-portalen][lnk-portal], gå till din IoT-hubb. 
1. Välj **konfiguration för IoT-enheter (förhandsgranskning)**. 
1. Välj den konfiguration som du vill ändra. 
1. Göra uppdateringar för följande fält: 
   * Målvillkoren 
   * Etiketter 
   * Prioritet 
   * Mått
1. Välj **Spara**.
1. Följ stegen i [övervakaren en konfiguration] [fästpunkt övervakaren] se ändringarna lanserar. 

## <a name="delete-a-configuration"></a>Ta bort en konfiguration

När du tar bort en konfiguration vidta någon enhet twins på deras nästa högsta prioritet konfiguration. Om enheten twins inte uppfyller villkoret mål av någon annan konfiguration kan tillämpas inga andra inställningar. 

1. I den [Azure-portalen][lnk-portal], gå till din IoT-hubb. 
1. Välj **konfiguration för IoT-enheter (förhandsgranskning)**. 
1. Använd kryssrutan för att välja den konfiguration som du vill ta bort. 
1. Välj **Ta bort**.
1. En uppmaning ber dig att bekräfta.

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
