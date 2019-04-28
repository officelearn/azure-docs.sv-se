---
title: Skapa automatiska distributioner från Azure portal – Azure IoT Edge | Microsoft Docs
description: Använda Azure portal för att skapa automatiska distributioner för grupper av IoT Edge-enheter
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 69ba0a882c0e52e7c0d063b8f77e7a0fe22526a1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126377"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuera och övervaka IoT Edge-moduler i stor skala med Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge kan du flytta analys vid gränsen och tillhandahåller ett molngränssnitt så att du kan hantera och övervaka din IoT Edge-enheter utan att behöva fysisk åtkomst till var och en. Möjlighet att hantera enheter blir allt viktigare som IOT-lösningar växer större och mer komplexa. Azure IoT Edge är utformat för att stödja dina affärsmål, oavsett hur många enheter som du lägger till.

Du kan hantera enskilda enheter och distribuera moduler till dem i taget. Om du vill göra ändringar på enheter i stor skala, du kan dock skapa en **IoT Edge automatisk distribution**, vilket är en del av automatisk hantering av enheter i IoT Hub. Distributioner är dynamisk processer som gör att du kan distribuera flera moduler till flera enheter på samma gång, spåra status och hälsa moduler och göra ändringar när det behövs. 

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Innan du kan skapa en distribution måste ha för att kunna ange vilka enheter som du vill påverka. Azure IoT Edge identifierar enheter med hjälp av **taggar** i enhetstvillingen. Varje enhet kan ha flera taggar och definiera ett sätt som passar för din lösning. Om du hanterar en campus av smarta byggnader kan du lägga till följande taggar till en enhet:

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

