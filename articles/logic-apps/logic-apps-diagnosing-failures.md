---
title: "Felsöka och diagnostisera fel - Azure Logic Apps | Microsoft Docs"
description: "Förstå hur och varför logikappar misslyckas"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: de706f711e9c57b2e575d130a2a0cfd0bdc907a1
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Felsöka och diagnostisera fel i logik app

Din logikapp genererar information som kan hjälpa dig att diagnostisera och felsöka problem i din app. Du kan diagnostisera en logikapp genom att granska varje steg i arbetsflödet via Azure-portalen. Eller du kan lägga till några steg i ett arbetsflöde för felsökning av körningsmiljön.

## <a name="review-trigger-history"></a>Granska utlösaren tidigare

Varje logikapp börjar med utlösare. Om utlösaren inte utlösas först historiken utlösare. Denna historik visar alla utlösare försök som görs i din logikapp och information om indata och utdata för varje utlösare försök.

1. Om du vill kontrollera om åtgärden utlösts på logiken app-menyn, Välj **översikt**. Under **utlösaren historik**, granska utlösarens status.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska utlösaren tidigare](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data som du förväntar dig, försök med att välja **uppdatera** i verktygsfältet.
   > * Om listan innehåller många utlöser försök och du inte hittar du, försök att filtrera listan.

   Här följer de olika statusvärdena för en utlösare försök:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Utlösaren kontrolleras slutpunkten och finns tillgängliga data. Vanligtvis visas statusen ”Fired” också tillsammans med den här statusen. Om utlösardefinition inte kan ha ett condition eller `SplitOn` kommando som inte uppfylldes. <p>Denna status kan använda en manuell utlösare, upprepning utlösare eller avsökningen utlösare. En utlösare kan köras, men kör själva kan fortfarande misslyckas när åtgärderna som genererar ett fel. | 
   | **Hoppades över** | Utlösaren markerad slutpunkten men inga data påträffades. | 
   | **Det gick inte** | Ett fel uppstod. För att granska eventuella felmeddelanden som genereras för en misslyckad utlösare, markera försöket utlösare och välj **utdata**. Exempelvis kanske indata är inte giltiga. | 
   ||| 

   Du kan ha flera utlösare poster med samma datum och tidpunkt som händer när din logikapp hittar flera objekt. 
   Varje gång utlösaren-utlöses Logic Apps motorn skapar logiken app-instansen för att köra arbetsflödet. Som standard körs varje instans parallellt så att inga arbetsflödet måste vänta innan du startar en körning.

   > [!TIP]
   > Du kan kontrollera utlösaren utan att vänta på nästa återkommande. Välj verktygsfältet översikt **köra utlösaren**, och välj utlösare, vilket gör att en kontroll. Eller välj **kör** Logic Apps Designer i verktygsfältet.

3. Granska informationen för en utlösare försök under **utlösaren historik**, Välj utlösaren försöket. 

   ![Välj en utlösare försök](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Granska indata och utdata som genererats av utlösaren. Utlösaren utdata visar data som kommer från utlösaren. Dessa utdata kan hjälpa dig att avgöra om alla egenskaper returneras som förväntat.

   > [!NOTE]
   > Om du hittar allt innehåll som du inte förstår lär du dig hur Azure Logic Apps [hanterar olika typer av innehåll](../logic-apps/logic-apps-content-type.md).

   ![Utlösaren utdata](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Granska körningshistorik

Varje Eldad utlösare startar ett arbetsflöde som körs. Du kan granska vad som hände under som körs, inklusive statusen för varje steg i arbetsflödet, samt indata och utdata för varje steg.

1. På logikappmenyn väljer du **Översikt**. Under **körs historik**, granska kör för Eldad utlösaren.

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

   ![Granska körs historik](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Om du inte hittar de data som du förväntar dig, försök med att välja **uppdatera** i verktygsfältet.
   > * Om du inte hittar du i listan visas många körs och försök filtrera listan.

   Här följer de olika statusvärdena för en körning:

   | Status | Beskrivning | 
   | ------ | ----------- | 
   | **Lyckades** | Alla åtgärder har slutförts. <p>Om några fel har inträffat i en specifik åtgärd, hanterade följande åtgärd i arbetsflödet att fel. | 
   | **Det gick inte** | Minst en åtgärd misslyckades, och inga senare åtgärder i arbetsflödet har ställts in för att hantera felet. | 
   | **Avbröts** | Arbetsflödet kördes, men tog emot en begäran om avbrytning. | 
   | **Kör** | Arbetsflödet körs. <p>Den här statusen kan inträffa för begränsad arbetsflöden eller på grund av den aktuella prisavtal. Mer information finns i [åtgärd gränser för prissättningssidan](https://azure.microsoft.com/pricing/details/logic-apps/). Om du ställer in [diagnostikloggning](../logic-apps/logic-apps-monitor-your-logic-apps.md), du kan också få information om eventuella begränsning av händelser som inträffar. | 
   ||| 

2. Granska informationen för varje steg i en viss körning. Under **körs historik**, Välj Kör som du vill undersöka.

   ![Granska körs historik](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Om kör själva har lyckats eller misslyckats, kör Detaljvyn visas varje steg och om de har lyckats eller misslyckats.

   ![Visa information om en logikappkörning](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Om du vill kontrollera indata, utdata och felmeddelanden för ett specifikt steg, väljer du steget så att formen expanderas och visar information. Exempel:

   ![Visa information om steg](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Utföra runtime-felsökning

Om du vill hjälpa till med felsökning, du kan lägga till diagnostik steg i ett arbetsflöde, tillsammans med granska utlösaren och kör historik. Du kan till exempel lägga till steg som använder den [RequestBin](http://requestb.in) tjänsten så att du kan inspektera HTTP-begäranden och kontrollera deras exakta storlek, form och format.

1. Skapa en RequestBin som du kan göra privata och kan endast visas i webbläsaren.

2. Lägg till en HTTP POST-åtgärd med body-innehåll som du vill testa, till exempel, ett uttryck eller en annan steg utdata i din logikapp.

3. Klistra in Webbadressen för ditt RequestBin i HTTP POST-åtgärd.

4. Kör logikappen för att granska hur en begäran bildas när genereras från Logic Apps-motorn, och uppdatera din RequestBin.

## <a name="next-steps"></a>Nästa steg

[Övervaka din logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md)