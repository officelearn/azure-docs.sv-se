---
title: Automatisera anpassade rapporter med Azure Application Insights-data
description: Automatisera anpassade dagliga/veckovisa/månatliga rapporter med Azure Application Insights-data
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655131"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisera anpassade rapporter med Azure Application Insights-data

Periodiska rapporter hjälper till att hålla ett team informerat om hur det går för deras affärskritiska tjänster. Utvecklare, DevOps/SRE-team och deras chefer kan vara produktiva med automatiserade rapporter som på ett tillförlitligt sätt ger insikter utan att alla behöver logga in på portalen. Sådana rapporter kan också hjälpa till att identifiera gradvisa ökningar av svarstider, belastnings- eller felfrekvenser som kanske inte utlöser några varningsregler.

Varje företag har sina unika rapporteringsbehov, till exempel: 

* Specifika percentilaggregeringar av mått eller anpassade mått i en rapport.
* Ha olika rapporter för dagliga, veckovisa och månatliga sammanslagningar av data för olika målgrupper.
* Segmentering efter anpassade attribut som region eller miljö. 
* Gruppera vissa AI-resurser tillsammans i en enda rapport, även om de kan finnas i olika prenumerationer eller resursgrupper etc.
* Separata rapporter som innehåller känsliga mått som skickas till selektiv målgrupp.
* Rapporter till intressenter som kanske inte har åtkomst till portalresurserna.

> [!NOTE] 
> Den veckovisa Application Insights sammandrag e-post tillät inte någon anpassning, och kommer att avbrytas till förmån för de anpassade alternativen nedan. Det sista veckosammanfattningsmeddelandet skickas den 11 juni 2018. Konfigurera ett av följande alternativ för att hämta liknande anpassade rapporter (använd frågan som föreslås nedan).

## <a name="to-automate-custom-report-emails"></a>Så här automatiserar du e-postmeddelanden med anpassade rapporter

Du kan [programmässigt fråga Application Insights-data](https://dev.applicationinsights.io/) för att generera anpassade rapporter enligt ett schema. Följande alternativ kan hjälpa dig att komma igång snabbt:

* [Automatisera rapporter med Microsoft Flow](automate-with-flow.md)
* [Automatisera rapporter med Logic Apps](automate-with-logic-apps.md)
* Använd [azure-funktionsmallen](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application Insights scheduled digest" i övervakningsscenariot. Den här funktionen använder SendGrid för att leverera e-postmeddelandet. 

    ![Azure-funktionsmall](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exempelfråga för ett sammanfattat veckomeddelande
Följande fråga visar att det finns en koppling över flera datauppsättningar för en veckovis sammanfattad e-post som rapport. Anpassa den efter behov och använd den med något av alternativen ovan för att automatisera en veckorapport.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Schemalagd sammanfattad rapport för application insights

1. I Azure-portalen väljer du **Skapa en resource** > **compute** > **function app**.

   ![Skapa en skärmdump av Azure Resource Function App](./media/automate-custom-reports/function-app-01.png)

2. Ange lämplig information för din app och välj _Skapa_. (Application Insights _On_ krävs bara om du vill övervaka din nya funktionsapp med Application Insights)

   ![Skapa en skärmbild av appinställningar för Azure Resource Function](./media/automate-custom-reports/function-app-02.png)

3. När din nya funktionsapp har slutfört distributionen väljer du **Gå till resurs**.

4. Välj **Ny funktion**.

   ![Skapa en ny funktionsskärm](./media/automate-custom-reports/function-app-03.png)

5. Välj **_den schemalagda sammanfattningsmallen Application Insights_**.

     > [!NOTE]
     > Som standard skapas funktionsappar med körningsversion 2.x. Du måste [inrikta dig på Azure Functions runtime version](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1.x** för att kunna använda den schemalagda sammanfattningsmallen Application Insights.  ![skärmbild av runtime](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Skärmbild av mallen För ny funktionsapplikationsstatistik](./media/automate-custom-reports/function-app-04.png)

6. Ange en lämplig mottagares e-postadress för rapporten och välj **Skapa**.

   ![Skärmbild av funktionsinställningar](./media/automate-custom-reports/function-app-05.png)

7. Välj dina **funktionsappplattformsfunktioner** > **Platform features** > **Programinställningar**.

    ![Skärmbild av Azure-funktionsprogram](./media/automate-custom-reports/function-app-07.png)

8. Skapa tre nya programinställningar ``AI_APP_ID``med ``AI_APP_KEY``lämpliga ``SendGridAPI``motsvarande värden , och . Välj **Spara**.

     ![Skärmbild av funktionsintegreringsgränssnitt](./media/automate-custom-reports/function-app-08.png)
    
    (De AI_ värdena finns under API Access för den Application Insights-resurs som du vill rapportera om. Om du inte har en API-nyckel för programinsikter finns det möjlighet att **skapa API-nyckel**.)
    
   * AI_APP_ID = Program-ID
   * AI_APP_KEY = API-nyckel
   * SendGridAPI =SendGrid API-nyckel

     > [!NOTE]
     > Om du inte har ett SendGrid-konto kan du skapa ett. SendGrids dokumentation för Azure Functions finns [här](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Om bara vill ha en minimal förklaring av hur du ställer in SendGrid och generera en API-nyckel en finns i slutet av den här artikeln. 

9. Välj **Integrera** och klicka på **SendGrid ($return)** under Utdata .

     ![Skärmdump av utdata](./media/automate-custom-reports/function-app-09.png)

10. Under **inställningen SendGridAPI Key App**väljer du den nyskapade appinställningen för **SendGridAPI**.

     ![Kör skärmbild av funktionsapp](./media/automate-custom-reports/function-app-010.png)

11. Kör och testa din funktionsapp.

     ![Testa skärmbild](./media/automate-custom-reports/function-app-11.png)

12. Kontrollera din e-post för att bekräfta att meddelandet har skickats/tagits emot.

     ![Skärmdump av ämnesraden för e-post](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid med Azure

De här stegen gäller bara om du inte redan har ett SendGrid-konto konfigurerat.

1. Välj **Skapa en resurssökning** för **SendGrid-e-postleverans** > Klicka på **Skapa** > i Azure-portalen och fyll i SendGrid-specifika skapa instruktioner. 

     ![Skapa Skärmbild av SendGrid-resurser](./media/automate-custom-reports/function-app-13.png)

2. När du har skapats under SendGrid-konton väljer du **Hantera**.

     ![Skärmbild av API-nyckel för inställningar](./media/automate-custom-reports/function-app-14.png)

3. Detta kommer att lansera SendGrid webbplats. Välj**API-nycklar för** **inställningar** > .

     ![Skärmbild av API-nyckelprogram](./media/automate-custom-reports/function-app-15.png)

4. Skapa en API-nyckel > välja **Skapa & vy** (Läs SendGrids dokumentation om begränsad åtkomst för att avgöra vilken behörighetsnivå som är lämplig för din API-nyckel. Fullständig åtkomst väljs här endast i exempelvis förändamål.)

   ![Skärmbild av fullständig åtkomst](./media/automate-custom-reports/function-app-16.png)

5. Kopiera hela nyckeln, är detta värde vad du behöver i din funktion App inställningar som värde för SendGridAPI

   ![Kopiera API-nyckel skärmdump](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du skapar [Analytics-frågor](../../azure-monitor/log-query/get-started-queries.md).
* Läs mer om [programmatiskt fråga Application Insights-data](https://dev.applicationinsights.io/)
* Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).