1. I den [Azure-portalen](https://portal.azure.com)går du till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **Lägg till IoT Edge-distribution**.

Det finns fem steg för att skapa en distribution. I följande avsnitt beskriver var och en. 

### <a name="step-1-name-and-label"></a>Steg 1: Namn och etikett

1. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvika blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
1. Du kan lägga till etiketter som nyckel / värde-par till att spåra dina distributioner. Till exempel **HostPlatform** och **Linux**, eller **Version** och **3.0.1**.
1. Välj **nästa** att gå till steg två. 

### <a name="step-2-add-modules-optional"></a>Steg 2: Lägg till moduler (valfritt)

Det finns två typer av moduler som du kan lägga till en distribution. Först är en modul som baseras på en Azure-tjänst, t.ex. Storage-konto eller Stream Analytics. Andra är en modul med din egen kod. Du kan lägga till flera moduler båda typer av en distribution. 

Om du skapar en distribution utan moduler, tar bort alla aktuella moduler från enheterna. 

>[!NOTE]
>Azure Functions stöder inte automatisk Azure tjänstdistributionen ännu. Använd anpassad modul distributionen för att manuellt lägga till tjänsten för din distribution. 

Följ dessa steg för att lägga till en modul från Azure Stream Analytics:

1. I den **distribution moduler** avsnitt på sidan, klickar du på **Lägg till**.
1. Välj **Azure Stream Analytics-modulen**.
1. Välj din **prenumeration** från den nedrullningsbara menyn.
1. Välj din **Edge-jobb** från den nedrullningsbara menyn.
1. Välj **spara** att lägga till din modul i distributionen. 

Lägga till anpassad kod som en modul, eller att manuellt lägga till en Azure-tjänst-modul, gör du följande:

1. I den **inställningar för Behållarregister** avsnitt på sidan Ange namn och autentiseringsuppgifter för alla privata behållarregister som innehåller modulen bilder för den här distributionen. Edge-agenten rapporterar fel 500 om den inte kan hitta autentiseringsuppgifterna för registret behållare för en Docker-avbildning.
1. I den **distribution moduler** avsnitt på sidan, klickar du på **Lägg till**.
1. Välj **IoT Edge-modul**.
1. Ge din modul en **namn**.
1. För den **URI för avbildning** anger behållaravbildningen för din modul. 
1. Ange någon **behållare skapa alternativ** som ska överföras till behållaren. Mer information finns i [docker skapa](https://docs.docker.com/engine/reference/commandline/create/).
1. Använd den nedrullningsbara menyn för att välja en **omstartsprincip**. Välj bland följande alternativ: 
   * **Alltid** -modulen startas alltid om ifall den stänger av någon anledning.
   * **Aldrig** -modulen aldrig startas om ifall den stänger av någon anledning.
   * **På misslyckades** -modulen startar om den kraschar, men inte om den får en ren avstängning. 
   * **På defekta** -modulen startar om den kraschar eller returnerar en skadad status. Det är upp till varje modul att implementera funktionen hälsotillstånd status. 
1. Använd den nedrullningsbara menyn för att välja den **önskad Status** för modulen. Välj bland följande alternativ:
   * **Kör** – det här är standardalternativet. Modulen ska börja köras omedelbart efter att ha distribuerats.
   * **Stoppad** -efter att ha distribuerats modulen kan vara inaktiv tills skall start av dig eller en annan modul.
1. Välj **önskade egenskaper för modultvilling Set** om du vill lägga till taggar och andra egenskaper för modultvillingen.
1. Ange **miljövariabler** för den här modulen. Miljövariabler informera tillägg till en modul underlätta konfigurationsprocessen.
1. Välj **spara** att lägga till din modul i distributionen. 

När du har alla moduler för en distribution som konfigurerats väljer **nästa** att gå till steg tre.

### <a name="step-3-specify-routes-optional"></a>Steg 3: Ange vägar (valfritt)

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Som standard i guiden får du en väg kallas **väg** och definieras som **FROM /\* i $uppströms**, vilket innebär att alla meddelanden som utdata av alla moduler som skickas till din IoT-hubb.  

Lägg till eller uppdatera vägar med information från [deklarera vägar](module-composition.md#declare-routes)och välj sedan **nästa** att fortsätta till avsnittet granskning.

### <a name="step-4-specify-metrics-optional"></a>Steg 4: Ange mått (valfritt)

Mått ger sammanfattning av antalet olika tillstånd som en enhet kan rapportera tillbaka till följd av tillämpa innehåll.

1. Ange ett namn för **måttet namnet**.

1. Ange en fråga för **mått kriterier**. Frågan är baserad på IoT Edge hub modultvilling [rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties). Mätvärdet visar antalet rader som returneras av frågan.

Exempel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="step-5-target-devices"></a>Steg 5: Målenheter

Använd egenskapen taggar från dina enheter för att fokusera på specifika enheter som ska ta emot den här distributionen. 

Eftersom flera distributioner kan samma enhet som mål, bör du ge varje distribution flera prioritet. Om du har en konflikt, wins-distributionen med högst prioritet (högre värden ange högre prioritet). Om två distributioner har samma prioritetsnummer, det som har skapats i de flesta WINS-nyligen. 

1. Ange ett positivt heltal för distributionen **prioritet**. I händelse av att två eller fler distributioner är inriktade på samma enhet, gäller distribution med det högsta numeriska värdet för prioritet.
1. Ange en **rikta villkor** att avgöra vilka enheter som ska användas med den här distributionen. Villkoret är baserat på enhet twin taggar eller enhetstvillingen rapporterade egenskaper och måste matcha uttrycket-format. Till exempel `tags.environment='test'` eller `properties.reported.devicemodel='4000x'`. 
1. Välj **nästa** att gå vidare till det sista steget.

### <a name="step-6-review-deployment"></a>Steg 6: Granska distributionen

Granska information om din distribution, och välj sedan **skicka**.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

Azure Marketplace är en online-program och tjänster där du kan bläddra igenom ett brett utbud av företagsprogram och lösningar som är certifierade och optimerade för körning på Azure, inklusive [IoT Edge-moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan även nås via Azure-portalen under **skapa en resurs**.

Du kan installera en IoT Edge-modul från Azure Marketplace eller Azure-portalen:

1. Hitta en modul och påbörjar distributionen.

   * Azure-portalen: Hitta en modul och välj **skapa**.

   * Azure Marketplace:

     1. Hitta en modul och välj **Hämta nu**.
     1. Bekräfta leverantörens användningsvillkoren och sekretesspolicyn princip genom att välja **Fortsätt**.

1. Välj din prenumeration och IoT-hubben som målenheten är ansluten.

1. Välj **distribuera skalenligt**.

1. Välj om du vill lägga till modulen till en ny distribution eller till en klon av en befintlig distribution; Om kloning, väljer du den befintliga distributionen i listan.

1. Välj **skapa** att fortsätta processen för att skapa en distribution i stor skala. Du kommer att kunna ange samma information som för alla distributioner.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande steg för att visa information om en distribution och övervakning av enheter som kör den:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge-distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Granska listan över distributionen. Du kan visa följande information för varje distribution:
   * **ID** -namnet på distributionen.
   * **Rikta villkor** -taggen används för att definiera målenheter.
   * **Prioritet** -prioritetsnummer för distributionen.
   * **Systemmått** - **riktad** anger hur många enhetstvillingar i IoT-hubb som matchar villkoret Sök mål och **kopplat** anger hur många enheter som har hade i distributionsinformationen tillämpas på deras modultvillingar i IoT Hub. 
   * **Enhetsmått** -antalet Edge-enheter i distributionen rapporterar lyckades eller fel från klienten IoT Edge-körningen.
   * **Anpassade mått** -antalet Edge-enheter i distributionen rapporterar data för alla mått som du definierat för distributionen.
   * **Skapandetid** -tidsstämpel från när distributionen har skapats. Den här tidsstämpeln används för att bryta ties när två distributioner har samma prioritet. 
1. Välj distributionen som du vill övervaka.  
1. Kontrollera distributionen. Du kan använda flikarna för att få mer information om distributionen.

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoret, inträffar följande uppdateringar:

* Om en enhet inte uppfyllde gamla målvillkoret, men uppfyller nya målvillkor och den här distributionen är den högsta prioriteten för enheten, tillämpas den här distributionen på enheten. 
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret, avinstallerar den här distributionen och tar på nästa distributionen med högst prioritet. 
* Om en enhet som körs på den här distributionen inte längre uppfyller målvillkoret och uppfyller inte target villkoret för alla andra distributioner, sker ingen ändring på enheten. Enheten fortsätter att köra dess aktuella moduler i det aktuella tillståndet, men hanteras inte som en del av den här distributionen längre. När den uppfyller målvillkoret för alla andra distributioner, avinstallerar den här distributionen och tar på den nya servern. 

Om du vill ändra en distribution, använder du följande steg: 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge-distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Välj distributionen som du vill ändra. 
1. Gör uppdateringar i följande fält: 
   * Målvillkor
   * Mått – du kan ändra eller ta bort mått som du har definierat eller lägga till nya.
   * Etiketter
   * Prioritet
1. Välj **Spara**.
1. Följ stegen i [övervaka en distribution](#monitor-a-deployment) och se ändringarna lanseras. 

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om din enhet inte uppfyller målvillkoret för alla andra distributioner, tas sedan moduler inte bort när distributionen har tagits bort. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb. 
1. Välj **IoT Edge**.
1. Välj **IoT Edge-distributioner**. 

   ![Visa IoT Edge-distributioner](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Använd kryssrutan för att välja den distribution som du vill ta bort. 
1. Välj **Ta bort**.
1. En uppmaning informerar om att den här åtgärden tar bort den här distributionen och återgå till det tidigare tillståndet för alla enheter.  Det innebär att en distribution med lägre prioritet kommer att gälla.  Om inga andra distribueringen är avsedd tas några moduler bort. Om du vill ta bort alla moduler från din enhet, skapa en distribution med noll moduler och distribuera den till samma enheter. Välj **Ja** att fortsätta. 

## <a name="next-steps"></a>Nästa steg

Läs mer om [distribuerar moduler till gränsenheter](module-deployment-monitoring.md).
