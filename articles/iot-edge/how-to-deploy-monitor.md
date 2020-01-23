---
title: Distribuera moduler i skala i Azure Portal-Azure IoT Edge
description: Använda Azure portal för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510268"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure portal

Skapa en **IoT Edge automatisk distribution** i Azure Portal för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste ha för att kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning.

Om du till exempel hanterar ett campus-nätverk kan du lägga till plats, rums typ och miljö etiketter på en enhet:

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

IoT Edge tillhandahåller två olika typer av automatiska distributioner som du kan använda för att anpassa ditt scenario. Du kan skapa en standard *distribution*, som innehåller system runtime-moduler och eventuella ytterligare moduler och vägar. Varje enhet kan bara använda en distribution. Du kan också skapa en *lager distribution*som bara innehåller anpassade moduler och vägar, inte systemets körnings miljö. Många lager distributioner kan kombineras på en enhet, ovanpå en standard distribution. Mer information om hur de två typerna av automatisk distribution fungerar tillsammans finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Stegen för att skapa en distribution och en lager distribution är mycket lika. Eventuella skillnader anropas i följande steg.

1. I [Azure Portal](https://portal.azure.com)går du till din IoT Hub.
1. I menyn i den vänstra rutan väljer du **IoT Edge** under **Automatisk enhets hantering**.
1. I den övre stapeln väljer du **skapa distribution** eller **skapa lager distribution**.

Det finns fem steg för att skapa en distribution. I följande avsnitt beskriver var och en.

### <a name="step-1-name-and-label"></a>Steg 1: namn och etikett

1. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
1. Du kan lägga till etiketter som nyckel/värde-par som hjälper dig att spåra dina distributioner. Till exempel **HostPlatform** och **Linux**, eller **version** och **3.0.1**.
1. Välj **Nästa: moduler** för att gå vidare till steg två.

### <a name="step-2-modules"></a>Steg 2: moduler

Du kan lägga till upp till 20 moduler i en distribution. Om du skapar en distribution utan moduler tas alla aktuella moduler bort från mål enheterna.

I distributioner kan du hantera inställningarna för IoT Edge agent och IoT Edge Hub-moduler. Välj **körnings inställningar** för att konfigurera de två körnings modulerna. I skiktad distribution inkluderas inte runtime-modulerna så att de inte kan konfigureras.

Du kan lägga till tre typer av moduler:

* IoT Edge modul
* Marketplace-modul
* Azure Stream Analytics modul

#### <a name="add-an-iot-edge-module"></a>Lägg till en IoT Edge-modul

Lägga till anpassad kod som en modul, eller att manuellt lägga till en Azure-tjänst-modul, gör du följande:

1. I avsnittet **container Registry autentiseringsuppgifter** på sidan anger du namn och autentiseringsuppgifter för alla privata behållar register som innehåller modulens avbildningar för den här distributionen. IoT Edge-agenten rapporterar fel 500 om den inte kan hitta autentiseringsuppgifterna för behållar registret för en Docker-avbildning.
1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **IoT Edge modul** på den nedrullningsbara menyn.
1. Ge modulen ett **IoT Edge modulens namn**.
1. För den **URI för avbildning** anger behållaravbildningen för din modul.
1. Använd den nedrullningsbara menyn för att välja en **omstartsprincip**. Välj bland följande alternativ:
   * **Always** -modulen startar alltid om den stängs av av någon anledning.
   * **aldrig** -modulen startar aldrig om, om den stängs av av någon anledning.
   * **vid fel** – modulen startas om om den kraschar, men inte om den stängs av på ett säkert sätt.
   * **on-ohälsosamt** -modulen startar om om den kraschar eller returnerar en felaktig status. Det är upp till varje modul att implementera funktionen hälsotillstånd status.
1. Använd den nedrullningsbara menyn för att välja den **önskad Status** för modulen. Välj bland följande alternativ:
   * **körning** – körs är standard alternativet. Modulen ska börja köras omedelbart efter att ha distribuerats.
   * **stoppad** – när modulen har distribuerats förblir modulen inaktiv tills den anropas av dig eller någon annan modul.
1. Ange någon **behållare skapa alternativ** som ska överföras till behållaren. Mer information finns i [docker skapa](https://docs.docker.com/engine/reference/commandline/create/).
1. Välj **modul, dubbla inställningar** om du vill lägga till taggar eller andra egenskaper i modulen.
1. Ange **miljövariabler** för den här modulen. Miljövariabler ger konfigurations information till en modul.
1. Välj **Lägg till** för att lägga till modulen i distributionen.

#### <a name="add-a-module-from-the-marketplace"></a>Lägg till en modul från Marketplace

Följ dessa steg om du vill lägga till en modul från Azure Marketplace:

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **Marketplace-modul** på den nedrullningsbara menyn.
1. Välj en modul på sidan **IoT Edge modul Marketplace** . Den modul du väljer konfigureras automatiskt för din prenumeration, resurs grupp och enhet. Den visas sedan i listan med IoT Edge moduler. Vissa moduler kan kräva ytterligare konfiguration. Mer information finns i [distribuera moduler från Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Lägg till en Stream Analytics-modul

Följ dessa steg för att lägga till en modul från Azure Stream Analytics:

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **Azure Stream Analytics modul** på den nedrullningsbara menyn.
1. Välj din **prenumeration**i den högra rutan.
1. Välj ditt IoT **Edge-jobb**.
1. Välj **spara** att lägga till din modul i distributionen.

#### <a name="configure-module-settings"></a>Konfigurera inställningar för modul

När du har lagt till en modul i en distribution kan du välja dess namn för att öppna sidan **uppdatera IoT Edge-modul** . På den här sidan kan du redigera inställningarna för modulen, miljövariabler, skapa alternativ och modulens dubbla. Om du har lagt till en modul från Marketplace kanske den redan har en del av de här parametrarna ifyllda.

Om du skapar en lager distribution kan du konfigurera en modul som finns i andra distributioner som riktar sig mot samma enheter. Om du vill uppdatera modulen, utan att skriva över andra versioner, öppnar du fliken med **dubbla inställningar** . skapa en ny **modul, dubbel egenskap** med ett unikt namn för ett underavsnitt i modulens unika egenskaper, till exempel `properties.desired.settings`. Om du definierar egenskaper inom bara `properties.desired` fältet skrivs de önskade egenskaperna för modulen som definierats i eventuella distributioner med lägre prioritet.

![Ställ in modul, delad egenskap för lager distribution](./media/how-to-deploy-monitor/module-twin-property.png)

Mer information om modulens dubbla konfiguration i lager distributioner finns i [lager distribution](module-deployment-monitoring.md#layered-deployment).

När du har konfigurerat alla moduler för en konfigurerad distribution väljer du **Nästa: vägar** för att gå vidare till steg tre.

### <a name="step-3-routes"></a>Steg 3: vägar

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Som standard ger guiden en väg som kallas **överordnad** och definieras som **från/messages/\* till $upstream**, vilket innebär att alla meddelanden som skickas av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera vägar med information från [deklarera vägar](module-composition.md#declare-routes)och välj sedan **nästa** att fortsätta till avsnittet granskning.

Välj **Nästa: mått**.

### <a name="step-4-metrics"></a>Steg 4: mått

Mått tillhandahåller sammanfattande antal i de olika tillstånd som en enhet kan rapportera tillbaka till följd av att konfigurations innehåll tillämpas.

1. Ange ett namn för **måttets namn**.

1. Ange en fråga för **mått villkor**. Frågan baseras på IoT Edge Hub-modulens dubbla [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Måttet representerar antalet rader som returneras av frågan.

   Ett exempel:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Välj **Nästa: mål enheter**.

### <a name="step-5-target-devices"></a>Steg 5: mål enheter

Använd egenskapen taggar från dina enheter för att fokusera på specifika enheter som ska ta emot den här distributionen.

Eftersom flera distributioner kan samma enhet som mål, bör du ge varje distribution flera prioritet. Om det finns en konflikt är distributionen med högst prioritet (större värden anger högre prioritet) WINS. Om två distributioner har samma prioritetsnummer, det som har skapats i de flesta WINS-nyligen.

Om flera distributioner riktar sig till samma enhet används bara den som har den högre prioriteten. Om flera lager distributioner riktar sig till samma enhet används alla. Men om några av egenskaperna dupliceras, som om det finns två vägar med samma namn, skrivs resten av med en lager med högre prioritet.

Alla lager distributioner riktade till en enhet måste ha en högre prioritet än den grundläggande distributionen för att kunna tillämpas.

1. Ange ett positivt heltal för distributionen **prioritet**.
1. Ange en **rikta villkor** att avgöra vilka enheter som ska användas med den här distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`.

Välj **Nästa: granska + skapa** för att gå vidare till det sista steget.

### <a name="step-6-review-and-create"></a>Steg 6: granska och skapa

Granska distributions informationen och välj sedan **skapa**.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande steg för att visa information om en distribution och övervakning av enheter som kör den:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge distributioner** .

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Granska listan över distributionen. För varje distribution kan du se följande information:
   * **ID** -namnet på distributionen.
   * **Typ** – typen av distribution, antingen **distribution** eller **lager distribution**.
   * **Mål villkor** – den tagg som används för att definiera mål enheter.
   * **Prioritet** -prioritetsnummer för distributionen.
   * **Systemmått** - **riktad** anger hur många enhetstvillingar i IoT-hubb som matchar villkoret Sök mål och **kopplat** anger hur många enheter som har hade i distributionsinformationen tillämpas på deras modultvillingar i IoT Hub.
   * **Enhets mått** – antalet IoT Edge enheter i distributions rapporteringen lyckades eller fel från IoT Edge klient körning.
   * **Anpassade mått** – antalet IoT Edge enheter i distributions rapporterings data för alla mät värden som du har definierat för distributionen.
   * **Skapelse tid** : tidsstämpeln från när distributionen skapades. Den här tidsstämpeln används för att bryta ties när två distributioner har samma prioritet.
1. Välj distributionen som du vill övervaka.  
1. Kontrollera distributionen. Du kan använda flikarna för att få mer information om distributionen.

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter.

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhet inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här distributionen är den högsta prioriteten för enheten, tillämpas den här distributionen på enheten.
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret, avinstallerar den här distributionen och tar på nästa distributionen med högst prioritet.
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra dess aktuella moduler i det aktuella tillståndet, men hanteras inte som en del av den här distributionen längre. När den uppfyller målvillkoret för alla andra distributioner, avinstallerar den här distributionen och tar på den nya servern.

Om du vill ändra en distribution, använder du följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge distributioner** .

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Välj distributionen som du vill ändra.
1. Gör uppdateringar på följande flikar:
   * **Mål villkor**
   * **Mått** – du kan ändra eller ta bort mått som du har definierat eller lägga till nya.
   * **Etiketter**
   * **Moduler**
   * **Vägar**
   * **Distribution**

1. Välj **Spara**.
1. Följ stegen i [övervaka en distribution](#monitor-a-deployment) och se ändringarna lanseras.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution börjar alla distribuerade enheter med sin näst högsta prioriterade distribution. Om din enhet inte uppfyller målvillkoret för alla andra distributioner, tas sedan moduler inte bort när distributionen har tagits bort.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge distributioner** .

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryssrutan för att välja den distribution som du vill ta bort.
1. Välj **Ta bort**.
1. En uppmaning informerar om att den här åtgärden tar bort den här distributionen och återgå till det tidigare tillståndet för alla enheter.  Det innebär att en distribution med lägre prioritet kommer att gälla. Om ingen annan distribution är avsedd tas inga moduler bort. Om du vill ta bort alla moduler från din enhet, skapa en distribution med noll moduler och distribuera den till samma enheter. Välj **Ja** om du vill fortsätta.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).
