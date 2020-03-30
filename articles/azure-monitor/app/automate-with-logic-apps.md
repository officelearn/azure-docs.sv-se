---
title: Automatisera Azure Application Insights-processer med hjälp av Logic Apps
description: Lär dig hur du snabbt kan automatisera repeterbara processer genom att lägga till Application Insights-kopplingen i logikappen.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473174"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatisera processer för programinsikter med hjälp av Logic Apps

Hittar du dig själv upprepade gånger kör samma frågor på din telemetri data för att kontrollera om din tjänst fungerar korrekt? Funderar du på att automatisera dessa frågor för att hitta trender och avvikelser och sedan bygga dina egna arbetsflöden runt dem? Azure Application Insights-kopplingen för Logic Apps är rätt verktyg för detta ändamål.

> [!NOTE]
> Azure Application Insights-kopplingen har ersatts med [Azure Monitor-anslutningen](../platform/logicapp-flow-connector.md) som är integrerad med Azure Active Directory i stället för att kräva en API-nyckel och låter dig även hämta data från en Log Analytics-arbetsyta.

Med den här integrationen kan du automatisera många processer utan att skriva en enda kodrad. Du kan skapa en logikapp med Application Insights-kopplingen för att snabbt automatisera alla Application Insights-processer. 

Du kan också lägga till ytterligare åtgärder. Logic Apps-funktionen i Azure App Service gör hundratals åtgärder tillgängliga. Genom att till exempel använda en logikapp kan du automatiskt skicka ett e-postmeddelande eller skapa ett fel i Azure DevOps. Du kan också använda en av de många tillgängliga [mallarna](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) för att påskynda processen med att skapa logikappen. 

## <a name="create-a-logic-app-for-application-insights"></a>Skapa en logikapp för Application Insights

I den här självstudien får du lära dig hur du skapar en logikapp som använder analytics autoklusteralgoritmen för att gruppera attribut i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Application Insights Analytics och Logic Apps tillsammans. 

### <a name="step-1-create-a-logic-app"></a>Steg 1: Skapa en logikapp
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Skapa en resurs,** välj **Webb + Mobil**och välj sedan Logic **App**.

    ![Nytt logikappfönster](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Steg 2: Skapa en utlösare för logikappen
1. Välj **Återkommande**under Start **med en vanlig utlösare**i fönstret **Logic App Designer** .

    ![Fönstret Logic App Designer](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Skriv **1** i rutan **Intervall** **Frequency** och välj sedan **Dag**.

    ![Fönstret "Återkommande" i Logic App Designer](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägga till en åtgärd för programinsikter
1. Klicka på **Nytt steg**.

1. Skriv **Azure Application Insights**i sökrutan **Välj en åtgärd** .

1. Klicka på **Azure Application Insights - Visualisera Analytics-fråga**under **Åtgärder**.

    ![Fönstret "Välj en åtgärd" i logic app designer](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Ansluta till en application insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen, som visas i följande diagram:

![Program-ID i Azure-portalen](./media/automate-with-logic-apps/5apiaccess.png)

![Program-ID i Azure-portalen](./media/automate-with-logic-apps/6apikey.png)

Ange ett namn för anslutningen, program-ID:n och API-nyckeln.

![Anslutningsfönster för Logic App Designer-flöde](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange Analytics-fråga och diagramtyp
I följande exempel väljer frågan de misslyckade begärandena under den sista dagen och korrelerar dem med undantag som inträffade som en del av åtgärden. Analytics korrelerar de misslyckade begärandena, baserat på operation_Id-identifieraren. Frågan segmenterar sedan resultaten med hjälp av autocluster-algoritmen. 

När du skapar egna frågor kontrollerar du att de fungerar som de ska i Analytics innan du lägger till det i flödet.

1. Lägg till följande Analytics-fråga i rutan **Fråga:**

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

1. Välj **Html-tabell**i rutan **Diagramtyp** .

    ![Konfigurationsfönster för Analytics-frågor](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Steg 6: Konfigurera logikappen så att den skickar e-post

1. Klicka på **Nytt steg**.

1. Skriv **Office 365 Outlook**i sökrutan .

1. Klicka på **Office 365 Outlook - Skicka ett e-postmeddelande**.

    ![Val av Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. Gör följande i fönstret **Skicka ett e-postmeddelande:**

   a. Skriv mottagarens e-postadress.

   b. Skriv ett ämne för e-postmeddelandet.

   c. Klicka var som helst i rutan **Brödtext** och välj sedan **Brödtext**på menyn Dynamiskt innehåll som öppnas till höger .
    
   d. Klicka på listrutan **Lägg till ny parameter** och välj Bifogade filer och HTML.

      ![Konfiguration av Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfiguration av Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. Gör följande på menyn dynamiskt innehåll:

    a. Välj **Namn på bifogad fil**.

    b. Välj **Innehåll för bifogade filer**.
    
    c. Välj **Ja**i rutan **Är HTML** .

      ![Konfigurationsskärmen för e-post i Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Steg 7: Spara och testa logikappen
* Klicka på **Spara** om du vill spara ändringarna.

Du kan vänta på att utlösaren ska köra logikappen eller köra logikappen direkt genom att välja **Kör**.

![Skärmen För att skapa logikappar](./media/automate-with-logic-apps/13save.png)

När logikappen körs får mottagarna som du angav i e-postlistan ett e-postmeddelande som ser ut så här:

![E-postmeddelande om logikapp](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar [Analytics-frågor](../../azure-monitor/log-query/get-started-queries.md).
- Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





