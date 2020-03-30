---
title: Ange aviseringar i Azure Application Insights
description: Få meddelanden om långsamma svarstider, undantag och andra prestanda- eller användningsändringar i webbappen.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295084"
---
# <a name="set-alerts-in-application-insights"></a>Ange aviseringar i programinsikter

[Azure Application Insights][start] kan varna dig om ändringar i prestanda- eller användningsmått i din webbapp. 

Application Insights övervakar din live-app på en [mängd olika plattformar][platforms] som hjälper dig att diagnostisera prestandaproblem och förstå användningsmönster.

Det finns flera typer av aviseringar:

* [**Måttaviseringar**](../../azure-monitor/platform/alerts-metric-overview.md) talar om för dig när ett mått korsar ett tröskelvärde för en viss period , till exempel svarstider, undantagsantal, CPU-användning eller sidvisningar.
* [**Loggaviseringar**](../../azure-monitor/platform/alerts-unified-log.md) används för att beskriva aviseringar där varningssignalen baseras på en anpassad Kusto-fråga.
* [**Webbtester**][availability] talar om för dig när webbplatsen inte är tillgänglig på internet eller svarar långsamt. [Läs mer][availability].
* [**Proaktiv diagnostik**](../../azure-monitor/app/proactive-diagnostics.md) konfigureras automatiskt för att meddela dig om ovanliga prestandamönster.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Så här anger du en undantagsvarning med anpassad loggsökning

I det här avsnittet går vi igenom hur du anger en frågebaserad undantagsavisering. Låt oss i det här exemplet säga att vi vill ha en avisering när den misslyckade hastigheten är större än 10 % under de senaste 24 timmarna.

1. Gå till din Application Insight-resurs i Azure-portalen.
2. Till vänster, under konfigurera klicka på **Alert**.

    ![Till vänster under konfigurera klickvarning](./media/alerts/1appinsightalert.png)

3. Högst upp på varningsfliken väljer du **Ny varningsregel**.

     ![Högst upp på varningsfliken klickar du på den nya varningsregeln](./media/alerts/2createalert.png)

4. Din resurs bör väljas automatiskt. Om du vill ange ett villkor klickar du på **Lägg till villkor**.

    ![Klicka på Lägg till villkor](./media/alerts/3addcondition.png)

5. På fliken Konfigurera signallogik väljer du **Anpassad loggsökning**

    ![Klicka på anpassad loggsökning](./media/alerts/4customlogsearch.png)

6. På fliken anpassad loggsökning anger du frågan i rutan Sökfråga. I det här exemplet använder vi nedanstående Kusto-fråga.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Skriv fråga i sökfrågerutan](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Du kan också använda dessa steg på andra typer av frågebaserade aviseringar. Du kan läsa mer om Kusto frågespråk från denna [Kusto komma igång doc](https://docs.microsoft.com/azure/kusto/concepts/) eller denna [SQL till Kusto lathund](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Under "Varningslogik" väljer du om den baseras på antal resultat eller måttmätning. Välj sedan villkoret (större än, lika med, mindre än) och ett tröskelvärde. När du ändrar dessa värden kan du märka att meningen ändras i förhandsgranskningsförhandsversionen. I det här exemplet använder vi "lika med".

    ![Under Varningslogik väljer du bland de alternativ som anges för baserat på och villkor, skriv sedan ett tröskelvärde](./media/alerts/6alertlogic.png)

8. Under "Utvärderad baserat på", ange period och frekvens. Perioden här måste matcha det värde som vi sätter för period i frågan ovan. **Klicka**sedan gjort .

    ![Ställ in period och frekvens längst ned och klicka sedan på klar](./media/alerts/7evaluate.png)

9. Vi ser nu det tillstånd vi skapade med den beräknade månadskostnaden. Nedan under ["Åtgärdsgrupper"](../platform/action-groups.md) kan du skapa en ny grupp eller välja en befintlig. Om du vill kan du anpassa åtgärderna.

    ![klicka på markerings- eller skapa-knapparna under åtgärdsgrupp](./media/alerts/8actiongroup.png)

10. Lägg slutligen till dina varningsuppgifter (varningsregelnamn, beskrivning, allvarlighetsgrad). När du är klar klickar du på **Skapa aviseringsregel** längst ned.

    ![Skriv en beskrivning under varningsdetaljtyp och välj allvarlighetsgrad och välj allvarlighetsgrad](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Så här avslutar du prenumerationen på klassiska e-postmeddelanden för aviseringar

Det här avsnittet gäller **klassiska tillgänglighetsaviseringar,** **klassiska mätaviseringar för Application Insights**och **klassiska felavvikelser**.

Du får e-postmeddelanden för dessa klassiska aviseringar om något av följande gäller:

* Din e-postadress visas i fältet E-postmottagare för meddelanden i inställningarna för varningsregeln.

* Alternativet att skicka e-postmeddelanden till användare som har vissa roller för prenumerationen aktiveras och du har en respektive roll för just den Azure-prenumerationen.

![Skärmdump av varningsmeddelande](./media/alerts/alert-notification.png)

För att bättre kontrollera din säkerhet och integritet rekommenderar vi i allmänhet att du uttryckligen anger meddelandemottagarna för dina klassiska aviseringar i fältet **E-postmottagare** för meddelanden. Alternativet att meddela alla användare som har vissa roller tillhandahålls för bakåtkompatibilitet.

Om du vill avsluta prenumerationen på e-postmeddelanden som genererats av en viss varningsregel tar du bort din e-postadress från fältet **E-postmottagare** för meddelanden.

Om din e-postadress inte uttryckligen anges rekommenderar vi att du inaktiverar alternativet att meddela alla medlemmar om vissa roller automatiskt och i stället listar alla användarmeddelanden som behöver ta emot meddelanden för den varningsregeln i e-postmeddelandets e-postmeddelande mottagarfältet.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem får (klassiska) varningsmeddelanden?

Det här avsnittet gäller endast klassiska aviseringar och hjälper dig att optimera dina aviseringar för att säkerställa att endast önskade mottagare får aviseringar. Mer information om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md) och den nya aviseringarupplevelsen finns i [översiktsartikeln för aviseringar](../platform/alerts-overview.md). Om du vill styra aviseringar i den nya aviseringarupplevelsen använder du [åtgärdsgrupper](../platform/action-groups.md).

* Vi rekommenderar att du använder specifika mottagare för klassiska varningsmeddelanden.

* För aviseringar om alla mätvärden för Application Insights (inklusive tillgänglighetsmått) skickar kryssrutan **bulk/grupp,** om det är aktiverat, till användare med ägar-, deltagar- eller läsarroller i prenumerationen. I själva verket är _alla_ användare med åtkomst till prenumerationen Application Insights resurs i omfattning och kommer att få meddelanden.

> [!NOTE]
> Om du för närvarande använder kryssrutan **bulk/grupp** och inaktiverar det kan du inte återställa ändringen.

Använd den nya aviseringsupplevelsen/aviseringar i nära realtid om du behöver meddela användarna baserat på deras roller. Med [åtgärdsgrupper](../platform/action-groups.md)kan du konfigurera e-postmeddelanden till användare med någon av rollerna deltagare/ägare/läsare (inte kombineras tillsammans som ett enda alternativ).

## <a name="automation"></a>Automation
* [Använda PowerShell för att automatisera ställa in aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Använd webhooks för att automatisera svara på aviseringar](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Se även
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatisera ställa in aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Proaktiv diagnostik](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

