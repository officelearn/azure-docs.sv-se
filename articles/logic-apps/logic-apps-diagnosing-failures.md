---
title: Felsöka och diagnostisera fel – Azure Logic Apps | Microsoft Docs
description: Lär dig att felsöka och diagnostisera fel i arbetsflödet i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999822"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Felsöka och diagnostisera fel i arbetsflödet i Azure Logic Apps

Logikappen genererar information som kan hjälpa dig att diagnostisera och felsöka problem i din app. Du kan diagnostisera en logikapp genom att granska varje steg i arbetsflödet via Azure portal. Eller du kan lägga till några steg i ett arbetsflöde för körningsfelsökning.

## <a name="review-trigger-history"></a>Granska utlösarhistorik

Varje logikapp börjar med utlösare. Om utlösaren inte utlöses, kontrollera först utlösaren historiken. Historiken när du visar en lista över alla utlösare försök som gjorts av din logikapp och information om indata och utdata för varje utlösare försök.

1. Om du vill kontrollera om utlösaren utlöstes på logikappmenyn, Välj **översikt**. Under **Utlösarhistorik**, granska utlösarens status.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska utlösarhistorik](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data som du förväntar dig kan du prova att välja **uppdatera** i verktygsfältet.
   > * Om listan visar många utlösa försök och du inte kan hitta den post som du vill använda, försök att filtrera listan.

   Här följer de möjliga tillstånden för en utlösare försök:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Utlösaren kontrolleras slutpunkten och hitta tillgängliga data. Vanligtvis visas statusen ”Fired” också tillsammans med denna status. Om inte, utlösardefinitionen kanske ett villkor eller `SplitOn` kommando som inte uppfylldes. <p>Den här statusen kan använda för en manuell utlösare eller upprepningsutlösare avsökningen utlösaren. En utlösare kan köras, men körningen själva kan fortfarande misslyckas när åtgärderna som genererar ett ohanterat fel. | 
   | **Överhoppad** | Utlösaren markerat slutpunkten men inga data att hitta. | 
   | **Misslyckades** | Ett fel uppstod. För att granska eventuella felmeddelanden som genereras för en misslyckad utlösare, Välj det försöket utlösare och välj **utdata**. Exempelvis kanske indata som inte är giltigt. | 
   ||| 

   Du kan ha flera utlösare poster med samma datum och tid, som sker när logikappen hittar flera objekt. 
   Varje gång utlösaren utlöses skapar Logic Apps-motorn skapar en logikappinstans för att köra arbetsflödet. Som standard varje instans som körs parallellt så att inga arbetsflödet måste vänta innan du påbörjar en körning.

   > [!TIP]
   > Du kan kontrollera utlösaren utan att vänta tills nästa upprepningen. Översikt över-verktygsfältet **Körningsutlösaren**, och välj utlösaren, vilket tvingar en kontroll. Eller välj **kör** Logic Apps Designer-verktygsfältet.

3. Att granska informationen för en utlösare försök under **Utlösarhistorik**, Välj det försöket för utlösaren. 

   ![Välj en utlösare-försök](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Granska indata och utdata som genererats av utlösaren. Utlösarutdata visar data som kommer från utlösaren. Dessa utdata kan hjälpa dig att avgöra om alla egenskaper returneras som förväntat.

   > [!NOTE]
   > Om du hittar allt innehåll som du inte förstår, lär du dig hur Azure Logic Apps [hanterar olika typer av innehåll](../logic-apps/logic-apps-content-type.md).

   ![Utlösarutdata](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Granska körningshistorik

Varje standardaktiverade utlösaren startar ett arbetsflöde som körs. Du kan granska vad som hände under att köra, inklusive statusen för varje steg i arbetsflödet, plus indata och utdata för varje steg.

1. På logikappmenyn väljer du **Översikt**. Under **Körningshistorik**, granska körningen av den standardaktiverade utlösaren.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska körningshistorik](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data som du förväntar dig kan du prova att välja **uppdatera** i verktygsfältet.
   > * Om listan visar många körs, och du inte kan hitta den post som du vill använda, försök att filtrera listan.

   Här följer de möjliga tillstånden för en körning:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Alla åtgärder har slutförts. <p>Om några fel har inträffat i en specifik åtgärd hanterade en följande åtgärd i arbetsflödet som fel. | 
   | **Misslyckades** | Minst en åtgärd misslyckades och inga senare åtgärder i arbetsflödet har ställts in för att hantera felet. | 
   | **Har avbrutits** | Arbetsflödet kördes men tog emot en begäran om att avbryta. | 
   | **Körs** | Arbetsflödet körs. <p>Den här statusen kan ske för begränsade arbetsflöden eller på grund av den aktuella prisplanen. Mer information finns i den [åtgärd gränserna på sidan med priser](https://azure.microsoft.com/pricing/details/logic-apps/). Om du har konfigurerat [diagnostikloggning](../logic-apps/logic-apps-monitor-your-logic-apps.md), du kan också få information om eventuella begränsning händelser som inträffar. | 
   ||| 

2. Granska informationen för varje steg i en specifik körning. Under **Körningshistorik**, Välj det kör som du vill undersöka.

   ![Granska körningshistorik](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Om körningen själva har lyckats eller misslyckats i körningsinformation visas varje steg och om de har lyckats eller misslyckats.

   ![Visa information om en logikappkörning](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Om du vill kontrollera indata, utdata och eventuella felmeddelanden för ett specifikt steg att välja det steget så att formen expanderar och visar information. Exempel:

   ![Visa information om steg](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Utföra körningsfelsökning

För att hjälpa till med felsökning, du kan lägga till steg i ett arbetsflöde, tillsammans med granska utlösaren diagnostik- och körningshistorik. Du kan till exempel lägga till åtgärder som använder den [Webhook Tester](https://webhook.site/) tjänsten så att du kan inspektera HTTP-begäranden och kontrollera deras exakt storlek, form och format.

1. Besök [Webhook Tester](https://webhook.site/) och kopiera den unika URL: en som har skapats

2. Lägg till en HTTP POST-åtgärd med brödtext som du vill testa, till exempel, ett uttryck eller en annan steg utdata i din logikapp.

3. Klistra in URL: en för Webhook-Tester i HTTP POST-åtgärd.

4. Om du vill granska hur en begäran har formaterats när genereras från Logic Apps-motorn, kör logikappen och se Webhooken testprogram för mer information.

## <a name="next-steps"></a>Nästa steg

[Övervaka din logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md)
