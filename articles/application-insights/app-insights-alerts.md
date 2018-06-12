---
title: Ställ in aviseringar i Azure Application Insights | Microsoft Docs
description: Håll dig informerad om långa svarstider, undantag, andra prestanda och användning av ändringar i ditt webbprogram.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 87be1a48a6c3320187243e549a8fb8e5ecc9e006
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293611"
---
# <a name="set-alerts-in-application-insights"></a>Ställ in aviseringar i Application Insights
[Azure Application Insights] [ start] kan meddela dig för ändringar i prestanda eller användningsområden mätvärden i ditt webbprogram. 

Application Insights övervakar din aktiva app på en [flera olika plattformar] [ platforms] som hjälper dig att diagnostisera prestandaproblem och förstå användningsmönster.

Det finns tre typer av aviseringar:

* **Mått aviseringar** berättar när ett mått överskrider ett tröskelvärde för vissa punkt - till exempel svarstider, antalet undantag, CPU-användning eller sidvisningar. 
* [**Webbtester** ] [ availability] berättar som din webbplats är tillgänglig på internet, eller så svarar långsamt. [Lär dig mer][availability].
* [**Proaktiv diagnostik** ](app-insights-proactive-diagnostics.md) konfigureras automatiskt så att du meddelas om ovanliga prestandamönster.

Vi fokusera på mått aviseringar i den här artikeln.

## <a name="set-a-metric-alert"></a>Ange en avisering om mått
Öppna bladet Varningsregler och sedan använda knappen Lägg till. 

![Välj Lägg till avisering i bladet Varningsregler. Ange din app som resursen att mäta, ange ett namn för aviseringen och välja ett mått.](./media/app-insights-alerts/01-set-metric.png)

* Ange resursen innan de andra egenskaperna. **Välj ”(komponenter)” resursen** om du vill ställa in varningar på prestanda eller användningsområden mätvärden.
* Namnet som du ger aviseringen måste vara unikt inom resursgruppen (inte bara program).
* Var noga med att känna till de enheter där du uppmanas att ange ett tröskelvärde.
* Om du markerar rutan ”e-ägare...” skickas aviseringar via e-post till alla som har åtkomst till den här resursgruppen. Om du vill expandera den här uppsättningen personer, lägga till dem i den [resursgrupp eller prenumeration](app-insights-resources-roles-access-control.md) (inte på resursen).
* Om du anger ”ytterligare e-postmeddelanden” skickas aviseringar till de personer eller grupper (om du markerade kryssrutan ”Skicka e-ägare...”). 
* Ange en [webhook adress](../monitoring-and-diagnostics/insights-webhooks-alerts.md) om du har skapat ett webbprogram som svarar på aviseringar. Det kallas både när aviseringen aktiveras och när den är löst. (Men Observera att för närvarande, frågeparametrar skickas inte via webhook-egenskaper.)
* Du kan inaktivera eller aktivera en avisering: Se knappar längst upp på bladet.

*Knappen Lägg till avisering visas inte.* 

* Använder du ett organisationskonto? Du kan ställa in varningar om du har ägare eller deltagare som har åtkomst till den här resursen i programmet. Ta en titt på bladet för åtkomstkontroll. [Lär dig mer om åtkomstkontroll][roles].

> [!NOTE]
> Du ser att det finns redan en avisering uppsättning i bladet aviseringar: [Proactive Diagnostics](app-insights-proactive-failure-diagnostics.md). Automatisk avisering övervakar ett viss mått, misslyckandegrad. Om du inte vill inaktivera proaktiv avisering behöver du inte ange egna avisering på misslyckandegrad. 
> 
> 

## <a name="see-your-alerts"></a>Se aviseringar
Du får ett e-postmeddelande när en avisering ändrar tillstånd mellan inaktivt och aktivt. 

Det aktuella tillståndet för varje avisering visas i bladet Varningsregler.

Det finns en sammanfattning av de senaste aktivitet i aviseringar listrutan:

