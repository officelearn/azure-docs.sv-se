---
title: Automatisera anpassade rapporter med Azure Application Insights-data
description: Automatisera anpassade rapporter för varje dag/vecka/månad med Azure Application Insights-data
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 74fdfed23d30dbf88b2faa6028039159010d9afc
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53756806"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisera anpassade rapporter med Azure Application Insights-data

Periodiska rapporter att hålla ett team informerad om hur deras företag kritiska tjänster gör. Utvecklare, DevOps/SRE team och deras chefer kan vara produktiva med automatiserade rapporter på ett tillförlitligt leverera insikter utan att alla ska logga in på portalen. Rapporterna kan också identifiera gradvis ökar i svarstider, priserna för belastning eller fel som inte kan utlösa någon aviseringsregler.

Alla företag har sin unika rapporteringskrav, till exempel: 

* Specifika: e percentilen sammanställning av mått eller anpassade mått i en rapport.
* Ha olika rapporter för dagliga, veckovisa och månatliga översikter över data för olika målgrupper.
* Segmentering av anpassade attribut som region eller miljö. 
* Gruppera vissa AI-resurser i en enda rapport, även om de kan vara i olika prenumerationer eller resurs grupper osv.
* Separata rapporter som innehåller känsliga mått som skickas till selektiv publik.
* Rapporter för att intressenter som inte kanske har åtkomst till portalen resurser.

> [!NOTE] 
> Varje vecka Application Insights sammanfattad e-postmeddelandet godkändes inte av någon anpassning och kommer att upphöra och ersatts med anpassade alternativ nedan. Senaste veckovisa sammanfattad e-postmeddelandet skickas den 11 juni 2018. Konfigurera en av följande alternativ för att hämta liknande anpassade rapporter (Använd frågan föreslås nedan).

## <a name="to-automate-custom-report-emails"></a>Automatisera e-postmeddelanden för anpassad rapport

Du kan [också programmera en fråga Application Insights](https://dev.applicationinsights.io/) data för att skapa anpassade rapporter på ett schema. Med hjälp av följande alternativ kan du snabbt komma igång:

* [Automatisera rapporter med Microsoft Flow](automate-with-flow.md)
* [Automatisera rapporter med Logic Apps](automate-with-logic-apps.md)
* Använd ”Application Insights schemalagda digest” [Azure-funktion](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) mall i övervakning-scenario. Den här funktionen använder SendGrid för att leverera e-postmeddelandet. 

    ![Mall för Azure-funktion](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exempelfråga för en veckovis sammanfattad e-post
Följande fråga visar koppla över flera datauppsättningar för en veckovis sammanfattad e-postmeddelande som rapporten. Anpassa den efter behov och använder den med någon av de alternativ som beskrivs ovan för att automatisera en veckorapport.   

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

1. Azure-portalen väljer du **skapa en resurs** > **Compute** > **Funktionsapp**.

   ![Skapa en Funktionsapp i Azure Resource-skärmbild](./media/automate-custom-reports/function-app-01.png)

2. Ange information som är lämpliga för din app och välj _skapa_. (Application Insights _på_ krävs bara om du vill övervaka din nya Funktionsapp med Application Insights)

   ![Skapa en Azure Resource Funktionsappinställningar skärmbild](./media/automate-custom-reports/function-app-02.png)

3. När din nya Funktionsapp har slutfört distributionen, väljer **gå till resurs**.

4. Välj **ny funktion**.

   ![Skapa en ny funktion-skärmbild](./media/automate-custom-reports/function-app-03.png)

5. Välj den  **_Application Insights schemalagda sammanfattad mallen_**.

   ![Ny Funktionsmallen Application Insights-skärmbild](./media/automate-custom-reports/function-app-04.png)

6. Ange en lämplig mottagarens e-postadress för rapporten och välj **skapa**.

   ![Funktionen inställningar skärmbild](./media/automate-custom-reports/function-app-05.png)

7. Välj din **Funktionsapp** > **plattformsfunktioner** > **programinställningar**.

    ![Skärmbild av Azure-Funktionsprogram-inställningar](./media/automate-custom-reports/function-app-07.png)

8. Skapa tre nya programinställningar med lämpliga värden för motsvarande ``AI_APP_ID``, ``AI_APP_KEY``, och ``SendGridAPI``. Välj **Spara**.

     ![Funktionen integration gränssnittet skärmbild](./media/automate-custom-reports/function-app-08.png)
    
    (AI_-värden kan hittas under API-åtkomst för Application Insights-resurs som du vill rapportera om. Om du inte har en Application Insights API-nyckel, finns alternativet att **skapa API-nyckel**.)
    
    * AI_APP_ID = program-ID
    * AI_APP_KEY = API-nyckel
    * SendGridAPI = SendGrid API-nyckel

    > [!NOTE]
    > Om du inte har ett SendGrid-konto kan du skapa en. SendGrid-dokumentation för Azure Functions är [här](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Om bara vill att en minimal förklaring av hur du konfigurerar SendGrid och generera en API-nyckel som sådan anges i slutet av den här artikeln. 

9. Välj **integrera** och klicka på utdata under **SendGrid ($return)**.

     ![Skärmbild av utdata](./media/automate-custom-reports/function-app-09.png)

10. Under den **SendGridAPI nyckel för Appinställning**, Välj din nyligen skapade App-inställning för **SendGridAPI**.

     ![Kör Funktionsappen skärmbild](./media/automate-custom-reports/function-app-010.png)

11. Kör och testa din Funktionsapp.

     ![Testa skärmbild](./media/automate-custom-reports/function-app-11.png)

12. Kontrollera din e-post för att bekräfta att meddelandet har skickats/tagits emot.

     ![E-ämne rad skärmbild](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid med Azure

De här stegen gäller endast om du inte redan har ett SendGrid-konto som har konfigurerats.

1. Azure portal väljer **skapa en resurs** söka efter **SendGrid e-postleverans** > klickar du på **skapa** > och Fyll i den SendGrid-specifikt skapa instruktioner. 

     ![Skapa resurs för SendGrid-skärmbild](./media/automate-custom-reports/function-app-13.png)

2. När du skapade under SendGrid-konton väljer **hantera**.

     ![Skärmbild av inställningar för API-nyckel](./media/automate-custom-reports/function-app-14.png)

3. SendGrid-webbplatsen startas. Välj **inställningar** > **API-nycklar**.

     ![Skapa och visa API-nyckel App skärmbild](./media/automate-custom-reports/function-app-15.png)

4. Skapa en API-nyckel > Välj **skapa vy** (finns i SendGrid-dokumentationen på begränsad åtkomst för att avgöra vilken nivå av behörigheter som är lämpliga för din API-nyckel. Fullständig åtkomst väljs här endast exempelsyfte.)

   ![Skärmbild för fullständig åtkomst](./media/automate-custom-reports/function-app-16.png)

5. Kopiera hela nyckeln, det här värdet är vad du behöver i inställningarna för din Funktionsapp som värde för SendGridAPI

   ![Kopiera API key skärmbild](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du skapar [analysfrågor](../../azure-monitor/log-query/get-started-queries.md).
* Läs mer om [programmässigt hämtar data från Application Insights](https://dev.applicationinsights.io/)
* Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).
