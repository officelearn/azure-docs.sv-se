---
title: Felsöka och diagnostisera fel
description: Lär dig hur du felsöker och diagnostiserar arbets flödes fel i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 93b6d9d2975aa1758afffd19deb1d315b974cc47
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790777"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Felsöka och diagnostisera arbets flödes fel i Azure Logic Apps

Din Logic app genererar information som kan hjälpa dig att diagnostisera och felsöka problem i din app. Du kan diagnostisera en Logic-app genom att granska varje steg i arbets flödet via Azure Portal. Du kan också lägga till några steg i ett arbets flöde för körnings fel sökning.

## <a name="review-trigger-history"></a>Granska utlösarens historik

Varje Logic app börjar med utlösare. Om utlösaren inte utlöses ska du först kontrol lera utlösarens historik. Den här historiken visar alla utlösare försök som din Logi Kap par har gjort och information om indata och utdata för varje Utlös ande försök.

1. Om du vill kontrol lera om utlösaren har startats väljer du **Översikt**på din Logic app-meny. Granska utlösarens status under **utlösarens historik**.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska utlösarens historik](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data du förväntar dig kan du prova att välja **Uppdatera** i verktygsfältet.
   > * Om listan visar många Utlös ande försök och du inte hittar den post som du vill använda kan du prova att filtrera listan.

   Här följer möjliga status för ett utlösarnamn-försök:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Utlösaren kontrollerade slut punkten och hittade tillgängliga data. Normalt visas statusen "utlöst" även tillsammans med denna status. I så fall kan utlösnings definitionen ha ett villkor eller `SplitOn` kommando som inte uppfylldes. <p>Den här statusen kan gälla för en manuell utlösare, upprepnings utlösare eller avsöknings utlösare. En utlösare kan köras utan problem, men själva körningen kan fortfarande Miss lyckas när åtgärderna genererar ohanterade fel. | 
   | **Överhoppad** | Utlösaren kontrollerade slut punkten men hittade inga data. | 
   | **Misslyckades** | Ett fel uppstod. Om du vill granska eventuella genererade fel meddelanden för en misslyckad utlösare väljer du det Utlös ande försöket och väljer **utdata**. Du kan till exempel hitta indata som inte är giltiga. | 
   ||| 

   Du kan ha flera utlösnings poster med samma datum och tid, som inträffar när din Logi Kap par söker efter flera objekt. 
   Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans för att köra arbets flödet. Som standard körs varje instans parallellt så att inget arbets flöde måste vänta innan en körning påbörjas.

   > [!TIP]
   > Du kan kontrol lera utlösaren igen utan att vänta på nästa upprepning. I verktygsfältet översikt väljer du **Kör utlösare**och väljer utlösaren, vilket tvingar en kontroll. Eller Välj **Kör** i Logic Apps designer-verktygsfältet.

3. Välj det Utlös ande försöket under **utlösarens historik**för att granska informationen för ett Utlös ande försök. 

   ![Välj ett Utlös ande försök](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Granska indata och eventuella utdata som genereras av utlösaren. Utlös utdata visar de data som kom från utlösaren. Dessa utdata kan hjälpa dig att avgöra om alla egenskaper returneras som förväntat.

   > [!NOTE]
   > Om du hittar något innehåll som du inte förstår kan du lära dig hur Azure Logic Apps [hanterar olika innehålls typer](../logic-apps/logic-apps-content-type.md).

   ![Utlös utdata](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Granska körningshistorik

Varje utlöst utlösare startar en arbets flödes körning. Du kan granska vad som hände under körningen, inklusive status för varje steg i arbets flödet, plus indata och utdata för varje steg.

1. På logikappmenyn väljer du **Översikt**. Under **körnings historiken**granskar du körningen för den utlöst utlösaren.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska körnings historik](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data du förväntar dig kan du prova att välja **Uppdatera** i verktygsfältet.
   > * Om listan visar många körningar och du inte hittar den post som du vill använda kan du prova att filtrera listan.

   Här följer möjliga status värden för en körning:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Alla åtgärder har genomförts. <p>Om ett problem inträffar i en speciell åtgärd, hanterade en följande åtgärd i arbets flödet detta problem. | 
   | **Misslyckades** | Minst en åtgärd misslyckades, och inga senare åtgärder i arbets flödet har kon figurer ATS för att hantera fel. | 
   | **Avbröts** | Arbets flödet kördes men tog emot en Cancel-begäran. | 
   | **Körs** | Arbets flödet körs för närvarande. <p>Den här statusen kan inträffa för begränsade arbets flöden eller på grund av den aktuella pris planen. Mer information finns i [Åtgärds gränserna på sidan med priser](https://azure.microsoft.com/pricing/details/logic-apps/). Om du konfigurerar [diagnostikloggning](../logic-apps/logic-apps-monitor-your-logic-apps.md)kan du också få information om eventuella begränsnings händelser som inträffar. | 
   ||| 

2. Granska informationen för varje steg i en speciell körning. Under **körnings historik**väljer du den körning som du vill undersöka.

   ![Granska körnings historik](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Oavsett om körningen lyckades eller inte fungerar visar vyn kör information varje steg och om de lyckades eller misslyckades.

   ![Visa information om en logikappkörning](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Om du vill undersöka indata, utdata och eventuella fel meddelanden för ett särskilt steg väljer du det steget så att formen expanderar och visar informationen. Exempel:

   ![Visa information om steg](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Utför körnings fel sökning

Om du vill ha hjälp med fel sökning kan du lägga till diagnostiska steg till ett arbets flöde, tillsammans med att granska utlösaren och köra historiken. Du kan till exempel lägga till steg som använder [webhook tester](https://webhook.site/) -tjänsten så att du kan kontrol lera HTTP-begäranden och bestämma exakt storlek, form och format.

1. Besök [webhook-testaren](https://webhook.site/) och kopiera den unika URL: en som skapats

2. I din Logic app lägger du till en HTTP POST-åtgärd med det innehålls innehåll som du vill testa, till exempel ett uttryck eller en annan steg-utdata.

3. Klistra in URL: en för webhook-testaren i HTTP POST-åtgärden.

4. Om du vill granska hur en begäran bildas när den genereras från Logic Apps-motorn kör du Logic-appen och läser webhook-testaren för mer information.

## <a name="next-steps"></a>Nästa steg

[Övervaka din Logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md)
