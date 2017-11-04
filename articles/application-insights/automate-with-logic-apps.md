---
title: "Automatisera Azure Application Insights processer med hjälp av Logic Apps."
description: "Lär dig hur du snabbt kan automatisera repeterbara processer genom att lägga till Application Insights connector din logikapp."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: 92521bab2082f63fa01e816151e3da9400effe6d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatisera processer för Application Insights med hjälp av Logic Apps

Hittar du själv körs flera gånger samma frågor på telemetridata att kontrollera om din tjänst fungerar korrekt? Letar du vill automatisera dessa frågor för att hitta trender och avvikelser och sedan skapa egna arbetsflöden runt dem? Anslutningsprogrammet Azure Application Insights (förhandsversion) för Logic Apps är rätt verktyg för detta ändamål.

Med den här integreringen kan du automatisera många processer utan att skriva en enda rad kod. Du kan skapa en logikapp med Application Insights connector snabbt automatisera processer Application Insights. 

Du kan lägga till ytterligare åtgärder samt. Funktionen Logic Apps i Azure Apptjänst tillgängliggör hundratals åtgärder. Till exempel med hjälp av en logikapp, kan du automatiskt skicka ett e-postmeddelande eller skapa ett programfel i Visual Studio Team Services. Du kan också använda en av många tillgängliga [mallar](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) för att påskynda processen att skapa din logikapp. 

## <a name="create-a-logic-app-for-application-insights"></a>Skapa en logikapp för Application Insights

I kursen får du lära dig hur du skapar en logikapp som använder algoritmen Analytics autocluster grupp attribut i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Application Insights Analytics och Logic Apps tillsammans. 

### <a name="step-1-create-a-logic-app"></a>Steg 1: Skapa en logikapp
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den **ny** väljer **webb + mobilt**, och välj sedan **Logikapp**.

    ![Nytt logik app fönster](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Steg 2: Skapa en utlösare för din logikapp
1. I den **logik App Designer** fönstret under **börja med en gemensam utlösare**väljer **återkommande**.

    ![Logik App Designer fönster](./media/automate-with-logic-apps/logicapp2.png)

2. I den **frekvens** väljer **dag** och sedan, i den **intervall** skriver **1**.

    ![Logik App Designer ”återkommande” fönster](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Klicka på **nytt steg**, och klicka sedan på **lägga till en åtgärd**.

2. I den **välja en åtgärd** sökrutan, Skriv **Azure Application Insights**.

3. Under **åtgärder**, klickar du på **Azure Application Insights – visualisera Analytics fråga Preview**.

    ![Logik App Designer ”Välj åtgärden” fönster](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Anslut till en Application Insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen som visas i följande diagram:

![Program-ID i Azure-portalen](./media/automate-with-logic-apps/appid.png) 

Ange ett namn för din anslutning, program-ID och API-nyckeln.

![Fönstret för logik App Designer flödet anslutning](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange Analytics query och diagram
I följande exempel frågan väljer misslyckade begäranden i den sista dagen och korrelerar dem med undantag uppstod under åtgärden. Analytics korrelerar misslyckade begäranden, baserat på operation_Id identifierare. Frågan segment sedan resultatet med hjälp av algoritmen autocluster. 

När du skapar egna frågor kan du kontrollera att de fungerar korrekt i Analytics innan du lägger till den din flödet.

1. I den **frågan** Lägg till följande Analytics-fråga: 

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

2. I den **diagramtyp** väljer **HTML-tabell**.

    ![Analytics frågefönstret konfiguration](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Steg 6: Konfigurera logikappen så att skicka e-post

1. Klicka på **nytt steg**, och välj sedan **lägga till en åtgärd**.

2. I sökrutan skriver **Office 365 Outlook**.

3. Klicka på **Office 365 Outlook – skicka ett e-post**.

    ![Val av Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. I den **skickar ett e-** fönster, gör du följande:

   a. Skriv e-postadressen till mottagaren.

   b. Skriv ett ämne för e-postmeddelandet.

   c. Klicka någonstans i den **brödtext** rutan och markera sedan på dynamiskt innehåll menyn som öppnas till höger, **brödtext**.

   d. Klicka på **visa avancerade alternativ**.

      ![Outlook-konfiguration för Office 365](./media/automate-with-logic-apps/flow5.png)

5. På menyn om dynamiskt innehåll, gör du följande:

    a. Välj **Bilagenamn**.

    b. Välj **bifogade filer**.
    
    c. I den **är HTML** väljer **Ja**.

      ![Skärm för konfiguration av Office 365 e-post](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Steg 7: Spara och testa din logikapp
* Klicka på **spara** att spara ändringarna.

Du kan vänta utlösaren till kör logikappen eller du kan köra logikappen omedelbart genom att välja **kör**.

![Logik app skapa skärmen](./media/automate-with-logic-apps/step7.png)

När logikappen körs får mottagare som du angav i e-postlistan ett e-postmeddelande som ser ut som följande:

![Logik appen e-postmeddelande](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du skapar [Analytics-frågor](app-insights-analytics-using.md).
- Lär dig mer om [Logikappar](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





