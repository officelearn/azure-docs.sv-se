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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: 65909e13c75ae4d2577ea29f562b841a1eb20477
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256433"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatisera Azure Application Insights processer med anslutningsappen för Microsoft Flow

Hittar du själv upprepade gånger köra samma frågor på dina telemetridata för att kontrollera att tjänsten fungerar korrekt? Vill du automatisera de här frågorna för att hitta trender och avvikelser och sedan skapa dina egna arbetsflöden runt dem? Azure Application Insights-anslutningsapp (förhandsversion) för Microsoft Flow är rätt verktyg för dessa ändamål.

Med den här integrationen kan du nu automatisera många processer utan att behöva skriva en enda rad kod. När du har skapat ett flöde med hjälp av en Application Insights-åtgärd körs flödet automatiskt din Application Insights Analytics-fråga. 

Du kan lägga till ytterligare åtgärder samt. Microsoft Flow tillgängliggör hundratals åtgärder. Du kan till exempel använda Microsoft Flow för att automatiskt skicka ett e-postmeddelande eller skapa en bugg i Azure DevOps. Du kan också använda en av många [mallar](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) som är tillgängliga för anslutningsappen för Microsoft Flow. Dessa mallar snabba upp processen med att skapa ett flöde. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Skapa ett flöde för Application Insights

I den här självstudien får du lära dig hur du skapar ett flöde som använder algoritmen Analytics automatiskt-kluster till attributen i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Microsoft Flow och Application Insights Analytics tillsammans. 

### <a name="step-1-create-a-flow"></a>Steg 1: Skapa ett flöde
1. Logga in på [Microsoft Flow](https://flow.microsoft.com), och välj sedan **Mina flöden**.
1. Klicka på **skapa ett flöde från början**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: Skapa en utlösare för flödet
1. Välj **schema**, och välj sedan **schema – återkommande**.
1. I den **frekvens** väljer **dag**, och i den **intervall** anger **1**.

    ![Dialogrutan för Microsoft Flow-utlösare](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Klicka på **nytt steg**, och klicka sedan på **Lägg till en åtgärd**.
1. Sök efter **Azure Application Insights**.
1. Klicka på **Azure Application Insights - visualisera Analytics fråga förhandsversion**.

    ![Kör Analytics frågefönster](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Anslut till en Application Insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen, enligt följande diagram:

![Program-ID i Azure portal](./media/app-insights-automate-with-flow/appid.png) 

- Ange ett namn för anslutningen, tillsammans med den program-ID och API-nyckeln.

    ![Fönstret för Microsoft Flow-anslutning](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange hur Analytics-fråga och diagram
Den här exempelfråga väljer misslyckade förfrågningar under den senaste dagen och kopplar dem med undantag som inträffat som en del av åtgärden. Analytics korrelerar dem baserat på operation_Id identifierare. Frågan segment sedan resultatet med hjälp av algoritmen autocluster. 

När du skapar egna frågor kan du kontrollera att de fungerar korrekt i Analytics innan du lägger till den i ditt flöde.

- Lägg till följande Analytics-frågan och välj sedan diagramtypen HTML-tabell. 

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
    
    ![Konfigurationsfönstret i Analytics-fråga](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Steg 6: Konfigurera flödet för att skicka e-post

1. Klicka på **nytt steg**, och klicka sedan på **Lägg till en åtgärd**.
1. Sök efter **Office 365 Outlook**.
1. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.

    ![Office 365 Outlook-urvalsfönstret](./media/app-insights-automate-with-flow/flow2b.png)

1. I den **skicka ett e-postmeddelande** fönstret gör du följande:

   a. Ange e-postadressen till mottagaren.

   b. Ange ett ämne för e-postmeddelandet.

   c. Klicka på den **brödtext** rutan och sedan på dynamiskt innehåll-menyn som öppnas till höger väljer **brödtext**.

   d. Klicka på **visa avancerade alternativ**.

    ![Office 365 Outlook-konfiguration](./media/app-insights-automate-with-flow/flow5.png)

1. På menyn om dynamiskt innehåll, gör du följande:

    a. Välj **Bilagenamn**.

    b. Välj **innehåll i bifogad fil**.
    
    c. I den **är HTML** väljer **Ja**.

    ![Konfigurationsfönstret i Office 365 e-post](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Steg 7: Spara och testa ditt flöde
- I den **flödesnamn** , lägga till ett namn för flödet och sedan på **Skapa flöde**.

    ![Skapa flöde fönster](./media/app-insights-automate-with-flow/flow8.png)

Du kan vänta på att utlösaren ska köras den här åtgärden eller kan du köra flödet direkt av [köra utlösaren på begäran](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

När flödet körs postmeddelande de mottagare som du har angett i listan över e-ett e-som ser ut som följande:

![E-postexempel](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar [analysfrågor](../log-analytics/query-language/get-started-queries.md).
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





