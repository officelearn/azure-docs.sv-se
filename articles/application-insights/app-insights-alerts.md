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
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 1061f356d75037bae440a5289413b2b5d17af1c9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246960"
---
# <a name="set-alerts-in-application-insights"></a>Ställa in aviseringar i Application Insights
[Azure Application Insights] [ start] kan varna dig om ändringar i mått för prestanda och användning i din webbapp. 

Application Insights övervakar din live-app på en [rad olika plattformar] [ platforms] hjälper dig att diagnostisera prestandaproblem och förstå användningsmönster.

Det finns tre typer av aviseringar:

* **Måttaviseringar** berättar när ett mått överskrider ett tröskelvärde under en period – till exempel svarstider, antalet undantag, processoranvändning eller sidvisningar. 
* [**Webbtester** ] [ availability] berätta om din webbplats är inte tillgänglig på internet eller svarar långsamt. [Läs mer][availability].
* [**Proaktiv diagnostik** ](app-insights-proactive-diagnostics.md) konfigureras automatiskt så att du meddelas om ovanliga prestandamönster.

Vi fokuserar på måttaviseringar i den här artikeln.

## <a name="set-a-metric-alert"></a>Ange en metrisk varning
Öppna bladet Varningsregler och Använd knappen Lägg till. 

![I bladet aviseringsregler väljer du Lägg till avisering. Ställ in din app som resursen för att mäta, ange ett namn för aviseringen och välj ett mått.](./media/app-insights-alerts/01-set-metric.png)

* Ställ in resursen innan de andra egenskaperna. **Välj ”(komponenter)” resurs** om du vill ställa in varningar för mått för prestanda och användning.
* Namnet som du ge aviseringen måste vara unikt inom resursgruppen (inte bara ditt program).
* Var noga med att tänka på de enheter där du uppmanas att ange ett tröskelvärde.
* Om du markerar kryssrutan ”e-postägare...” skickas aviseringar via e-post till alla som har åtkomst till den här resursgruppen. Om du vill expandera den här uppsättningen med personer, lägga till dem i den [resursgrupp eller prenumeration](app-insights-resources-roles-access-control.md) (inte på resursen).
* Om du anger ”ytterligare e-postmeddelanden” skickas aviseringar till de enskilda användare eller grupper (om du markerade kryssrutan ”Skicka e-ägare...”). 
* Ange en [webhook adress](../monitoring-and-diagnostics/insights-webhooks-alerts.md) om du har ställt in en webbapp som svarar på aviseringar. Det kallas för både när aviseringen aktiveras och när det är löst. (Men Observera att för närvarande, frågeparametrar skickas inte som webhook-egenskaper.)
* Du kan inaktivera eller aktivera aviseringen: Se knappar längst upp på bladet.

*Knappen Lägg till avisering visas inte.* 

* Använder du ett organisationskonto? Du kan ställa in aviseringar om du har ägare eller deltagare som har åtkomst till den här programresursen. Ta en titt på åtkomstkontroll-bladet. [Lär dig mer om åtkomstkontroll][roles].

> [!NOTE]
> Du ser att det finns redan en avisering uppsättning på aviseringsbladet: [proaktiv diagnostik](app-insights-proactive-failure-diagnostics.md). Automatisk avisering övervakar ett visst mått, begäran Felfrekvens. Såvida du inte vill inaktivera proaktiv avisering, behöver du inte ställa in dina egna avisering på fel hastighet på begäran. 
> 
> 

## <a name="see-your-alerts"></a>Se aviseringarna
Du får ett e-postmeddelande när en avisering ändrar tillstånd mellan inaktiva och aktiva. 

Det aktuella tillståndet för varje avisering visas i bladet Varningsregler.

Det finns en sammanfattning av senaste aktivitet i aviseringarna listrutan:

![Aviseringar i listrutan](./media/app-insights-alerts/010-alert-drop.png)

Historiken över tillståndsändringar är i aktivitetsloggen:

![På bladet översikt klickar du på inställningar, granskningsloggar](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Så fungerar aviseringar
* En avisering har tre tillstånd: ”aldrig aktiverats”, ”aktiverad” och ”matcha”. Aktiverad innebär att du villkoret var SANT, när den senast utvärderades.
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
* **Serverundantagen** – om du vill se dem, du behöver göra några [ytterligare inställningar](app-insights-asp-net-exceptions.md).

Glöm inte att [hastighet för proaktiv feldiagnostik](app-insights-proactive-failure-diagnostics.md) automatiskt övervaka den hastighet med vilken din app svarar på begäranden med felkoder. 

## <a name="automation"></a>Automation
* [Använd PowerShell för att automatisera konfigurationen av aviseringar](app-insights-powershell-alerts.md)
* [Använda webhooks för att automatisera svarar på aviseringar](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Se också
* [Webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md)
* [Automatisera konfigurationen av aviseringar](app-insights-powershell-alerts.md)
* [Proaktiv diagnostik](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

