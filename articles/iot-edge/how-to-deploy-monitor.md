---
title: Skapa automatiska distributioner från Azure portal – Azure IoT Edge | Microsoft Docs
description: Använda Azure portal för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457359"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure portal

Skapa en **IoT Edge automatisk distribution** i Azure Portal för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge ingår i funktionen [Automatisk enhets hantering](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter, spåra status och hälsa för modulerna och göra ändringar när det behövs. 

Mer information finns i [förstå IoT Edge automatiska distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste ha för att kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enheten med dubbla. Varje enhet kan ha flera taggar som du definierar på ett sätt som passar din lösning. Om du hanterar en campus av smarta byggnader kan du lägga till följande taggar till en enhet:

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

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **Lägg till IoT Edge distribution**.

Det finns fem steg för att skapa en distribution. I följande avsnitt beskriver var och en. 

### <a name="step-1-name-and-label"></a>Steg 1: Namn och etikett

1. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvik blank steg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
1. Du kan lägga till etiketter som nyckel/värde-par som hjälper dig att spåra dina distributioner. Till exempel **HostPlatform** och **Linux**, eller **version** och **3.0.1**.
1. Välj **Nästa** för att gå vidare till steg två. 

### <a name="step-2-add-modules-optional"></a>Steg 2: Lägg till moduler (valfritt)

Du kan lägga till upp till 20 moduler i en distribution. 

Om du skapar en distribution utan moduler tas alla aktuella moduler bort från mål enheterna. 

Följ dessa steg för att lägga till en modul från Azure Stream Analytics:

1. I avsnittet **distributions moduler** på sidan klickar du på **Lägg till**.
1. Välj **Azure Stream Analytics modul**.
1. Välj din **prenumeration** på den nedrullningsbara menyn.
1. Välj ditt IoT **Edge-jobb** på den nedrullningsbara menyn.
1. Välj **Spara** för att lägga till modulen i distributionen. 

Lägga till anpassad kod som en modul, eller att manuellt lägga till en Azure-tjänst-modul, gör du följande:

1. I avsnittet **container Registry inställningar** på sidan anger du namn och autentiseringsuppgifter för alla privata behållar register som innehåller modulens avbildningar för den här distributionen. IoT Edge-agenten rapporterar fel 500 om den inte kan hitta autentiseringsuppgifterna för behållar registret för en Docker-avbildning.
1. I avsnittet **distributions moduler** på sidan klickar du på **Lägg till**.
1. Välj **IoT Edge modul**.
1. Ge din modul ett **namn**.
1. I fältet **bild-URI** anger du behållar avbildningen för modulen. 
1. Ange eventuella **skapande alternativ för behållare** som ska skickas till behållaren. Mer information finns i [Docker skapa](https://docs.docker.com/engine/reference/commandline/create/).
1. Använd den nedrullningsbara menyn för att välja en **princip för omstart**. Välj bland följande alternativ: 
   * **Always** -modulen startar alltid om den stängs av av någon anledning.
   * **aldrig** -modulen startar aldrig om, om den stängs av av någon anledning.
   * **vid fel** – modulen startas om om den kraschar, men inte om den stängs av på ett säkert sätt. 
   * **on-ohälsosamt** -modulen startar om om den kraschar eller returnerar en felaktig status. Det är upp till varje modul att implementera funktionen hälsotillstånd status. 
1. Använd den nedrullningsbara menyn för att välja **önskad status** för modulen. Välj bland följande alternativ:
   * **körning** – körs är standard alternativet. Modulen ska börja köras omedelbart efter att ha distribuerats.
   * **stoppad** – när modulen har distribuerats förblir modulen inaktiv tills den anropas av dig eller någon annan modul.
1. Välj **Ange önskade egenskaper för modulens egenskaper** om du vill lägga till taggar eller andra egenskaper i modulen.
1. Ange **miljövariabler** för den här modulen. Miljövariabler ger konfigurations information till en modul.
1. Välj **Spara** för att lägga till modulen i distributionen. 

När du har konfigurerat alla moduler för en konfigurerad distribution väljer du **Nästa** för att gå till steg tre.

### <a name="step-3-specify-routes-optional"></a>Steg 3: Ange vägar (valfritt)

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Som standard ger guiden en väg som kallas **väg** och definieras som **från/* till $upstream * *, vilket innebär att alla meddelanden som skickas av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera vägarna med information från [deklarera vägar](module-composition.md#declare-routes), och välj sedan **Nästa** för att fortsätta till gransknings avsnittet.

### <a name="step-4-specify-metrics-optional"></a>Steg 4: ange mått (valfritt)

Mått tillhandahåller sammanfattande antal i de olika tillstånd som en enhet kan rapportera tillbaka till följd av att konfigurations innehåll tillämpas.

1. Ange ett namn för **måttets namn**.

1. Ange en fråga för **mått villkor**. Frågan baseras på IoT Edge Hub-modulens dubbla [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Måttet representerar antalet rader som returneras av frågan.

   Till exempel:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Steg 5: mål enheter

Använd egenskapen taggar från dina enheter för att fokusera på specifika enheter som ska ta emot den här distributionen. 

Eftersom flera distributioner kan samma enhet som mål, bör du ge varje distribution flera prioritet. Om det finns en konflikt är distributionen med högst prioritet (större värden anger högre prioritet) WINS. Om två distributioner har samma prioritetsnummer, det som har skapats i de flesta WINS-nyligen. 

1. Ange ett positivt heltal för distributions **prioriteten**.
1. Ange ett **mål villkor** för att avgöra vilka enheter som ska vara mål för distributionen. Villkoret baseras på enhetens dubbla taggar eller enhets egenskaper med dubbla rapporter och ska överensstämma med uttrycks formatet. Till exempel `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`. 
1. Välj **Nästa** för att gå vidare till det sista steget.

### <a name="step-6-review-deployment"></a>Steg 6: granska distribution

Granska distributions informationen och välj sedan **Skicka**.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

Azure Marketplace är ett online-program och tjänster för tjänster där du kan bläddra igenom en rad olika företags program och-lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan också nås via Azure Portal under **skapa en resurs**.

Du kan distribuera en IoT Edge modul från antingen Azure Marketplace eller Azure Portal:

1. Hitta en modul och påbörja distributions processen.

   * Azure Portal: Leta reda på en modul och välj **skapa**.

   * Azure Marketplace:

     1. Hitta en modul och välj **Hämta den nu**.
     1. Bekräfta providerns användnings villkor och sekretess policy genom att välja **Fortsätt**.

1. Välj din prenumeration och den IoT Hub som mål enheten är ansluten till.

1. Välj **distribuera i skala**.

1. Välj om du vill lägga till modulen i en ny distribution eller en klon av en befintlig distribution. om kloning väljer du den befintliga distributionen i listan.

1. Välj **skapa** för att fortsätta processen med att skapa en distribution i stor skala. Du kommer att kunna ange samma information som för alla distributioner.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande steg för att visa information om en distribution och övervakning av enheter som kör den:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Granska listan över distributionen. För varje distribution kan du se följande information:
   * **ID** – distributionens namn.
   * **Mål villkor** – den tagg som används för att definiera mål enheter.
   * **Prioritet** – prioritets numret som tilldelats distributionen.
   * **System mått** - Target anger antalet enheter i IoT Hub som matchar mål villkoret och **används** **anger det** antal enheter som har haft det distributions innehåll som tillämpats på deras modul i IoT Hub. 
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

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Välj distributionen som du vill ändra. 
1. Gör uppdateringar i följande fält: 
   * Målvillkor
   * Mått – du kan ändra eller ta bort mått som du har definierat eller lägga till nya.
   * Etiketter
   * Prioritet
1. Välj **Spara**.
1. Följ stegen i [övervaka en distribution](#monitor-a-deployment) för att se att ändringarna distribueras. 

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om din enhet inte uppfyller målvillkoret för alla andra distributioner, tas sedan moduler inte bort när distributionen har tagits bort. 

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryssrutan för att välja den distribution som du vill ta bort. 
1. Välj **Ta bort**.
1. En uppmaning informerar om att den här åtgärden tar bort den här distributionen och återgå till det tidigare tillståndet för alla enheter.  Det innebär att en distribution med lägre prioritet kommer att gälla.  Om ingen annan distribution är avsedd tas inga moduler bort. Om du vill ta bort alla moduler från din enhet, skapa en distribution med noll moduler och distribuera den till samma enheter. Välj **Ja** om du vill fortsätta. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att distribuera moduler till IoT Edge enheter](module-deployment-monitoring.md).
