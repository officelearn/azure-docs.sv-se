---
title: Distribuera, övervaka moduler för Azure IoT kant | Microsoft Docs
description: Hantera de moduler som körs på enheter
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6d024dfdd661d6bebe7d163b96659d6e169cc5cc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Distribuera och övervaka IoT kant moduler i skala - förhandsgranskning

Azure IoT-gräns kan du flytta analytics kant och tillhandahåller ett gränssnitt för molnet så att du kan hantera och övervaka dina kant för IoT-enheter utan att behöva fysiskt kommer åt den. Möjlighet att hantera enheter blir allt viktigare som Internet of Things lösningar växer större och mer komplexa. Azure IoT-gräns är utformad för att stödja affärsmålen, oavsett hur många enheter som du lägger till.

Du kan hantera enskilda enheter och distribuera moduler till dem i taget. Men om du vill göra ändringar i enheter på en stor skala kan du skapa en **IoT kant automatisk distribution**, vilket är en del av automatisk hantering av enheter i IoT-hubb. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter samtidigt, spåra statusen och hälsan på moduler och göra ändringar när det behövs. 

## <a name="identify-devices-using-tags"></a>Identifiera enheter med hjälp av taggar

Du måste kunna ange vilka enheter som du vill påverka innan du kan skapa en distribution. Azure IoT-Edge identifierar enheter med hjälp av **taggar** i dubbla för enheten. Varje enhet kan ha flera etiketter och definiera ett sätt som passar för din lösning. Om du hanterar en plats för smart byggnader kan du exempelvis lägga till följande taggar till en enhet:

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

Mer information om enheten twins och taggar finns [förstå och använda enheten twins i IoT-hubb][lnk-device-twin].

## <a name="create-a-deployment"></a>Skapa en distribution

1. I den [Azure-portalen][lnk-portal], gå till din IoT-hubb. 
1. Välj **IoT kant (förhandsgranskning)**.
1. Välj **Lägg till IoT kant distribution**.

Det finns fem steg för att skapa en distribution. Följande avsnitt beskriver hur vart och ett. 

### <a name="step-1-name-and-label"></a>Steg 1: Ange namnet och etiketten

1. Ge ett unikt namn för din distribution. Undvik blanksteg och följande ogiltiga tecken: `& ^ [ ] { } \ | " < > /`.
1. Lägga till etiketter om du vill spåra dina distributioner. Etiketter är **namn**, **värdet** par som beskriver distributionen. Till exempel `HostPlatform, Linux` eller `Version, 3.0.1`.
1. Välj **nästa** gå vidare till steg två. 

### <a name="step-2-add-modules-optional"></a>Steg 2: Lägg till moduler (valfritt)

Det finns två typer av moduler som du kan lägga till en distribution. Först är en modul som är baserade på en Azure-tjänst, t.ex. Lagringskonto eller Stream Analytics. Andra är en modul som är baserade på din kod. Du kan lägga till flera moduler båda typer av en distribution. 

Om du skapar en distribution med några moduler, tar bort några befintliga moduler från enheter. 

>[!NOTE]
>Azure Machine Learning och Azure Functions stöder inte automatisk Azure tjänstdistributionen ännu. Använd anpassad modul distributionen manuellt lägga till dessa tjänster för din distribution. 

Följ dessa steg om du vill lägga till en modul från Azure Stream Analytics:
1. Välj **importera Azure Stream Analytics IoT kant modulen**.
1. Använd nedrullningsbara menyerna för att välja de Azure-tjänst-instanser som du vill distribuera.
1. Välj **spara** modulen ska läggas till i distributionen. 

Lägga till egen kod som en modul, eller att manuellt lägga till en Azure-tjänst-modul, gör du följande:
1. Välj **Lägg till IoT Edge-modul**.
1. Ge din modulen en **namn**.
1. För den **avbildningen URI** anger Docker behållare bilden för. 
1. Ange vilken **behållare skapa alternativ** som ska skickas till behållaren. Mer information finns i [docker skapa][lnk-docker-create].
1. Använd den nedrullningsbara menyn för att välja en **starta om principen**. Välj bland följande alternativ: 
   * **Alltid** -modulen alltid startar om den stänger av någon anledning.
   * **Aldrig** -modulen startar aldrig om den stänger av någon anledning.
   * **På misslyckades** -modulen startar om den kraschar, men inte om det avslutas korrekt. 
   * **På ohälsosamt** -modulen startas om om den kraschar eller returnerar ett feltillstånd status. Den kan varje modul att implementera funktionen hälsa status. 
1. Använd den nedrullningsbara menyn för att välja den **önskad Status** för modulen. Välj bland följande alternativ:
   * **Kör** -detta är standardalternativet. Modulen ska börja köras omedelbart efter att ha distribuerats.
   * **Stoppats** -modulen efter att ha distribuerats kan vara inaktiv tills du uppmanas för att starta av dig eller en annan modul.
1. Välj **aktivera** om du vill lägga till några taggar eller önskade egenskaper i modulen dubbla. 
1. Välj **spara** modulen ska läggas till i distributionen. 

När du har alla moduler för en distribution som konfigurerats Välj **nästa** gå vidare till steg tre.

### <a name="step-3-specify-routes-optional"></a>Steg 3: Ange vägar (valfritt)

Vägar definierar hur moduler kommunicerar med varandra i en distribution. Ange alla vägar för din distribution, och välj sedan **nästa** gå vidare till steg fyra. 

### <a name="step-4-target-devices"></a>Steg 4: Målenheter

Använd egenskapen taggar från dina enheter för att fokusera på specifika enheter som ska ta emot den här distributionen. 