![Aviseringar listrutan](./media/app-insights-alerts/010-alert-drop.png)

Historik över tillståndsändringar är i aktivitetsloggen:

![Klicka på inställningar, granskningsloggar på bladet översikt](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Så fungerar aviseringar
* En avisering har tre lägen: ”aldrig aktiverats”, ”aktiverad” och ”löst”. Aktiverad innebär det villkor du anger var true när den senast utvärderades.
* En avisering genereras när en avisering ändrar tillstånd. (Om aviseringstillståndet var redan sant när du skapade aviseringen kan du kanske inte får ett meddelande tills villkoret blir FALSKT.)
* Varje meddelande genererar ett e-postmeddelande om du har markerat kryssrutan e-postmeddelanden eller angivna e-postadresser. Du kan också titta på listan meddelanden.
* En avisering utvärderas för varje gång ett mått tas emot, men inte på annat sätt.
* Utvärderingen aggregerar måttet under föregående period och sedan jämförs med tröskelvärdet för att fastställa det nya läget.
* Den period som du anger intervallet över vilket aggregeras mått. Det påverkar inte hur ofta aviseringen ska utvärderas: som beror på frekvensen för ankomsten av mått.
* Om inga data kommer för ett viss mått under en viss tid, har mellanrummet olika effekter på aviseringen utvärdering och diagram i mått explorer. I mått explorer om inga data visas under längre tid än diagrammets exempelintervall visar diagrammet värdet 0. Men en avisering baserat på samma mått kan inte vara reevaluated och den avisering tillstånd ändras inte. 
  
    När data kommer så småningom, hoppar diagrammet tillbaka till ett värde som inte är noll. Aviseringen utvärderar baserat på data som är tillgängliga för den angivna perioden. Om den nya datapunkten är enda tillgängliga under perioden, baseras mängdfunktionen bara att datapunkt.
* En avisering flimra ofta mellan varning och felfritt tillstånd, även om du anger en längre tid. Detta kan inträffa om måttet är placerad runt tröskelvärdet. Det finns inga betraktas som tröskelvärde: övergången till avisering sker på samma värde som övergång till felfritt.

## <a name="what-are-good-alerts-to-set"></a>Vad är bra aviseringar för att ställa in?
Det beror på ditt program. Börja med, rekommenderas inte att ställa in för många mått. Ägna en stund tittar på dina mått diagram när appen körs, om du vill få en bild av hur den fungerar normalt. Den här övningen hjälper dig att hitta sätt att förbättra prestandan. Ställa in aviseringar som talar om när mätvärdena går utanför normal zonen. 

Populära aviseringar är:

* [Webbläsaren mått][client], särskilt webbläsare **sidan laddningstider**, är bra för webbprogram. Om sidan har många skript, ska du leta efter **webbläsarundantag**. För att få dessa mätvärden och aviseringar, du måste registrera [webbsida övervakning][client].
* **Serversvarstid** för serversidan av webbprogram. Hålla ett öga på det här måttet att se om det beror oproportionerligt hög begäran priser samt att konfigurera aviseringar: variationen kan tyda på att din app körs slut på resurser. 
* **Undantag för** - om du vill se dem, du behöver göra några [ytterligare inställningar](app-insights-asp-net-exceptions.md).

Glöm inte som [proaktiv fel hastighet diagnostik](app-insights-proactive-failure-diagnostics.md) automatiskt övervaka den hastighet som din app svarar på begäranden med felkoder. 

## <a name="automation"></a>Automation
* [Använd PowerShell för att automatisera ställa in aviseringar](app-insights-powershell-alerts.md)
* [Använd webhooks för att automatisera svarar på aviseringar](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Se också
* [Tillgänglighetstester för webbprogram](app-insights-monitor-web-app-availability.md)
* [Automatisera ställa in aviseringar](app-insights-powershell-alerts.md)
* [Proaktiv diagnostik](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

