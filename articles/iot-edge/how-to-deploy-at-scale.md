---
title: Distribuera moduler i skala i Azure Portal-Azure IoT Edge
description: Använd Azure Portal för att skapa automatiska distributioner för grupper av IoT Edge enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b0952f31bf6e327ff6d9f632f242919139ed6cae
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512018"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera IoT Edge moduler i skala med hjälp av Azure Portal

Skapa en **IoT Edge automatisk distribution** i Azure Portal för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs.

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifiera enheter som använder Taggar

Innan du kan skapa en distribution måste du kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enheten med dubbla. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning.

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

Mer information om enhets sammanflätade och taggar finns i [förstå och använda enhets uppIoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Skapa en distribution

IoT Edge tillhandahåller två olika typer av automatiska distributioner som du kan använda för att anpassa ditt scenario. Du kan skapa en standard *distribution*, som innehåller system runtime-moduler och eventuella ytterligare moduler och vägar. Varje enhet kan bara använda en distribution. Du kan också skapa en *lager distribution*som bara innehåller anpassade moduler och vägar, inte systemets körnings miljö. Många lager distributioner kan kombineras på en enhet, ovanpå en standard distribution. Mer information om hur de två typerna av automatisk distribution fungerar tillsammans finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

Stegen för att skapa en distribution och en lager distribution är mycket lika. Eventuella skillnader anropas i följande steg.

1. I [Azure Portal](https://portal.azure.com)går du till din IoT Hub.
1. I menyn i den vänstra rutan väljer du **IoT Edge** under **Automatisk enhets hantering**.
1. I den övre stapeln väljer du **skapa distribution** eller **skapa lager distribution**.

Det finns fem steg för att skapa en distribution. Följande avsnitt beskriver var och en.

### <a name="step-1-name-and-label"></a>Steg 1: namn och etikett

1. Ge din distribution ett unikt namn som består av upp till 128 små bokstäver. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /` .
1. Du kan lägga till etiketter som nyckel/värde-par som hjälper dig att spåra dina distributioner. Till exempel **HostPlatform** och **Linux**, eller **version** och **3.0.1**.
1. Välj **Nästa: moduler** för att gå vidare till steg två.

### <a name="step-2-modules"></a>Steg 2: moduler

Du kan lägga till upp till 30 moduler i en distribution. Om du skapar en distribution utan moduler tas alla aktuella moduler bort från mål enheterna.

I distributioner kan du hantera inställningarna för IoT Edge agent och IoT Edge Hub-moduler. Välj **körnings inställningar** för att konfigurera de två körnings modulerna. I skiktad distribution inkluderas inte runtime-modulerna så att de inte kan konfigureras.

Du kan lägga till tre typer av moduler:

* IoT Edge modul
* Marketplace-modul
* Azure Stream Analytics modul

#### <a name="add-an-iot-edge-module"></a>Lägg till en IoT Edge-modul

Följ dessa steg om du vill lägga till anpassad kod som en modul eller lägga till en Azure Service-modul manuellt:

1. I avsnittet **container Registry autentiseringsuppgifter** på sidan anger du namn och autentiseringsuppgifter för alla privata behållar register som innehåller modulens avbildningar för den här distributionen. IoT Edge-agenten rapporterar fel 500 om den inte kan hitta autentiseringsuppgifterna för behållar registret för en Docker-avbildning.
1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **IoT Edge modul** på den nedrullningsbara menyn.
1. Ge modulen ett **IoT Edge modulens namn**.
1. I fältet **bild-URI** anger du behållar avbildningen för modulen.
1. Använd den nedrullningsbara menyn för att välja en **princip för omstart**. Välj bland följande alternativ:
   * **Always** -modulen startar alltid om den stängs av av någon anledning.
   * **aldrig** -modulen startar aldrig om, om den stängs av av någon anledning.
   * **vid fel** – modulen startas om om den kraschar, men inte om den stängs av på ett säkert sätt.
   * **on-ohälsosamt** -modulen startar om om den kraschar eller returnerar en felaktig status. Det är upp till varje modul att implementera hälso status funktionen.
1. Använd den nedrullningsbara menyn för att välja **önskad status** för modulen. Välj bland följande alternativ:
   * **körning** – körs är standard alternativet. Modulen börjar köras direkt efter att den har distribuerats.
   * **stoppad** – när modulen har distribuerats förblir modulen inaktiv tills den anropas av dig eller någon annan modul.
1. Ange eventuella **skapande alternativ för behållare** som ska skickas till behållaren. Mer information finns i [Docker skapa](https://docs.docker.com/engine/reference/commandline/create/).
1. Välj **modul, dubbla inställningar** om du vill lägga till taggar eller andra egenskaper i modulen.
1. Ange **miljövariabler** för den här modulen. Miljövariabler ger konfigurations information till en modul.
1. Välj **Lägg till** för att lägga till modulen i distributionen.

#### <a name="add-a-module-from-the-marketplace"></a>Lägg till en modul från Marketplace

Följ dessa steg om du vill lägga till en modul från Azure Marketplace:

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **Marketplace-modul** på den nedrullningsbara menyn.
1. Välj en modul på sidan **IoT Edge modul Marketplace** . Den modul du väljer konfigureras automatiskt för din prenumeration, resurs grupp och enhet. Den visas sedan i listan med IoT Edge moduler. Vissa moduler kan kräva ytterligare konfiguration. Mer information finns i [distribuera moduler från Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Lägg till en Stream Analytics-modul

Följ dessa steg om du vill lägga till en modul från Azure Stream Analytics:

1. I avsnittet **IoT Edge moduler** på sidan klickar du på **Lägg till**.
1. Välj **Azure Stream Analytics modul** på den nedrullningsbara menyn.
1. Välj din **prenumeration**i den högra rutan.
1. Välj ditt IoT **Edge-jobb**.
1. Välj **Spara** för att lägga till modulen i distributionen.

#### <a name="configure-module-settings"></a>Konfigurera inställningar för modul

När du har lagt till en modul i en distribution kan du välja dess namn för att öppna sidan **uppdatera IoT Edge-modul** . På den här sidan kan du redigera inställningarna för modulen, miljövariabler, skapa alternativ och modulens dubbla. Om du har lagt till en modul från Marketplace kanske den redan har en del av de här parametrarna ifyllda.

Om du skapar en lager distribution kan du konfigurera en modul som finns i andra distributioner som riktar sig mot samma enheter. Om du vill uppdatera modulen, utan att skriva över andra versioner, öppnar du fliken **dubbla inställningar** . skapa en ny **modul, dubbel egenskap** med ett unikt namn för ett underavsnitt i modulens två önskade egenskaper, till exempel `properties.desired.settings` . Om du definierar egenskaper inom bara `properties.desired` fältet skrivs de önskade egenskaperna för modulen som definierats i eventuella distributioner med lägre prioritet.

![Ställ in modul, delad egenskap för lager distribution](./media/how-to-deploy-monitor/module-twin-property.png)

Mer information om modulens dubbla konfiguration i lager distributioner finns i [lager distribution](module-deployment-monitoring.md#layered-deployment).

När du har konfigurerat alla moduler för en konfigurerad distribution väljer du **Nästa: vägar** för att gå vidare till steg tre.

### <a name="step-3-routes"></a>Steg 3: vägar

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Som standard ger guiden en väg som heter **överströms** och definieras som **från/Messages/ \* till $upstream**, vilket innebär att alla meddelanden som skickas av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera vägarna med information från [deklarera vägar](module-composition.md#declare-routes), och välj sedan **Nästa** för att fortsätta till gransknings avsnittet.

Välj **Nästa: mått**.

### <a name="step-4-metrics"></a>Steg 4: mått

Mått tillhandahåller sammanfattande antal i de olika tillstånd som en enhet kan rapportera tillbaka till följd av att konfigurations innehåll tillämpas.

1. Ange ett namn för **måttets namn**.

1. Ange en fråga för **mått villkor**. Frågan baseras på IoT Edge Hub-modulens dubbla [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Måttet representerar antalet rader som returneras av frågan.

   Till exempel:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Välj **Nästa: mål enheter**.

### <a name="step-5-target-devices"></a>Steg 5: mål enheter

Använd egenskapen taggar från dina enheter för att rikta in de specifika enheter som ska få den här distributionen.

Eftersom flera distributioner kan vara riktade till samma enhet bör du ge varje distribution ett prioritets nummer. Om det finns en konflikt är distributionen med högst prioritet (större värden anger högre prioritet) WINS. Om två distributioner har samma prioritets nummer, det som skapades senast.

Om flera distributioner riktar sig till samma enhet används bara den som har den högre prioriteten. Om flera lager distributioner riktar sig till samma enhet används alla. Men om några av egenskaperna dupliceras, som om det finns två vägar med samma namn, skrivs resten av med en lager med högre prioritet.

Alla lager distributioner riktade till en enhet måste ha en högre prioritet än den grundläggande distributionen för att kunna tillämpas.

1. Ange ett positivt heltal för distributions **prioriteten**.
1. Ange ett **mål villkor** för att avgöra vilka enheter som ska vara mål för distributionen.Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet.Exempel: `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`.

Välj **Nästa: granska + skapa** för att gå vidare till det sista steget.

### <a name="step-6-review-and-create"></a>Steg 6: granska och skapa

Granska distributions informationen och välj sedan **skapa**.

Information om hur du övervakar distributionen finns i [övervaka IoT Edge distributioner](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution replikeras ändringarna omedelbart till alla mål enheter. Du kan ändra följande inställningar och funktioner för en befintlig distribution:

* Mål villkor
* Anpassade mått
* Etiketter
* Taggar
* Önskade egenskaper

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Ändra mål villkor, anpassade mått och etiketter

1. I din IoT-hubb väljer du **IoT Edge** från menyn i den vänstra rutan.
1. Välj fliken **IoT Edge distributioner** och välj sedan den distribution som du vill konfigurera.
1. Välj fliken **mål villkor** . ändra **mål villkoret** för att rikta in de avsedda enheterna. Du kan också justera **prioriteten**.  Välj **Spara**.

    Om du uppdaterar mål villkoret inträffar följande uppdateringar:

    * Om en enhet inte uppfyller det gamla mål villkoret, men uppfyller det nya mål villkoret och den här distributionen är den högsta prioriteten för den enheten, tillämpas den här distributionen på enheten.
    * Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret avinstalleras distributionen och börjar med den näst högsta prioriterade distributionen.
    * Om en enhet som för närvarande kör distributionen inte längre uppfyller mål villkoret och inte uppfyller mål villkoren för andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra sina aktuella moduler i det aktuella läget, men hanteras inte som en del av distributionen längre. När den uppfyller mål villkoren för en annan distribution avinstallerar den distributionen och tar den nya.

1. Välj fliken **mått** och klicka på knappen **Redigera mått** . Lägg till eller ändra anpassade mått med hjälp av exempel-syntaxen som en guide. Välj **Spara**.

    ![Redigera anpassade mått i en distribution](./media/how-to-deploy-monitor/metric-list.png)

1. Välj fliken **Etiketter** och gör önskade ändringar och välj **Spara**.

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution börjar alla distribuerade enheter med sin näst högsta prioriterade distribution. Om enheterna inte uppfyller mål villkoren för någon annan distribution, tas modulerna inte bort när distributionen tas bort.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge**.
1. Välj fliken **IoT Edge distributioner** .

   ![Visa IoT Edge distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryss rutan för att välja den distribution som du vill ta bort.
1. Välj **Ta bort**.
1. En prompt informerar dig om att den här åtgärden tar bort den här distributionen och återgår till det tidigare läget för alla enheter.En distribution med lägre prioritet kommer att gälla.Om ingen annan distribution är avsedd tas inga moduler bort. Om du vill ta bort alla moduler från enheten skapar du en distribution med noll moduler och distribuerar den till samma enheter.Fortsätt genom att välja **Ja**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).
