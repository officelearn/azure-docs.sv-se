---
title: Automatisera Azure Application Insights-processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att automatisera snabbt repeterbara processer med hjälp av Application Insights-anslutningen.
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
ms.openlocfilehash: c92b50ee78c100a531a2973181251444b3810967
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293783"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatisera Azure Application Insights-processer med connector för Microsoft Flow

Hittar du själv körs flera gånger samma frågor på telemetridata att kontrollera att tjänsten fungerar korrekt? Letar du vill automatisera dessa frågor för att hitta trender och avvikelser och sedan skapa egna arbetsflöden runt dem? Anslutningsprogrammet Azure Application Insights (förhandsversion) för Microsoft Flow är rätt verktyg för dessa ändamål.

Med den här integreringen kan du automatisera många processer nu utan att skriva en enda rad kod. När du har skapat ett flöde med hjälp av en Application Insights-åtgärden körs flödet automatiskt Application Insights Analytics-fråga. 

Du kan lägga till ytterligare åtgärder samt. Microsoft Flow tillgängliggör hundratals åtgärder. Du kan till exempel använda Microsoft Flow för att automatiskt skicka ett e-postmeddelande eller skapa ett programfel i Visual Studio Team Services. Du kan också använda en av många [mallar](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) som är tillgängliga för anslutningen till Microsoft Flow. Dessa mallar snabbare att skapa ett flöde. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Skapa ett flöde för Application Insights

I kursen får du lära dig hur du skapar ett flöde som använder algoritmen Analytics auto-kluster till Gruppattribut i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Microsoft Flow och Application Insights Analytics tillsammans. 

### <a name="step-1-create-a-flow"></a>Steg 1: Skapa ett flöde
1. Logga in på [Microsoft Flow](http://flow.microsoft.com), och välj sedan **Mina flödar**.
2. Klicka på **skapa ett flöde från tomt**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: Skapa en utlösare för din flöde
1. Välj **schema**, och välj sedan **schema - upprepning**.
2. I den **frekvens** väljer **dag**, och i den **intervall** ange **1**.

    ![Dialogrutan för Microsoft Flow utlösare](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Klicka på **nytt steg**, och klicka sedan på **lägga till en åtgärd**.
2. Sök efter **Azure Application Insights**.
3. Klicka på **Azure Application Insights – visualisera Analytics fråga Preview**.

    ![Kör Analytics query-fönstret](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Anslut till en Application Insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen som visas i följande diagram:

![Program-ID i Azure-portalen](./media/app-insights-automate-with-flow/appid.png) 

- Ange ett namn för anslutningen, tillsammans med program-ID och API-nyckeln.

    ![Fönstret för Microsoft Flow-anslutning](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange Analytics query och diagram
Den här exempelfråga väljer misslyckade begäranden i den sista dagen och korrelerar dem med undantag uppstod under åtgärden. Analytics korrelerar dem baserat på operation_Id identifierare. Frågan segment sedan resultatet med hjälp av algoritmen autocluster. 

När du skapar egna frågor kan du kontrollera att de fungerar korrekt i Analytics innan du lägger till den din flödet.

- Lägg till följande Analytics-fråga och välj sedan diagramtypen HTML-tabellen. 

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
    
    ![Analytics frågefönstret konfiguration](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Steg 6: Konfigurera flödet för att skicka e-post

1. Klicka på **nytt steg**, och klicka sedan på **lägga till en åtgärd**.
2. Sök efter **Office 365 Outlook**.
3. Klicka på **Office 365 Outlook – skicka ett e-post**.

    ![Urvalsfönster för Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. I den **skickar ett e-** fönster, gör du följande:

   a. Skriv e-postadressen till mottagaren.

   b. Skriv ett ämne för e-postmeddelandet.

   c. Klicka någonstans i den **brödtext** rutan och markera sedan på dynamiskt innehåll menyn som öppnas till höger, **brödtext**.

   d. Klicka på **visa avancerade alternativ**.

    ![Outlook-konfiguration för Office 365](./media/app-insights-automate-with-flow/flow5.png)

5. På menyn om dynamiskt innehåll, gör du följande:

    a. Välj **Bilagenamn**.

    b. Välj **bifogade filer**.
    
    c. I den **är HTML** väljer **Ja**.

    ![Fönster för Office 365 e-konfiguration](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Steg 7: Spara och testa din flöde
- I den **flöde namnet** , lägga till ett namn för din flöde, och klicka sedan **skapa flödet**.

    ![Skapande av flödet fönster](./media/app-insights-automate-with-flow/flow8.png)

Utlösaren att köra den här åtgärden kan vänta eller köra flödet direkt av [kör utlösaren på begäran](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

När den körs, visas de mottagare som du har angett i listan över e-ett e-postmeddelande som ser ut som följande:

![E-postexempel](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du skapar [Analytics-frågor](app-insights-analytics-using.md).
- Lär dig mer om [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





