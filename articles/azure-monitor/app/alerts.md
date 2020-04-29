---
title: Ange aviseringar i Azure Application insikter
description: Få meddelanden om långsamma svars tider, undantag och andra prestanda-eller användnings ändringar i din webbapp.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295084"
---
# <a name="set-alerts-in-application-insights"></a>Ange aviseringar i Application Insights

[Azure Application insikter][start] kan varna dig om förändringar i prestanda-eller användnings statistik i din webbapp. 

Application Insights övervakar din Live-app på en mängd [olika plattformar][platforms] för att hjälpa dig att diagnostisera prestanda problem och förstå användnings mönster.

Det finns flera typer av aviseringar:

* [**Mått aviseringar**](../../azure-monitor/platform/alerts-metric-overview.md) visar när ett Mät värde korsar ett tröskelvärde för en viss period, till exempel svars tider, undantags antal, processor användning eller sid visningar.
* [**Logg aviseringar**](../../azure-monitor/platform/alerts-unified-log.md) används för att beskriva aviseringar där varnings signalen baseras på en anpassad Kusto-fråga.
* [**Webbtester**][availability] meddelar dig när webbplatsen inte är tillgänglig på Internet eller svarar långsamt. [Läs mer][availability].
* [**Proaktiv diagnostik**](../../azure-monitor/app/proactive-diagnostics.md) konfigureras automatiskt för att meddela dig om ovanliga prestanda mönster.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Så här anger du en undantags avisering med anpassad loggs ökning

I det här avsnittet ska vi gå igenom hur du ställer in en fråga-baserad undantags avisering. I det här exemplet vill vi säga att vi vill ha en avisering när den felaktiga frekvensen är större än 10% under de senaste 24 timmarna.

1. Gå till din Application Insight-resurs i Azure Portal.
2. Till vänster under Konfigurera Klicka på **avisering**.

    ![Till vänster under Konfigurera Klicka på avisering](./media/alerts/1appinsightalert.png)

3. Överst på fliken avisering väljer du **ny varnings regel**.

     ![Överst på fliken avisering klickar du på ny aviserings regel](./media/alerts/2createalert.png)

4. Din resurs ska väljas automatiskt. Ange ett villkor genom att klicka på **Lägg till villkor**.

    ![Klicka på Lägg till villkor](./media/alerts/3addcondition.png)

5. På fliken Konfigurera signal logik väljer du **anpassad loggs ökning**

    ![Klicka på anpassad loggs ökning](./media/alerts/4customlogsearch.png)

6. På fliken anpassad loggs ökning anger du din fråga i rutan Sök fråga. I det här exemplet kommer vi att använda nedanstående Kusto-fråga.
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

    ![Skriv frågan i rutan Sök fråga](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Du kan också använda dessa steg för andra typer av frågebaserade aviseringar. Du kan lära dig mer om Kusto-frågespråket från det här [Kusto kom igång-dokumentet](https://docs.microsoft.com/azure/kusto/concepts/) eller det här [SQL till Kusto lathund-bladet](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Under "aviserings logik" väljer du om det baseras på antalet resultat eller mått mått. Välj sedan villkoret (större än, lika med, mindre än) och ett tröskelvärde. När du ändrar de här värdena kan du se om meningen för villkors förhands granskningen ändras. I det här exemplet använder vi "lika med".

    ![Under aviserings logik väljer du bland de alternativ som anges för baserat på och anger sedan ett tröskelvärde](./media/alerts/6alertlogic.png)

8. Under utvärdera baserat på anger du period och frekvens. Perioden här måste matcha det värde som vi angav för perioden i frågan ovan. Klicka sedan på **färdig**.

    ![Ange period och frekvens längst ned och klicka sedan på klart](./media/alerts/7evaluate.png)

9. Vi ser nu det villkor som vi har skapat med den uppskattade månads kostnaden. Under ["åtgärds grupper"](../platform/action-groups.md) kan du skapa en ny grupp eller välja en befintlig. Om du vill kan du anpassa åtgärderna.

    ![Klicka på knapparna Välj eller skapa under åtgärds grupp](./media/alerts/8actiongroup.png)

10. Lägg slutligen till aviserings information (aviserings regel namn, beskrivning, allvarlighets grad). När du är färdig klickar du på **skapa varnings regel** längst ned.

    ![Under aviserings information skriver du aviserings regelns namn, skriver en beskrivning och väljer en allvarlighets grad](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Avbryta prenumerationen på de klassiska e-postaviseringarna

Det här avsnittet gäller för **klassiska tillgänglighets aviseringar**, **klassisk Application Insights mått aviseringar**och **varningar om klassiska fel avvikelser**.

Du får e-postaviseringar för dessa klassiska aviseringar om något av följande gäller:

* Din e-postadress visas i fältet för e-postmottagare i aviserings regeln.

* Alternativet att skicka e-postaviseringar till användare som har vissa roller för prenumerationen aktive ras och du har en respektive roll för just den Azure-prenumerationen.

![Skärm bild för varnings meddelande](./media/alerts/alert-notification.png)

För att bättre kontrol lera säkerheten och sekretessen rekommenderar vi vanligt vis att du uttryckligen anger aviserings mottagarna för dina klassiska aviseringar i fältet **e-postmottagare för avisering** . Alternativet att meddela alla användare som har vissa roller tillhandahålls för bakåtkompatibilitet.

Om du vill avbryta prenumerationen på e-postmeddelanden som genereras av en viss varnings regel tar du bort din e-postadress från fältet **e-postmottagare** .

Om din e-postadress inte uttryckligen visas, rekommenderar vi att du inaktiverar alternativet att meddela alla medlemmar i vissa roller automatiskt, och i stället visar alla e-postmeddelanden som behöver ta emot aviseringar för aviserings regeln i fältet e-postmottagare.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem får aviseringarna (klassisk)?

Det här avsnittet gäller endast för klassiska varningar och hjälper dig att optimera dina aviserings aviseringar så att endast dina mottagare får aviseringar. Om du vill veta mer om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md) och den nya aviserings upplevelsen, se [artikeln aviserings översikt](../platform/alerts-overview.md). Använd [Åtgärds grupper](../platform/action-groups.md)för att kontrol lera aviseringar i den nya aviserings upplevelsen.

* Vi rekommenderar att du använder vissa mottagare för klassisk aviserings aviseringar.

* För aviseringar på alla Application Insights mått (inklusive tillgänglighets mått), är kryss rutan **Mass-/grupp** alternativ, om aktive rad, skickar till användare med rollen ägare, deltagare eller läsare i prenumerationen. I praktiken är _alla_ användare som har åtkomst till prenumerationen Application Insightss resursen inom räckvidden och får meddelanden.

> [!NOTE]
> Om du för närvarande använder alternativet **Mass-/grupp** incheckning, och inaktiverar det, kommer du inte att kunna återställa ändringen.

Använd aviseringarna nya aviseringar/nästan-real tid om du behöver meddela användarna baserat på deras roller. Med [Åtgärds grupper](../platform/action-groups.md)kan du konfigurera e-postaviseringar till användare med någon av rollerna deltagare/ägare/läsare (som inte kombineras tillsammans som ett enda alternativ).

## <a name="automation"></a>Automation
* [Använd PowerShell för att automatisera konfigurationen av aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Använda Webhooks för att automatisera svar på aviseringar](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Se även
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatisera konfigurationen av aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Proaktiv diagnostik](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