Eftersom flera distributioner kan mål på samma enhet, bör du ge varje distribution en prioritetsnivå. Om det uppstår en konflikt med den högsta prioritet vinner distributionen. Om båda distributionerna har samma prioritetsnummer, som har skapats i de flesta WINS-nyligen. 

1. Ange ett positivt heltal för distributionen **prioritet**.
1. Ange en **mål villkoret** att avgöra vilka enheter som ska gälla för den här distributionen. Villkoret är baserad på enheten dubbla taggar och ska matcha uttrycket-format. Till exempel `tags.environment='test'`. 
1. Välj **nästa** att gå vidare till det sista steget.

### <a name="step-5-review-template"></a>Steg 5: Granska mall

Granska information om din distribution, och välj sedan **skicka**.

## <a name="monitor-a-deployment"></a>Övervaka en distribution

Använd följande steg om du vill visa information om en distribution och övervaka de enheter som kör det:

1. Logga in på den [Azure-portalen] [ lnk-portal] och navigera till din IoT-hubb. 
1. Välj **IoT kant (förhandsgranskning)**.
1. Välj **IoT kant distributioner**. 

   ![Visa kant för IoT-distributioner][1]

1. Granska listan med distributionen. Du kan visa följande information för varje distribution:
   * **ID** -namnet på distributionen.
   * **Rikta villkoret** -tagg som används för att definiera målenheter.
   * **Prioritet** -prioritetsnummer för distributionen.
   * **IoT-Edge agentstatus** -antalet enheter som tagit emot distributionen och deras hälsa statusar. 
   * **Feltillstånd moduler** -antal moduler i distributionen har rapporterat fel. 
   * **Skapandetid** -tidsstämpeln från när distributionen skapades. Den här tidsstämpel används för att bryta ties när två distributioner har samma prioritet. 
1. Välj distributionen som du vill övervaka.  
1. Kontrollera distributionsinformationen. Du kan använda flikarna för att visa specifik information om de enheter som tog emot distributionen: 
   * **Riktad** -gränsenheterna som matchar villkoret mål. 
   * **Tillämpas** – målenheterna kant som inte omfattas av en annan distribution av högre prioritet. Dessa är de enheter som faktiskt tar emot distributionen. 
   * **Rapportering lyckade** – tillämpas Edge-enheter som rapporterats tillbaka till tjänsten modulerna som har distribuerats. 
   * **Rapportera fel** – tillämpade gränsenheterna som rapporterat tillbaka till tjänsten som en eller flera moduler har inte distribuerats. Om du vill undersöka felet, behöver du fjärransluta till dessa enheter och visa loggfilerna. 
   * **Rapportering ohälsosamt moduler** – tillämpade gränsenheterna som rapporterat tillbaka till tjänsten som en eller flera moduler som har distribuerats, men nu rapporterar fel. 

## <a name="modify-a-deployment"></a>Ändra en distribution

När du ändrar en distribution kan replikera ändringarna direkt till alla målriktade enheter. 

Om du uppdaterar målvillkoren sker följande uppdateringar:
* Om en enhet inte uppfyller villkoret gamla mål men uppfyller villkoret mål och den här distributionen är den högsta prioriteten för enheten, används den här distributionen till enheten. 
* Om en enhet som körs på den här distributionen inte längre uppfyller villkoret mål, avinstallerar den här distributionen och tar på sig nästa högsta prioritet distributionen. 
* Om en enhet som körs på den här distributionen inte längre uppfyller villkoret mål och inte uppfyller villkoret mål för andra distributioner, sker ingen förändring på enheten. Enheten fortsätter att köra de aktuella modulerna i det aktuella tillståndet, men hanteras inte som en del av distributionen längre. När den uppfyller villkoret mål av andra distribution avinstallerar den här distributionen och går till den nya. 

Använd följande steg om du vill ändra en distribution: 

1. Logga in på den [Azure-portalen] [ lnk-portal] och navigera till din IoT-hubb. 
1. Välj **IoT kant (förhandsgranskning)**.
1. Välj **IoT kant distributioner**. 

   ![Visa kant för IoT-distributioner][1]

1. Välj distributionen som du vill ändra. 
1. Göra uppdateringar för följande fält: 
   * Målvillkoren 
   * Etiketter 
   * Prioritet 
1. Välj **Spara**.
1. Följ stegen i [övervaka en distribution] [ anchor-monitor] kan du titta på ändringarna lanserar. 

## <a name="delete-a-deployment"></a>Ta bort en distribution

När du tar bort en distribution kan ta några enheter på deras nästa högsta prioritet distribution. Om dina enheter inte uppfyller villkoret mål av andra distribution, tas sedan moduler inte bort när distributionen har tagits bort. 

1. Logga in på den [Azure-portalen] [ lnk-portal] och navigera till din IoT-hubb. 
1. Välj **IoT kant (förhandsgranskning)**.
1. Välj **IoT kant distributioner**. 

   ![Visa kant för IoT-distributioner][1]

1. Använd kryssrutan för att välja den distribution som du vill ta bort. 
1. Välj **Ta bort**.
1. En uppmaning informerar dig att den här åtgärden tar bort den här distributionen och återgå till det tidigare tillståndet för alla enheter.  Detta innebär att en distribution med lägre prioritet använder.  Om inga andra distribueringen är avsedd tas några moduler bort. Om kunder vill göra detta, behöver de skapar en distribution med noll moduler och distribuerar den till samma enheter. Välj **Ja** om du vill fortsätta. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [distribuerar moduler till gränsenheterna][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
