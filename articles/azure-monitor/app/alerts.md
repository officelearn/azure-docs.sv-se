---
title: Ställa in aviseringar i Azure Application Insights | Microsoft Docs
description: Håll dig informerad om långa svarstider, undantag, och andra prestanda och användning ändringar i din webbapp.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: eb8e98f66d000290ce7eb07d3d73e82fbc43514a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548727"
---
# <a name="set-alerts-in-application-insights"></a>Ställa in aviseringar i Application Insights
[Azure Application Insights] [ start] kan varna dig om ändringar i mått för prestanda och användning i din webbapp. 

Application Insights övervakar din live-app på en [rad olika plattformar] [ platforms] hjälper dig att diagnostisera prestandaproblem och förstå användningsmönster.

Det finns flera typer av aviseringar:

* [**Måttaviseringar** ](../../azure-monitor/platform/alerts-metric-overview.md) berättar när ett mått överskrider ett tröskelvärde under en period – till exempel svarstider, antalet undantag, processoranvändning eller sidvisningar.
* [**Loggaviseringar** ](../../azure-monitor/platform/alerts-unified-log.md) används för att beskriva aviseringar där varningssignal baseras på en anpassad Kusto-fråga.
* [**Webbtester** ] [ availability] berätta om din webbplats är inte tillgänglig på internet eller svarar långsamt. [Läs mer][availability].
* [**Proaktiv diagnostik** ](../../azure-monitor/app/proactive-diagnostics.md) konfigureras automatiskt så att du meddelas om ovanliga prestandamönster.

## <a name="set-a-metric-alert"></a>Ange en metrisk varning
Öppna fliken Varningsregler och Använd knappen Lägg till.

![Välj Lägg till avisering på fliken Varningsregler. Ställ in din app som resursen för att mäta, ange ett namn för aviseringen och välj ett mått.](./media/alerts/01-set-metric.png)

* Ställ in resursen innan de andra egenskaperna. **Välj ”(komponenter)” resurs** om du vill ställa in varningar för mått för prestanda och användning.
* Namnet som du ge aviseringen måste vara unikt inom resursgruppen (inte bara ditt program).
* Var noga med att tänka på de enheter där du uppmanas att ange ett tröskelvärde.
* Om du markerar kryssrutan ”e-postägare...” skickas aviseringar via e-post till alla som har åtkomst till den här resursgruppen. Om du vill expandera den här uppsättningen med personer, lägga till dem i den [resursgrupp eller prenumeration](../../azure-monitor/app/resources-roles-access-control.md) (inte på resursen).
* Om du anger ”ytterligare e-postmeddelanden” skickas aviseringar till de enskilda användare eller grupper (om du markerade kryssrutan ”Skicka e-ägare...”). 
* Ange en [webhook adress](../../azure-monitor/platform/alerts-webhooks.md) om du har ställt in en webbapp som svarar på aviseringar. Det kallas för både när aviseringen aktiveras och när det är löst. (Men Observera att för närvarande, frågeparametrar skickas inte som webhook-egenskaper.)
* Du kan inaktivera eller aktivera aviseringen: Se knappar längst upp.

*Knappen Lägg till avisering visas inte.*

* Använder du ett organisationskonto? Du kan ställa in aviseringar om du har ägare eller deltagare som har åtkomst till den här programresursen. Ta en titt på fliken behörighet. [Lär dig mer om åtkomstkontroll][roles].

> [!NOTE]
> På aviseringsbladet se du att det finns redan en avisering uppsättning: [Proaktiv diagnostik](../../azure-monitor/app/proactive-failure-diagnostics.md). Automatisk avisering övervakar ett visst mått, begäran Felfrekvens. Såvida du inte vill inaktivera proaktiv avisering, behöver du inte ställa in dina egna avisering på fel hastighet på begäran.
> 
> 

## <a name="see-your-alerts"></a>Se aviseringarna
Du får ett e-postmeddelande när en avisering ändrar tillstånd mellan inaktiva och aktiva. 

Det aktuella tillståndet för varje avisering visas på fliken Varningsregler.

Det finns en sammanfattning av senaste aktivitet i aviseringarna listrutan:

![Aviseringar i listrutan](./media/alerts/010-alert-drop.png)

Historiken över tillståndsändringar är i aktivitetsloggen:

![Klicka på inställningar, granskningsloggar på fliken Översikt](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Så fungerar aviseringar
* En avisering har tre lägen: ”Aldrig aktiverats”, ”aktiverad” och ”löst”. Aktiverad innebär att du villkoret var SANT, när den senast utvärderades.
* En avisering genereras när status ändras i en avisering. (Om aviseringstillståndet var redan sant när du har skapat aviseringen, du kan inte få ett meddelande tills villkoret sjunker till FALSKT.)
* Varje meddelande genererar ett e-postmeddelande om du har markerat kryssrutan e-postmeddelanden eller tillhandahålls av e-postadresser. Du kan också titta på listan meddelanden.
* En avisering utvärderas varje gång som ett mått tas emot, men i övrigt inte.
* Utvärderingen aggregerar måttet under föregående period och jämför den med tröskelvärdet för att fastställa det nya läget.
* Den period som du anger intervallet över vilket sammanställs måtten. Det påverkar inte hur ofta aviseringen ska utvärderas: Det beror på frekvensen för ankomst mått.
* Om inga data tas emot för ett visst mått under en viss tid har klyftan olika effekter på aviseringen utvärdering och diagram i metric explorer. I metric explorer om inga data visas under längre tid än diagrammets exempelintervall visar diagrammet värdet 0. Men en avisering baserat på samma mått är inte vara omvärderas och aviseringens status ändras inte. 
  
    När data kommer så småningom, hoppar diagrammet till ett annat värde än noll. Aviseringen utvärderar baserat på data som är tillgängliga under den period som du angav. Om den nya datapunkten är enda tillgängliga under perioden, baseras samlingen bara på att datapunkt.
* En avisering flimra ofta mellan aviseringar och felfritt tillstånd, även om du ställer in en längre tid. Detta kan inträffa om mätvärdet hovrar runt tröskelvärdet. Det finns inga betraktas som tröskelvärde: övergången till aviseringen sker på samma värde som övergång till felfritt.

## <a name="what-are-good-alerts-to-set"></a>Vad är bra aviseringar att ställa in?
Det beror på ditt program. Det är att börja med, bör du inte ange för många mått. Ägna lite tid som tittar på dina diagram med mätvärden när din app körs, för att få en känsla för hur den fungerar normalt. Den här metoden hjälper dig att hitta sätt att förbättra dess prestanda. Ställa in aviseringar som talar om när mått som går utanför den normala zonen. 

Populära aviseringar innehåller:

* [Webbläsaren mått][client], särskilt webbläsare **sidan inläsningstider**, är bra för webbprogram. Om din sida har många skript kan du ska leta efter **webbläsarundantag**. För att få dessa mått och aviseringar, du måste konfigurera [webbsida övervakning][client].
* **Svarstid för servern** för serversidan webbprogram. Håll ett öga på det här måttet att se om det beror oproportionerligt hög begäranhastigheter samt ställa in aviseringar: variationen kan tyda på att din app är igång tillräckligt med resurser. 
* **Serverundantagen** – om du vill se dem, du behöver göra några [ytterligare inställningar](../../azure-monitor/app/asp-net-exceptions.md).

Glöm inte att [hastighet för proaktiv feldiagnostik](../../azure-monitor/app/proactive-failure-diagnostics.md) automatiskt övervaka den hastighet med vilken din app svarar på begäranden med felkoder.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Hur du ställer in en undantagsaviseringen med hjälp av anpassade loggsökning

I det här avsnittet lär du dig hur du ställer en fråga baserad undantagsaviseringen. I det här exemplet vi antar att vi vill att en avisering när misslyckade frekvensen är större än 10% under de senaste 24 timmarna.

1. Gå till din Application Insights-resurs i Azure-portalen.
2. Till vänster under Konfigurera Klicka på **avisering**.

    ![Till vänster under Konfigurera aviseringen](./media/alerts/1appinsightalert.png)

3. Överst på aviseringsfliken väljer **ny aviseringsregel**.

     ![Klicka på ny aviseringsregel högst upp på fliken aviseringar](./media/alerts/2createalert.png)

4. Resursen ska vara automatisk. Ange ett villkor, klicka på **Lägg till villkor**.

    ![Klicka på Lägg till villkor](./media/alerts/3addcondition.png)

5. Välj fliken Konfigurera signalen logic **anpassad loggsökning**

    ![Klicka på den anpassade loggen Sök](./media/alerts/4customlogsearch.png)

6. Ange din fråga i rutan ”sökfrågan” i sökfliken anpassad logg. I det här exemplet ska vi använda den nedan Kusto-fråga.
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

    ![Skriv frågan i sökrutan för fråga](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Du kan även använda de här stegen för andra typer av frågebaserade aviseringar. Du kan lära dig mer om Kusto-frågespråket från den här [Kusto komma igång doc](https://docs.microsoft.com/azure/kusto/concepts/) eller i den här [SQL till Kusto-Lathund](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Välj om den baseras på antalet resultat eller metriska måttenheter under ”Alert logic”. Välj villkoret (större än, lika med, mindre än) och ett tröskelvärde. När du ändrar dessa värden kan märka du att villkoret Förhandsgranska mening ändringar. I det här exemplet använder vi ”lika med”.

    ![Under Alert logic välja bland alternativen för baserat på villkor och ange sedan ett tröskelvärde](./media/alerts/6alertlogic.png)

8. Under ”Evaluated baserat på”, ange perioden och frekvens. Den här perioden måste matcha det värde som låter vi för period i frågan ovan. Klicka sedan på **klar**.

    ![Ange perioden och frekvens längst ned på sidan och sedan klicka på klar](./media/alerts/7evaluate.png)

9. Vi kan nu se det villkoret som vi skapade med den uppskattade månadskostnaden. Nedan under [”åtgärdsgrupper”](../platform/action-groups.md) du kan skapa en ny grupp eller välj en befintlig. Om du vill kan anpassa du åtgärderna.

    ![Klicka på Välj eller skapa knappar under åtgärdsgrupp](./media/alerts/8actiongroup.png)

10. Slutligen lägger du till din aviseringsinformation (Avisera namn, beskrivning, allvarlighetsgrad). När du är klar klickar du på **skapa varningsregel** längst ned på sidan.

    ![Under aviseringsinformation ange varningsregelns namn, Skriv en beskrivning och välj en allvarlighetsgrad](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Hur du slutar prenumerera på klassiska avisering e-postaviseringar

Det här avsnittet gäller för **klassiska tillgänglighet aviseringar**, **klassiska måttaviseringar för Application Insights**, och **klassiska avvikelser aviseringar**.

Du får e-postaviseringar för dessa klassiska aviseringar om något av följande gäller:

* Din e-postadress visas i fältet meddelande e-postmottagare i inställningarna för varningsregeln.

* Alternativet för att skicka e-postaviseringar till användare som innehåller vissa roller för prenumerationen har aktiverats och du håller en respektive roll för den specifika Azure-prenumerationen.

![Avisering om skärmbild](./media/alerts/alert-notification.png)

Att få bättre kontroll över din säkerhet och sekretess i allmänhet rekommenderar vi att du uttryckligen anger meddelandemottagare för klassiska varningar i den **meddelande e-postmottagare** fält. Alternativ för att meddela alla användare som innehåller vissa roller tillhandahålls för bakåtkompatibilitet.

Om du vill avbryta prenumerationen på e-postmeddelanden som genereras av en viss aviseringsregel, tar du bort din e-postadress från den **meddelande e-postmottagare** fält.

Om din e-postadress inte visas uttryckligen, rekommenderar vi att du inaktiverar alternativet att automatiskt meddela alla medlemmar i vissa roller och i stället lista över alla användare e-postmeddelanden som ska ta emot meddelanden för den varningsregeln i e-postmeddelande mottagare fält.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem som får aviseringar (klassisk)?

Det här avsnittet gäller för klassiska aviseringar endast och hjälper dig att optimera dina aviseringar till att säkerställa att endast dina önskade mottagare får meddelanden. Vill veta mer om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md) och nya aviseringar uppstår, referera till den [aviseringar översikten](../platform/alerts-overview.md). Om du vill styra avisering om i det nya aviseringsgränssnittet använder [åtgärdsgrupper](../platform/action-groups.md).

* Vi rekommenderar användning av specifika mottagare för klassiska aviseringar.

* För aviseringar i alla Application Insights-mått (inklusive mått på tillgänglighet), den **grupp/grupp** kryssrutan alternativet, om aktiverad, skickar till användare med ägare, deltagare eller läsare roller i prenumerationen. I praktiken _alla_ användare med åtkomst till prenumerationen Application Insights-resursen omfattas och ska ta emot meddelanden.

> [!NOTE]
> Om du använder den **grupp/grupp** kryssrutan alternativet, och inaktivera det, kommer du inte kunna återställa ändringen.

Använd de nya upplevelse nära realtid/aviseringarna om du vill meddela användare baserat på deras roller. Med [åtgärdsgrupper](../platform/action-groups.md), du kan konfigurera e-postaviseringar till användare med någon av rollerna deltagare och ägare/läsare (inte kombineras tillsammans som ett alternativ).

## <a name="automation"></a>Automation
* [Använd PowerShell för att automatisera konfigurationen av aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Använda webhooks för att automatisera svarar på aviseringar](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Se också
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatisera konfigurationen av aviseringar](../../azure-monitor/app/powershell-alerts.md)
* [Proaktiv diagnostik](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

