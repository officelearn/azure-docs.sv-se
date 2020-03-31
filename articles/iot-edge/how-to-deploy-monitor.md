---
title: Distribuera moduler i stor skala i Azure Portal - Azure IoT Edge
description: Använda Azure-portalen för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271439"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure-portalen

Skapa en automatisk distribution i **IoT Edge** i Azure-portalen för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge är en del av den [automatiska enhetshanteringsfunktionen](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsotillstånd för modulerna och göra ändringar vid behov.

Mer information finns i [Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar som du definierar på något sätt som är meningsfullt för din lösning.

Om du till exempel hanterar ett campus med smarta byggnader kan du lägga till plats-, rumstyp- och miljötaggar på en enhet:

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

Mer information om enhetstvillingar och -taggar finns i [Förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

IoT Edge innehåller två olika typer av automatiska distributioner som du kan använda för att anpassa ditt scenario. Du kan skapa en *standarddistribution*, som innehåller systemkörningsmoduler och eventuella ytterligare moduler och vägar. Varje enhet kan bara använda en distribution. Du kan också skapa en *distribution i flera lager*, som bara innehåller anpassade moduler och vägar, inte systemets körning. Många distributioner i flera lager kan kombineras på en enhet, ovanpå en standarddistribution. Mer information om hur de två typerna av automatiska distributioner fungerar tillsammans finns i [Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

Stegen för att skapa en distribution och en distribution i flera lager är mycket lika. Eventuella skillnader anropas i följande steg.

1. Gå till din IoT Hub i [Azure-portalen.](https://portal.azure.com)
1. Välj **IoT Edge** under **Automatisk enhetshantering**på menyn i den vänstra rutan .
1. I det övre fältet väljer du **Skapa distribution** eller **Skapa lagerdistribution**.

Det finns fem steg för att skapa en distribution. Följande avsnitt går igenom var och en.

### <a name="step-1-name-and-label"></a>Steg 1: Namn och etikett

1. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande `& ^ [ ] { } \ | " < > /`ogiltiga tecken: .
1. Du kan lägga till etiketter som nyckel-värde-par för att spåra dina distributioner. **HostPlatform** och **Linux**, eller **Version** och **3.0.1**.
1. Välj **Nästa: Moduler** för att gå till steg två.

### <a name="step-2-modules"></a>Steg 2: Moduler

Du kan lägga till upp till 20 moduler i en distribution. Om du skapar en distribution utan moduler tas alla aktuella moduler bort från målenheterna.

I distributioner kan du hantera inställningarna för IoT Edge-agenten och IoT Edge-hubbmodulerna. Välj **Körningsinställningar** om du vill konfigurera de två körningsmodulerna. I distributionen i flera lager ingår inte körningsmodulerna så det går inte att konfigurera.

Du kan lägga till tre typer av moduler:

* IoT-kantmodul
* Marketplace-modul
* Analysmodul för Azure Stream

#### <a name="add-an-iot-edge-module"></a>Lägga till en IoT Edge-modul

Så här lägger du till anpassad kod som en modul eller lägger till en Azure-tjänstmodul manuellt:

1. I avsnittet **Autentiseringsuppgifter för behållarregister** på sidan anger du namn och autentiseringsuppgifter för alla privata behållarregister som innehåller modulavbildningarna för den här distributionen. IoT Edge Agent rapporterar fel 500 om den inte kan hitta behållarregistrets autentiseringsuppgifter för en Docker-avbildning.
1. Klicka på **Lägg till**i avsnittet **IoT-kantmoduler** på sidan .
1. Välj **IoT Edge Module** på rullgardinsmenyn.
1. Ge modulen ett **IoT Edge-modulnamn**.
1. För fältet **Bild-URI** anger du behållaravbildningen för modulen.
1. Använd den nedrullningsbara menyn för att välja en **omstartsprincip**. Välj bland följande alternativ:
   * **alltid** - Modulen startar alltid om den stängs av någon anledning.
   * **aldrig** - Modulen startar aldrig om den stängs av av någon anledning.
   * **on-failure** - Modulen startar om den kraschar, men inte om den stängs av rent.
   * **on-unhealthy** - Modulen startar om den kraschar eller returnerar en felaktig status. Det är upp till varje modul att implementera hälsostatusfunktionen.
1. Använd rullgardinsmenyn för att välja **önskad status** för modulen. Välj bland följande alternativ:
   * **köra** - Löpning är standardalternativet. Modulen börjar köras omedelbart efter att den har distribuerats.
   * **stoppad** - Efter att ha distribuerats förblir modulen inaktiv tills den uppmanas att starta av dig eller en annan modul.
1. Ange eventuella **alternativ för att skapa behållare** som ska skickas till behållaren. Mer information finns i [docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Välj **Modultvillinginställningar** om du vill lägga till taggar eller andra egenskaper i modultvillingen.
1. Ange **miljövariabler** för den här modulen. Miljövariabler ger konfigurationsinformation till en modul.
1. Välj **Lägg till** om du vill lägga till modulen i distributionen.

#### <a name="add-a-module-from-the-marketplace"></a>Lägga till en modul från Marketplace

Så här lägger du till en modul från Azure Marketplace:

1. Klicka på **Lägg till**i avsnittet **IoT-kantmoduler** på sidan .
1. Välj **Marketplace-modul** på den nedrullningsbara menyn.
1. Välj en modul på **marketplace-sidan IoT Edge Module** Marketplace. Modulen du väljer konfigureras automatiskt för din prenumeration, resursgrupp och enhet. Den visas sedan i din lista över IoT Edge-moduler. Vissa moduler kan kräva ytterligare konfiguration. Mer information finns i [Distribuera moduler från Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Lägga till en Stream Analytics-modul

Så här lägger du till en modul från Azure Stream Analytics:

1. Klicka på **Lägg till**i avsnittet **IoT-kantmoduler** på sidan .
1. Välj **Azure Stream Analytics-modul** på den nedrullningsbara menyn.
1. Välj **prenumeration**i den högra rutan .
1. Välj ditt IoT **Edge-jobb**.
1. Välj **Spara** om du vill lägga till modulen i distributionen.

#### <a name="configure-module-settings"></a>Konfigurera modulinställningar

När du har lagt till en modul i en distribution kan du välja dess namn för att öppna sidan **Uppdatera IoT Edge Module.** På den här sidan kan du redigera modulinställningar, miljövariabler, skapa alternativ och modultvilling. Om du har lagt till en modul från marknadsplatsen kan det redan ha några av dessa parametrar ifyllda.

Om du skapar en distribution i flera lager kan du konfigurera en modul som finns i andra distributioner som är inriktade på samma enheter. Om du vill uppdatera modultvillingen utan att skriva **Module Twin Property** över andra versioner öppnar du fliken `properties.desired.settings` **Modultvillinginställningar.** Om du definierar `properties.desired` egenskaper inom bara fältet skrivs de önskade egenskaperna för modulen som definierats i alla distributioner med lägre prioritet över.

![Ange egenskap för modultvilling för distribution i flera lager](./media/how-to-deploy-monitor/module-twin-property.png)

Mer information om modultvillingkonfiguration i distributioner i flera lager finns i [Lagerbaserad distribution](module-deployment-monitoring.md#layered-deployment).

När du har konfigurerat alla moduler för en distribution väljer du **Nästa: Vägar** för att gå till steg tre.

### <a name="step-3-routes"></a>Steg 3: Rutter

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Som standard ger guiden dig en väg som anropas **uppströms** och definieras som **\* FRÅN /messages/ INTO $upstream**, vilket innebär att alla meddelanden som matas ut av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera flödena med information från [Deklarera vägar](module-composition.md#declare-routes)och välj sedan **Nästa** för att fortsätta till granskningsavsnittet.

Välj **Nästa: Mått**.

### <a name="step-4-metrics"></a>Steg 4: Mått

Mått ger sammanfattningar av de olika tillstånd som en enhet kan rapportera tillbaka som ett resultat av att tillämpa konfigurationsinnehåll.

1. Ange ett namn **för Måttnamn**.

1. Ange en fråga för **måttvillkor**. Frågan baseras på IoT Edge hub module twin [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Måttet representerar antalet rader som returneras av frågan.

   Ett exempel:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Välj **Nästa: Målenheter**.

### <a name="step-5-target-devices"></a>Steg 5: Målenheter

Använd egenskapen tags från dina enheter för att rikta in dig på de specifika enheter som ska ta emot den här distributionen.

Eftersom flera distributioner kan rikta samma enhet bör du ge varje distribution ett prioritetsnummer. Om det någonsin finns en konflikt vinner distributionen med högst prioritet (större värden anger högre prioritet). Om två distributioner har samma prioritetsnummer vinner den som skapades senast.

Om flera distributioner är inriktade på samma enhet används bara den med högre prioritet. Om flera distributioner i flera lager riktar sig till samma enhet tillämpas alla. Men om några egenskaper dupliceras, som om det finns två vägar med samma namn, skriver den från distributionen med högre prioritet resten.

Alla distributioner i flera lager som är inriktade på en enhet måste ha högre prioritet än basdistributionen för att kunna användas.

1. Ange ett positivt heltal för **distributionsprioritet**.
1. Ange ett **målvillkor** för att avgöra vilka enheter som ska riktas med den här distributionen.Villkoret baseras på enhetstvillingtaggar eller enhetstvillingrapporterade egenskaper och bör matcha uttrycksformatet.Exempel: `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`.

Välj **Nästa: Granska + Skapa** om du vill gå vidare till det sista steget.

### <a name="step-6-review-and-create"></a>Steg 6: Granska och skapa

Granska distributionsinformationen och välj sedan **Skapa**.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Så här visar du information om en distribution och övervakar de enheter som kör den:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge-distributioner.**

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Kontrollera distributionslistan.För varje distribution kan du visa följande information:
   * **ID** - namnet på distributionen.
   * **Typ** - typ av distribution, antingen **distribution** eller **lagerdistribution**.
   * **Målvillkor** - taggen som används för att definiera riktade enheter.
   * **Prioritet** - prioritetsnumret som tilldelats distributionen.
   * **Systemmått** - **Riktade** anger antalet enhetstvillingar i IoT Hub som matchar målvillkoret och **Applied** anger antalet enheter som har haft distributionsinnehållet tillämpat på deras modultvillingar i IoT Hub.
   * **Enhetsmått** – antalet IoT Edge-enheter i distributionsrapporteringen lyckades eller fel från IoT Edge-klientkörningen.
   * **Anpassade mått** – antalet IoT Edge-enheter i distributionsrapportningsdata för alla mått som du har definierat för distributionen.
   * **Skapa tid** - tidsstämpeln från när distributionen skapades. Den här tidsstämpeln används för att bryta band när två distributioner har samma prioritet.
1. Välj den distribution som du vill övervaka.  
1. Kontrollera distributionsinformationen. Du kan använda flikar för att granska information om distributionen.

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla riktade enheter.

Om du uppdaterar målvillkoret sker följande uppdateringar:

* Om en enhet inte uppfyllde det gamla målvillkoret, men uppfyller det nya målvillkoret och den här distributionen har högsta prioritet för den enheten, tillämpas den här distributionen på enheten.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret avinstalleras den här distributionen och får den näst högsta prioritetsdistributionen.
* Om en enhet som för närvarande kör den här distributionen inte längre uppfyller målvillkoret och inte uppfyller målvillkoret för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i sitt aktuella tillstånd, men hanteras inte längre som en del av den här distributionen. När den uppfyller målvillkoret för en annan distribution avinstallerar den den här distributionen och tar sig an den nya.

Så här ändrar du en distribution:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge Deployments.**

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Markera den distribution som du vill ändra.
1. Gör uppdateringar på följande flikar:
   * **Målvillkor**
   * **Mått** – du kan ändra eller ta bort mått som du har definierat eller lägga till nya.
   * **Etiketter**
   * **Moduler**
   * **Vägar**
   * **Distribution**

1. Välj **Spara**.
1. Följ stegen i [Övervaka en distribution](#monitor-a-deployment) för att se ändringarna distribueras.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution får alla distribuerade enheter sin nästa distribution med högsta prioritet. Om dina enheter inte uppfyller målvillkoret för någon annan distribution tas modulerna inte bort när distributionen tas bort.

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge Deployments.**

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryssrutan för att markera den distribution som du vill ta bort.
1. Välj **Ta bort**.
1. En uppmaning informerar dig om att den här åtgärden kommer att ta bort den här distributionen och återgå till det tidigare tillståndet för alla enheter.Det innebär att en distribution med lägre prioritet gäller.Om ingen annan distribution är riktad tas inga moduler bort. Om du vill ta bort alla moduler från enheten skapar du en distribution med nollmoduler och distribuerar den till samma enheter.Välj **Ja** om du vill fortsätta.

## <a name="next-steps"></a>Nästa steg

Läs mer om [att distribuera moduler till IoT Edge-enheter](module-deployment-monitoring.md).
