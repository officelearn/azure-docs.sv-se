---
title: Automatisera anpassade rapporter med Azure Application Insights-data
description: Automatisera anpassade rapporter för varje dag/per vecka/månad med Azure Application Insights-data
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c8cff54c67ab2c9c3d09f9261617b6312cc4434a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025955"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisera anpassade rapporter med Azure Application Insights-data

Periodiska rapporter skydda ett team dig informerad om hur de kritiska företagstjänster gör. Utvecklare, DevOps/SRE team och deras chefer kan vara produktiva med automatisk rapporter som levereras på ett tillförlitligt sätt insikter utan alla att logga in på portalen. Dessa rapporter kan också identifiera stegvis ökar i fördröjningar, load eller fel priser som inte kan utlösa någon varning regler.

Varje företaget har dess unika rapporteringskrav som: 

* Specifika percentil aggregeringar av mått eller anpassade mått i en rapport.
* Har olika rapporter för dagliga, veckovisa och månatliga översikter över data för olika målgrupper.
* Segmentering av anpassade attribut som region eller miljö. 
* Gruppera vissa AI-resurser i en enda rapport, även om de finnas i olika prenumerationer eller resurs grupper osv.
* Separata rapporter som innehåller känsliga mått som skickas till selektiv målgrupp.
* Rapporter för att intressenter som inte kanske har åtkomst till portalen resurser.

> [!NOTE] 
> Varje vecka Application Insights sammanfattad e-postmeddelandet tillät inte någon anpassning och kommer att upphöra för anpassade alternativ nedan. Senaste veckovisa sammanfattad e-postmeddelandet skickas på den 11 juni 2018. Konfigurera en av följande alternativ för att hämta liknande anpassade rapporter (Använd frågan förslag nedan).

## <a name="to-automate-custom-report-emails"></a>Automatisera anpassad rapport e-post

Du kan [programmässigt fråga Application Insights](https://dev.applicationinsights.io/) data att skapa anpassade rapporter på ett schema. Följande alternativ kan hjälpa dig att komma igång snabbt:

* [Automatisera rapporter med Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatisera rapporter med Logic Apps](automate-with-logic-apps.md)
* Använd ”Application Insights schemalagda digest” [Azure funktionen](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) mall i scenariot för övervakning. Den här funktionen använder SendGrid för att leverera e-postmeddelandet. 

    ![Mall för Azure-funktion](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exempelfråga för en vecka sammanfattad e-post
Följande fråga visar anslutning över flera datamängder för en vecka sammanfattad e-postadress som rapporten. Anpassa den efter behov och använda den med något av alternativen som anges ovan för att automatisera en vecka rapport.   

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

## <a name="application-insights-scheduled-digest-report"></a>Application Insights schemalagda sammanfattad rapport

1. Välj den Azure-portalen **skapar du en resurs** > **Compute** > **Funktionsapp**.

   ![Skapa en Azure Resource Funktionsapp skärmbild](./media/automate-custom-reports/function-app-01.png)

2. Ange information som är lämpliga för din app och välj _skapa_. (Application Insights _på_ krävs bara om du vill övervaka din nya Funktionsapp med Application Insights)

   ![Skapa en App-inställningar för Azure-funktionen skärmbild](./media/automate-custom-reports/function-app-02.png)

3. När din nya Funktionsapp har slutfört distributionen, välja **finns resurs**.

4. Välj **nya funktionen**.

   ![Skapa en ny funktion skärmbild](./media/automate-custom-reports/function-app-03.png)

5. Välj den  **_Application Insights schemalagda sammanfattad mallen_**.

   ![Den nya funktionen Application Insights mall skärmbild](./media/automate-custom-reports/function-app-04.png)

6. Ange en lämplig mottagarnas e-postadress för rapporten och välj **skapa**.

   ![Funktionen inställningar skärmbild](./media/automate-custom-reports/function-app-05.png)

7. Välj din **Funktionsapp** > **plattformsfunktioner** > **programinställningar**.

    ![Skärmbild av Azure funktionen programmet-inställningar](./media/automate-custom-reports/function-app-07.png)

8. Skapa tre nya programinställningar med rätt motsvarande värden ``AI_APP_ID``, ``AI_APP_KEY``, och ``SendGridAPI``. Välj **Spara**.

     ![Funktionen integration gränssnittet skärmbild](./media/automate-custom-reports/function-app-08.png)
    
    (Värdena AI_ finns under API-åtkomst för Application Insights-resurs som du vill rapportera om. Om du inte har en Application Insights API-nyckel, finns alternativet att **skapa API-nyckel**.)
    
    * AI_APP_ID = program-ID
    * AI_APP_KEY = API-nyckel
    * SendGridAPI = SendGrid API-nyckel

    > [!NOTE]
    > Om du inte har ett SendGrid-konto som du kan skapa en. Sendgrids dokumentation för Azure Functions [här](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-sendgrid). Om bara vill att en minimal förklaring av hur man installerar SendGrid och skapa en API-nyckel som medföljer i slutet av den här artikeln. 

9. Välj **integrera** och klicka på utdata under **SendGrid ($return)**.

     ![Skärmbild av utdata](./media/automate-custom-reports/function-app-09.png)

10. Under den **Appinställning med nyckeln SendGridAPI**, Välj din nya App-inställning för **SendGridAPI**.

     ![Kör Funktionsapp skärmbild](./media/automate-custom-reports/function-app-010.png)

11. Köra och testa din App för funktionen.

     ![Testa skärmbild](./media/automate-custom-reports/function-app-11.png)

12. Kontrollera din e-post för att bekräfta att meddelandet har skickas/tas emot.

     ![E-ämne rad skärmbild](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid med Azure

De här stegen gäller endast om du inte redan har ett SendGrid-konto som har konfigurerats.

1. Azure portal väljer **skapar du en resurs** söka efter **SendGrid e-postleverans** > klickar du på **skapa** > och Fyll ut SendGrid specifika skapa instruktioner. 

     ![Skapa SendGrid resurs skärmbild](./media/automate-custom-reports/function-app-13.png)

2. När du skapade under SendGrid konton välja **hantera**.

     ![Skärmbild av inställningar för API-nyckel](./media/automate-custom-reports/function-app-14.png)

3. Detta startar Sendgrids plats. Välj **inställningar** > **API-nycklar**.

     ![Skapa och visa API-nyckel App skärmbild](./media/automate-custom-reports/function-app-15.png)

4. Skapa en API-nyckel > Välj **skapa vy** (granska Sendgrids dokumentation om begränsad åtkomst för att avgöra vilken nivå av behörigheter som är lämpliga för din API-nyckel. Fullständig åtkomst väljs här endast för exempel.)

   ![Skärmbild för fullständig åtkomst](./media/automate-custom-reports/function-app-16.png)

5. Kopiera hela nyckeln, det här värdet är vad du behöver i inställningarna för Funktionsapp som värde för SendGridAPI

   ![Kopiera API-nyckel skärmbild](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du skapar [Analytics-frågor](app-insights-analytics-using.md).
* Lär dig mer om [programmässigt datafrågor Application Insights](https://dev.applicationinsights.io/)
* Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Lär dig mer om [Microsoft Flow](https://ms.flow.microsoft.com).
