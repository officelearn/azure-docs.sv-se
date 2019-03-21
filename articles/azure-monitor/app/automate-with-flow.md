---
title: Automatisera Azure Application Insights processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att automatisera snabbt upprepade processer med hjälp av Application Insights-anslutningen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: cbb9cceef68febec0af991c3d8d5df3e3404f1c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834058"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatisera Azure Application Insights processer med anslutningsappen för Microsoft Flow

Hittar du själv upprepade gånger köra samma frågor på dina telemetridata för att kontrollera att tjänsten fungerar korrekt? Vill du automatisera de här frågorna för att hitta trender och avvikelser och sedan skapa dina egna arbetsflöden runt dem? Azure Application Insights-anslutningsappen för Microsoft Flow är rätt verktyg för dessa ändamål.

Med den här integrationen kan du nu automatisera många processer utan att behöva skriva en enda rad kod. När du har skapat ett flöde med hjälp av en Application Insights-åtgärd körs flödet automatiskt din Application Insights Analytics-fråga. 

Du kan lägga till ytterligare åtgärder samt. Microsoft Flow tillgängliggör hundratals åtgärder. Du kan till exempel använda Microsoft Flow för att automatiskt skicka ett e-postmeddelande eller skapa en bugg i Azure DevOps. Du kan också använda en av många [mallar](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) som är tillgängliga för anslutningsappen för Microsoft Flow. Dessa mallar snabba upp processen med att skapa ett flöde. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Skapa ett flöde för Application Insights

I den här självstudien får du lära dig hur du skapar ett flöde som använder algoritmen Analytics automatiskt-kluster till attributen i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Microsoft Flow och Application Insights Analytics tillsammans. 

### <a name="step-1-create-a-flow"></a>Steg 1: Skapa ett flöde
1. Logga in på [Microsoft Flow](https://flow.microsoft.com), och välj sedan **Mina flöden**.
2. Klicka på **New** sedan **skapa från tom**.

    ![Skapa nytt flöde från början](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: Skapa en utlösare för flödet
1. Fliken i Build-modulen väljer **schema**, och välj sedan **schema – återkommande**.

    ![Välj schemalägga under build](./media/automate-with-flow/2schedule.png)

1. I den de **intervall** anger **1**, och i den **frekvens** väljer **dag**.
2. Klicka på **nytt steg**

    ![Ställ in schema-upprepning med att ange frekvens och intervall](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Sök efter **Azure Application Insights**.
2. Klicka på **Azure Application Insights - visualisera analysfråga**.
 
    ![Välj en åtgärd: Azure Application Insights visualisera Analytics-fråga](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Anslut till en Application Insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen, enligt följande diagram:

![Program-ID i Azure portal](./media/automate-with-flow/5apiaccess.png)

![API-nyckel i Azure portal](./media/automate-with-flow/6apikey.png)

- Ange ett namn för anslutningen, tillsammans med den program-ID och API-nyckeln.

    ![Fönstret för Microsoft Flow-anslutning](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange Analytics-fråga och diagram
Den här exempelfråga väljer misslyckade förfrågningar under den senaste dagen och kopplar dem med undantag som inträffat som en del av åtgärden. Analytics korrelerar dem baserat på operation_Id identifierare. Frågan segment sedan resultatet med hjälp av algoritmen autocluster. 

När du skapar egna frågor kan du kontrollera att de fungerar korrekt i Analytics innan du lägger till den i ditt flöde.

- Lägg till följande Analytics-frågan och välj diagramtyp för HTML-tabell. Välj sedan **nytt steg**.

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
    
    ![Konfigurationsfönstret i Analytics-fråga](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Steg 6: Konfigurera flödet för att skicka e-post

1. Sök efter **Office 365 Outlook**.
2. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.

    ![Office 365 Outlook-urvalsfönstret](./media/automate-with-flow/9outlookaction.png)

1. I den **skicka ett e-postmeddelande** fönstret gör du följande:

   a. Ange e-postadressen till mottagaren.

   b. Ange ett ämne för e-postmeddelandet.

   c. Klicka på den **brödtext** rutan och sedan på dynamiskt innehåll-menyn som öppnas till höger väljer **brödtext**.

   d. Klicka på **visa avancerade alternativ**.

    ![Office 365 Outlook-konfiguration](./media/automate-with-flow/10sendemailbody.png)

1. På menyn om dynamiskt innehåll, gör du följande:

    a. Välj **Bilagenamn**.

    b. Välj **innehåll i bifogad fil**.
    
    c. I den **är HTML** väljer **Ja**.

    ![Konfigurationsfönstret i Office 365 e-post](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Steg 7: Spara och testa ditt flöde
- I den **flödesnamn** , lägga till ett namn för flödet och sedan på **spara**.

    ![Namnge flödet och spara](./media/automate-with-flow/12nameflow.png)

Du kan vänta på att utlösaren ska köras den här åtgärden eller kan du köra flödet direkt av [köra utlösaren på begäran](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

När flödet körs postmeddelande de mottagare som du har angett i listan över e-ett e-som ser ut som följande:

![E-postexempel](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar [analysfrågor](../../azure-monitor/log-query/get-started-queries.md).
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





