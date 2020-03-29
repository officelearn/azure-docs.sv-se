---
title: Övervaka status, visa historik och ställa in aviseringar
description: Felsöka logikappar genom att kontrollera körstatus, granska utlösarhistorik och aktivera aviseringar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907778"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Övervaka körstatus, granska utlösarhistorik och konfigurera aviseringar för Azure Logic Apps

När du [har skapat och kört en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)kan du kontrollera att logikappens körstatus, kör [historik,](#review-runs-history) [utlösarhistorik](#review-trigger-history)och prestanda. Om du vill få meddelanden om fel eller andra möjliga problem ställer du in [aviseringar](#add-azure-alerts). Du kan till exempel skapa en avisering som identifierar "när fler än fem körningar misslyckas på en timme".

För händelseövervakning i realtid och rikare felsökning konfigurerar du diagnostikloggning för logikappen med hjälp av [Azure Monitor-loggar](../azure-monitor/overview.md). Den här Azure-tjänsten hjälper dig att övervaka dina molnmiljöer och lokala miljöer så att du lättare kan underhålla deras tillgänglighet och prestanda. Du kan sedan hitta och visa händelser, till exempel utlösa händelser, köra händelser och åtgärdshändelser. Genom att lagra den här informationen i [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md)kan du skapa [loggfrågor](../azure-monitor/log-query/log-query-overview.md) som hjälper dig att hitta och analysera den här informationen. Du kan också använda dessa diagnostikdata med andra Azure-tjänster, till exempel Azure Storage och Azure Event Hubs. Mer information finns i [Övervaka logikappar med hjälp av Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Granskning kör historia

Varje gång utlösaren utlöses för ett objekt eller en händelse skapar och kör Logic Apps-motorn en separat arbetsflödesinstans för varje objekt eller händelse. Som standard körs varje arbetsflödesinstans parallellt så att inget arbetsflöde måste vänta innan du startar en körning. Du kan granska vad som hände under körningen, inklusive status för varje steg i arbetsflödet plus indata och utdata för varje steg.

1. I [Azure-portalen](https://portal.azure.com)hittar och öppnar du logikappen i Logic App Designer.

   Om du vill hitta logikappen anger `logic apps`du i den huvudsakliga Sökrutan i Azure och väljer sedan **Logic Apps**.

   ![Hitta och välj tjänsten "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure-portalen visar alla logikappar som är associerade med dina Azure-prenumerationer. Du kan filtrera den här listan baserat på namn, prenumeration, resursgrupp, plats och så vidare.

   ![Visa logikappar som är associerade med prenumerationer](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Välj logikapp och välj sedan **Översikt**.

   I översiktsfönstret visas alla tidigare, aktuella och alla väntar körningar för logikappen under **Runs-historik.** Om listan visar många körningar och du inte hittar den post du vill använda kan du prova att filtrera listan. Om du inte hittar de data som du förväntar dig kan du prova att välja **Uppdatera** i verktygsfältet.

   ![Översikt, kör historik och annan logikappinformation](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Här är möjliga statusar för en logikappkörning:

   | Status | Beskrivning |
   |--------|-------------|
   | **Avbrutet** | Arbetsflödet kördes men fick en avbryt begäran |
   | **Misslyckades** | Minst en åtgärd misslyckades och inga senare åtgärder i arbetsflödet har ställts in för att hantera felet |
   | **Körs** | Arbetsflödet körs för närvarande. <p>Den här statusen kan också visas för begränsade arbetsflöden eller på grund av den aktuella prisplanen. Mer information finns i [åtgärdsgränserna på prissidan](https://azure.microsoft.com/pricing/details/logic-apps/). Om du ställer in [diagnostikloggning](../logic-apps/monitor-logic-apps.md)kan du få information om eventuella begränsningshändelser som inträffar. |
   | **Lyckades** | Alla åtgärder lyckades. <p>**Om**några fel har inträffat i en viss åtgärd hanterade en senare åtgärd i arbetsflödet felet. |
   | **Väntar** | Arbetsflödet har inte startat eller pausats, till exempel på grund av ett tidigare arbetsflöde som fortfarande körs. |
   |||

1. Om du vill granska stegen och annan information för en viss körning väljer du den körningen under **Körshistorik.**

   ![Välj en viss körning som ska granskas](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   I fönstret **Logikappkörning** visas varje steg i den valda körningen, varje stegs körningsstatus och den tid det tar för varje steg att köra, till exempel:

   ![Varje åtgärd i den specifika körningen](./media/monitor-logic-apps/logic-app-run-pane.png)

   Om du vill visa den här informationen i listformulär väljer du **Kör detaljer**i verktygsfältet **Logikapp som körs** .

   ![Välj "Kör information" i verktygsfältet](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Vyn Kör information visar varje steg, deras status och annan information.

   ![Granska information om varje steg i körningen](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Du kan till exempel hämta egenskapen **Correlation ID** som du kan behöva när du använder [REST API för Logic Apps](https://docs.microsoft.com/rest/api/logic).

1. Om du vill ha mer information om ett visst steg väljer du något av alternativen:

   * I fönstret **Logikapp körs** väljer du steget så att formen expanderas. Nu kan du visa information som indata, utdata och eventuella fel som inträffade i det steget, till exempel:

     ![I fönstret logikappkörning misslyckades vyn](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Välj önskat steg i informationsfönstret **för logikappkörning.**

     ![I fönstret Kör information misslyckades vyn](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Nu kan du visa information som indata och utdata för det steget, till exempel:

   > [!NOTE]
   > Alla körningsinformation och händelser krypteras i Logic Apps-tjänsten. De dekrypteras endast när en användare begär att få visa dessa data. Du kan [dölja indata och utdata i körningshistorik](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) eller styra användarnas åtkomst till den här informationen med hjälp av [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md)

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Granska utlösarhistorik

Varje logikappkörning börjar med en utlösare. Utlösarhistoriken visar alla utlösarförsök som logikappen har gjort och information om indata och utdata för varje utlösarförsök.

1. I [Azure-portalen](https://portal.azure.com)hittar och öppnar du logikappen i Logic App Designer.

   Om du vill hitta logikappen anger `logic apps`du i den huvudsakliga Sökrutan i Azure och väljer sedan **Logic Apps**.

   ![Hitta och välj tjänsten "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure-portalen visar alla logikappar som är associerade med dina Azure-prenumerationer. Du kan filtrera den här listan baserat på namn, prenumeration, resursgrupp, plats och så vidare.

   ![Visa logikappar som är associerade med prenumerationer](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Välj logikapp och välj sedan **Översikt**.

1. På logikappens meny väljer du **Översikt**. Välj **Se utlösarhistorik**under **Utvärdering**i avsnittet **Sammanfattning** .

   ![Visa utlösarhistorik för logikappen](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Fönstret utlösarhistorik visar alla utlösarförsök som logikappen har gjort. Varje gång utlösaren utlöses för ett objekt eller en händelse skapar Logic Apps-motorn en separat logikappinstans som kör arbetsflödet. Som standard körs varje instans parallellt så att inget arbetsflöde måste vänta innan du startar en körning. Så om logikappen utlöses på flera objekt samtidigt visas en utlösarpost med samma datum och tid för varje objekt.

   ![Flera utlösarförsök för olika objekt](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Här är möjliga statusar för ett utlösarförsök:

   | Status | Beskrivning |
   |--------|-------------|
   | **Misslyckades** | Ett fel inträffade. Om du vill granska genererade felmeddelanden för en misslyckad utlösare markerar du utlösarförsöket och väljer **Utdata**. Du kan till exempel hitta indata som inte är giltiga. |
   | **Överhoppad** | Utlösaren kontrollerade slutpunkten men hittade inga data. |
   | **Lyckades** | Utlösaren kontrollerade slutpunkten och hittade tillgängliga data. Vanligtvis visas även statusen "Sparken" vid sidan av den här statusen. Om inte, kan utlösardefinitionen `SplitOn` ha ett villkor eller kommando som inte uppfylldes. <p>Den här statusen kan tillämpas på en manuell utlösare, återkommande utlösare eller avsökningsutlösare. En utlösare kan köras, men själva körningen kan fortfarande misslyckas när åtgärderna genererar ohanterade fel. |
   |||

   > [!TIP]
   > Du kan kontrollera utlösaren igen utan att vänta på nästa upprepning. I översiktsverktygsfältet väljer du **Kör utlösare**och väljer utlösaren, vilket tvingar en kontroll. Du kan också välja **Kör** i verktygsfältet Logikappdesigner.

1. Om du vill visa information om ett visst utlösarförsök markerar du utlösarhändelsen i utlösarfönstret. Om listan visar många utlösarförsök och du inte hittar den post du vill använda kan du prova att filtrera listan. Om du inte hittar de data som du förväntar dig kan du prova att välja **Uppdatera** i verktygsfältet.

   ![Visa specifika utlösarförsök](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Nu kan du granska information om den valda utlösarhändelsen, till exempel:

   ![Visa specifik utlösarinformation](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Ställ in övervakningsaviseringar

Om du vill få aviseringar baserat på specifika mått eller överskridit tröskelvärden för logikappen [konfigurerar](../azure-monitor/platform/alerts-overview.md)du aviseringar i Azure Monitor . Lär dig mer om [mått i Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Så här konfigurerar du aviseringar utan att använda [Azure Monitor.](../log-analytics/log-analytics-overview.md)

1. Välj Aviseringar Ny > **varningsregel** **Alerts**under **Övervakning**på logikapp-menyn.

   ![Lägga till en avisering för logikappen](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Välj logikappen under **Resurs**i **fönstret Skapa regel** om den inte redan är markerad. Under **Villkor**väljer du **Lägg till** så att du kan definiera villkoret som utlöser aviseringen.

   ![Lägga till ett villkor för regeln](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Leta reda på och välj den signal som du vill få en avisering för i fönstret **Konfigurera signallogik.** Du kan använda sökrutan eller sortera signalerna alfabetiskt, välja kolumnrubriken **Signalnamn.**

   Om du till exempel vill skicka en avisering när en utlösare misslyckas gör du så här:

   1. Leta reda på och markera signalen **Utlösare misslyckades** i kolumnen **Signalnamn.**

      ![Välj signal för att skapa avisering](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Ställ in villkoret under **Varningslogik**i informationsfönstret som öppnas för den valda signalen:

   1. För **Operator**väljer du **Större än eller lika med**.

   1. För **aggregeringstyp**väljer du **Antal**.

   1. För **tröskelvärde** `1`anger du .

   1. Under **Förhandsgranskning av villkor**bekräftar du att ditt tillstånd verkar korrekt.

   1. Under **Utvärderad baserat på**ställer du in intervallet och frekvensen för att köra aviseringsregeln. För **aggregeringsgranularitet (Period)** väljer du den period som ska grupperas för data. För **Utvärderingsfrekvens**väljer du hur ofta du vill kontrollera villkoret.

   1. När du är klar väljer du **Klar**.

   Här är det färdiga villkoret:

   ![Ställ in villkor för avisering](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Sidan **Skapa regel** visar nu det villkor som du skapade och kostnaden för att köra aviseringen.

   ![Ny avisering på sidan "Skapa regel"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Ange ett namn, valfri beskrivning och allvarlighetsgrad för aviseringen. Lämna antingen **inställningen Aktivera när** du skapar aktiverad eller stäng av tills du är redo att aktivera regeln.

1. När du är klar väljer du **Skapa aviseringsregel**.

> [!TIP]
> Om du vill köra en logikapp från en avisering kan du inkludera [utlösaren för begäran](../connectors/connectors-native-reqres.md) i arbetsflödet, vilket gör att du kan utföra uppgifter som dessa exempel:
> 
> * [Post till Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Skicka ett sms](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Lägga till ett meddelande i en kö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Nästa steg

* [Övervaka logikappar med hjälp av Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)