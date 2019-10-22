---
title: Automatisera Azure Application insikter-processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att snabbt automatisera upprepade processer med hjälp av Application Insights-anslutningen.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678311"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatisera Azure Application Insights-processer med anslutnings programmet för Microsoft Flow

Kan du hitta dig upprepade gånger med samma frågor på dina telemetridata för att kontrol lera att tjänsten fungerar korrekt? Vill du automatisera de här frågorna för att hitta trender och avvikelser och sedan bygga egna arbets flöden runt dem? Azure Application Insights-anslutaren för Microsoft Flow är rätt verktyg för de här syftena.

Med den här integreringen kan du nu automatisera flera processer utan att behöva skriva en enda rad kod. När du har skapat ett flöde med hjälp av en Application Insights åtgärd kör flödet automatiskt din Application Insights Analytics-fråga.

Du kan också lägga till ytterligare åtgärder. Microsoft Flow gör hundratals åtgärder tillgängliga. Du kan till exempel använda Microsoft Flow för att automatiskt skicka ett e-postmeddelande eller skapa en bugg i Azure-DevOps. Du kan också använda en av de många [mallar](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) som är tillgängliga för anslutningen till Microsoft Flow. Dessa mallar påskyndar processen med att skapa ett flöde.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Skapa ett flöde för Application Insights

I den här självstudien får du lära dig hur du skapar ett flöde som använder algoritmen för analys av automatiskt kluster för att gruppera attribut i data för ett webb program. Flödet skickar automatiskt resultaten per e-post, bara ett exempel på hur du kan använda Microsoft Flow och Application Insights Analytics tillsammans.

### <a name="step-1-create-a-flow"></a>Steg 1: skapa ett flöde

1. Logga in på [Microsoft Flow](https://flow.microsoft.com)och välj sedan **mina flöden**.
2. Klicka på **nytt** och sedan **schemalagt – från Tom**.

    ![Skapa nytt flöde från schemalagt tomt](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: skapa en utlösare för ditt flöde

1. I popup-fönstret **skapar du ett schemalagt flöde**, fyller i namnet på ditt flöde och hur ofta du vill att flödet ska köras.

    ![Konfigurera upprepning av schema med att ange frekvens och intervall](./media/automate-with-flow/2-schedule.png)

1. Klicka på **Skapa**.

### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd

1. Sök efter **Application Insights**.
2. Klicka på **Azure Application Insights-visualisera analys fråga**.

    ![Välj en åtgärd: Azure Application Insights visualisera analys fråga](./media/automate-with-flow/3-visualize.png)

3. Välj **Nytt steg**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: ansluta till en Application Insights-resurs

För att slutföra det här steget måste du ha ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure Portal, som du ser i följande diagram:

![Program-ID i Azure Portal](./media/automate-with-flow/5apiaccess.png)

![API-nyckel i Azure Portal](./media/automate-with-flow/6apikey.png)

Ange ett namn för anslutningen, tillsammans med programmets ID och API-nyckel.

   ![Microsoft Flow anslutnings fönster](./media/automate-with-flow/4-connection.png)

Om rutan anslutning inte visas direkt och i stället går direkt till att ange frågan klickar du på ellipserna längst upp till höger i rutan. Välj sedan mina anslutningar eller Använd en befintlig.

Klicka på **Skapa**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange analys frågan och diagram typen
Den här exempel frågan väljer de misslyckade förfrågningarna inom den senaste dagen och korrelerar dem med undantag som inträffat som en del av åtgärden. Analysen korrelerar dem baserat på operation_Id-ID: n. Frågan delar sedan resultatet med hjälp av algoritmen för autokluster.

När du skapar egna frågor måste du kontrol lera att de fungerar korrekt i Analytics innan du lägger till dem i ditt flöde.

- Lägg till följande analys fråga och välj diagram typen HTML-tabell. Välj sedan **nytt steg**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Konfigurations fönster för Analytics-fråga](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Steg 6: konfigurera flödet för att skicka e-post

1. Sök efter **Office 365 Outlook**.
2. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.

    ![Office 365 Outlook Selection-fönster](./media/automate-with-flow/6-outlook.png)

1. I fönstret **Skicka ett e-postmeddelande** :

   a. Ange mottagarens e-postadress.

   b. Ange ett ämne för e-postmeddelandet.

   c. Klicka någonstans i **text** rutan och välj sedan **brödtext**på menyn för dynamiskt innehåll som öppnas till höger.

   e. Välj **Visa avancerade alternativ**

1. På menyn för dynamiskt innehåll:

    a. Välj **namn på bifogad fil**.

    b. Välj **innehåll för bifogad fil**.
    
    c. I rutan **är HTML** väljer du **Ja**.

    ![Office 365 Outlook-konfiguration](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Steg 7: Spara och testa ditt flöde

Klicka på **Save** (Spara).

Du kan vänta tills utlösaren kör den här åtgärden, eller så kan du klicka på ![beaker testikon ](./media/automate-with-flow/testicon.png) **test** överst.

Efter val av **test**:

1. Välj **jag utför utlösnings åtgärden**.
2. Välj **Kör flöde**.

När flödet körs får mottagarna som du har angett i e-postlistan ett e-postmeddelande som det som visas nedan.

![Exempel på e-post](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att skapa [analys frågor](../../azure-monitor/log-query/get-started-queries.md).
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->