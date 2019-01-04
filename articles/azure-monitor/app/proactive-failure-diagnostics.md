---
title: Smart identifiering – felavvikelser i Application Insights | Microsoft Docs
description: Varnar dig för ovanliga ändringar av misslyckade begäranden till webbappen och ger diagnostisk analys. Ingen konfiguration krävs.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 901e12e989bb5a6f18c79e086357624648b1b7b9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028855"
---
# <a name="smart-detection---failure-anomalies"></a>Smart identifiering – Felavvikelser
[Application Insights](../../application-insights/app-insights-overview.md) meddelar i nära realtid om din webbapp upplever en onormal ökning av antalet misslyckade förfrågningar. Den identifierar en onormal ökning av HTTP-begäranden eller beroendeanrop som rapporteras som misslyckad. För begäranden är misslyckade förfrågningar vanligtvis de med svarskoder på 400 eller högre. För att hjälpa dig att hantera och diagnostisera problemet, finns en analys av egenskaperna för fel och relaterad telemetri i meddelandet. Det finns också länkar till Application Insights-portalen för ytterligare diagnos. Funktionen behöver ingen installation eller konfiguration, eftersom den använder machine learning-algoritmer för att förutsäga normalt felintervall.

Den här funktionen fungerar för Java- och ASP.NET web apps, finns i molnet eller på dina egna servrar. Den fungerar även för alla appar som genererar begäran eller beroende telemetri – till exempel om du har en arbetsroll som anropar [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) eller [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

När du har installerat [Application Insights för ditt projekt](../../application-insights/app-insights-overview.md), och under förutsättning att din app genererar en viss minsta mängd telemetri, Smart identifiering av avvikelser i fel tar 24 timmar att lära dig det normala beteendet för din app, innan den är aktiverat och som kan skicka aviseringar.

Här är en exempel-avisering.

![Smart identifiering aviseringen som visar kluster analysis runt fel](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Som standard får du en kortare format e-post än det här exemplet. Men du kan [växla till den här detaljerade format](#configure-alerts).
>
>

Observera att du:

* Den felintervall jämfört med normala appbeteendet.
* Hur många användare påverkas - så att du vet hur mycket oroa dig.
* En karakteristisk mönster som är associerade med felen. I det här exemplet finns en viss svarskod, namnet på begäran (åtgärd) och programversion. Som informerar du omedelbart var du vill börja söka i din kod. Andra möjligheter kan vara ett specifikt operativsystem webbläsare eller klient.
* De undantag, loggspårningar och beroendefel (databaser eller andra externa komponenter) som visas som ska associeras med utmärkande felen.
* Länkar direkt till relevanta sökningar på telemetri i Application Insights.

## <a name="benefits-of-smart-detection"></a>Fördelarna med Smart identifiering
Vanlig [måttaviseringar](../../azure-monitor/app/alerts.md) talar om det kan finnas ett problem. Men Smart identifiering startar diagnostiska arbetet åt dig, utför mycket analys som du annars skulle behöva göra själv. Du får de resultat som snyggt paketeras, vilket hjälper dig att snabbt komma till roten till problemet.

## <a name="how-it-works"></a>Hur det fungerar
Smart identifiering övervakar telemetri som togs emot från din app, särskilt i Felfrekvens. Den här regeln räknar antalet begäranden som den `Successful request` egenskapen är false och antalet beroende anrop som den `Successful call` egenskapen är false. För förfrågningar om som standard `Successful request == (resultCode < 400)` (såvida inte du har skrivit anpassad kod för att [filter](../../azure-monitor/app/api-filtering-sampling.md#filtering) eller skapa egna [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) anrop). 

Din Apps prestanda har ett typiskt mönster för beteende. Vissa begäranden eller beroendeanrop kommer att orsaka fel än andra. och den totala Felfrekvensen som kan gå upp när belastningen ökar. Smart identifiering använder maskininlärning att hitta dessa avvikelser.

När det kommer telemetri till Application Insights från din webbapp, jämför Smart identifiering aktuella beteendet med mönster över de senaste dagarna. Om onormala ökningar i Felfrekvens observeras genom jämförelse med föregående prestanda, utlöses en analys.

När en analys utlöses, utför tjänsten en kluster-analys på den misslyckade begäranden att identifiera ett mönster med värden som utmärker felen. I exemplet ovan har analysen upptäckt att de flesta fel är om en specifik Resultatkod, namnet på begäran, Server-URL-värden och rollinstansen. Däremot har analysen upptäckt att egenskapen client operativsystem distribueras över flera värden och så det inte visas.

När tjänsten är utrustade med dessa telemetri-anrop, söker analysatorn efter ett undantag och ett beroendefel som är associerade med begäranden i klustret har identifierat tillsammans med ett exempel på några spårningsloggar som associeras med dessa förfrågningar.

Resulterande analysen skickas till dig som aviseringen om du har inte konfigurerat den.

Som den [aviseringar du manuellt ange](../../azure-monitor/app/alerts.md), du kan granska status för aviseringen och konfigurera den på bladet aviseringar i Application Insights-resursen. Men till skillnad från andra aviseringar, du behöver inte konfigurera eller konfigurera Smart identifiering. Om du vill kan du inaktivera den eller ändra dess mål e-postadresser.

### <a name="alert-logic-details"></a>Aviseringslogik information

Aviseringarna utlöses av våra egna maskininlärningsalgoritmen så att vi inte kan dela de exakta implementeringsdetaljerna. Därmed sade förstår vi att du ibland behöva veta mer om hur den underliggande logiken fungerar. De främsta faktorerna som utvärderas för att avgöra om en avisering ska utlösas är: 

* Analys av körningsfel i procent av begäranden/beroenden i ett rullande tidsfönster på 20 minuter.
* En jämförelse av körningsfel i procent av de senaste 20 minuterna till priset i de senaste 40 minuterna och de senaste sju dagarna och söker efter betydande avvikelser som överstiger X gånger den standardavvikelsen.
* Använder en anpassningsbar gräns för den minsta felprocent, utifrån som varierar appens mängden begäranden/beroenden.

## <a name="configure-alerts"></a>Konfigurera varningar
Du kan inaktivera Smart identifiering, ändra e-postmottagare, skapa en webhook eller välja att mer detaljerad varningsmeddelanden.

Öppna sidan aviseringar. Felavvikelser ingår tillsammans med eventuella aviseringar som du har angett manuellt och du kan se om det är för närvarande i aviseringstillståndet.

![Klicka på panelen aviseringar på sidan Översikt. Eller alla mått-sidan, klicka på knappen för aviseringar.](./media/proactive-failure-diagnostics/021.png)

Klicka på aviseringen om du vill konfigurera den.

![Konfiguration](./media/proactive-failure-diagnostics/032.png)

Observera att du kan inaktivera Smart identifiering, men du kan inte ta bort den (eller skapa en annan).

#### <a name="detailed-alerts"></a>Detaljerad aviseringar
Om du väljer ”få mer detaljerad analys” ska e-postmeddelandet innehålla mer diagnostisk information. Ibland kommer du att kunna diagnostisera problemet bara från data i e-postmeddelandet.

Det finns en liten risk att mer detaljerad aviseringen kan innehålla känslig information, eftersom den innehåller undantag och spår. Men händer detta enbart om din kod kan innebära att känslig information i dessa meddelanden.

## <a name="triaging-and-diagnosing-an-alert"></a>Bedömning och diagnos av en avisering
En varning som anger att en onormal ökning av misslyckade begäranden har identifierats. Det är troligt att det finns problem med din app eller dess miljö.

Du kan bestämma hur brådskande problemet är från procent av begäranden och antalet användare som påverkas. I exemplet ovan misslyckandegrad för 22,5% jämförs med en normal taxa för % 1, indikerar att ett fel som händer. Å andra sidan, påverkades endast 11 användare. Om det vore din app skulle du kunna bedöma hur allvarlig som är.

I många fall kommer du att kunna diagnostisera problem snabbt från namnet på begäran, undantag, fel och spåra data för programberoende tillhandahålls.

Det finns några andra ledtrådar. Till exempel är Felfrekvensen beroende i det här exemplet samma som antal undantag (89.3%). Det tyder på att undantaget uppstår direkt från beroendefel – vilket ger dig en tydlig bild av var du vill börja söka i din kod.

Om du vill undersöka vidare på länkarna i varje avsnitt leder dig direkt till en [söksidan](../../azure-monitor/app/diagnostic-search.md) filtrerat till relevanta begäranden, undantag, beroenden eller spårningar. Du kan också öppna den [Azure-portalen](https://portal.azure.com), navigera till Application Insights-resurs för din app och öppna bladet fel.

I det här exemplet öppnas att klicka på länken ”Visa information om beroenden fel” bladet search Application Insights. Den visar SQL-instruktionen som innehåller ett exempel på den bakomliggande orsaken: Null-värden har angetts i obligatoriska fält och klarade inte verifieringen under spara igen.

![Diagnostiksökning](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Granska de senaste aviseringarna

Klicka på **Smart identifiering** att komma till den senaste aviseringen:

![Sammanfattning av aviseringar](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Vad är skillnaden...
Smart identifiering av avvikelser i fel kompletterar andra liknande men olika funktioner för Application Insights.

* [Måttaviseringar](../../azure-monitor/app/alerts.md) konfigureras av dig och kan övervaka en mängd olika mått som CPU-användandet, begäranhastigheter, sidinläsningstider och så vidare. Du kan använda dem för att varna dig, till exempel om du behöver lägga till fler resurser. Däremot täcker Smart identifiering av avvikelser i fel ett litet antal viktiga mått (för närvarande endast misslyckade begäranden pris), som utformats för att meddela dig i nära realtid sätt när din webbapp godkändes inte begära satsen ökar markant i förhållande till webbapp normalt beteende.

    Smart identifiering justeras automatiskt dess tröskelvärdet som svar på rådande villkor.

    Smart identifiering startar diagnostiska arbetet åt dig.
* [Smart identifiering om prestandaavvikelser](proactive-performance-diagnostics.md) också använder machine för att upptäcka onormala mönster i dina mått och krävs ingen konfiguration av dig. Men till skillnad från Smart identifiering av avvikelser i fel Smart identifiering om prestandaavvikelser syftar till att upptäcka segment av din användning samlingsrör som felaktigt kan hanteras, till exempel av specifika sidor på en viss typ av webbläsare. Analysen utförs varje dag och om alla resultat hittas, är det troligt att vara mycket mindre brådskande än en avisering. Däremot analys för felavvikelser utförs kontinuerligt på inkommande telemetri, och du meddelas inom några minuter om server Felfrekvens är större än förväntat.

## <a name="if-you-receive-a-smart-detection-alert"></a>Om du får en avisering om Smart identifiering
*Varför har jag fått den här aviseringen?*

* Vi har identifierat en onormal uppgång av misslyckade begäranden jämfört med normala baslinje för den inledande punkten. Vi tror att det finns ett problem som bör du fundera på att efter analys av fel och telemetri.

*Betyder meddelandet jag definitivt har problem?*

* Vi försöker Avisera om avbrott i appen eller tjänsten, men endast du kan helt förstå semantiken och påverkan på app eller användare.

*Därför ni titta på Mina data?*

* Nej. Tjänsten är helt automatisk. Du får endast meddelanden. Dina data är [privata](../../azure-monitor/app/data-retention-privacy.md).

*Måste jag prenumerera på den här aviseringen?*

* Nej. Varje program som skickar begäran telemetri har varningsregel för Smart identifiering.

*Kan jag säga upp prenumerationen eller få meddelanden som skickas till min kollegor i stället?*

* Ja, i Varningsregler, klickar du på regel för Smart identifiering för att konfigurera den. Du kan inaktivera aviseringen eller ändra mottagarna för aviseringen.

*E-postmeddelandet gick förlorad. Var hittar jag aviseringar i portalen?*

* I aktivitetsloggarna. Öppna Application Insights-resurs för din app i Azure, och välj aktivitetsloggar.

*Vissa av aviseringarna som handlar om kända problem och jag vill inte ta emot dem..*

* Vi har Undertryckning av aviseringar på våra återstående uppgifter.

## <a name="next-steps"></a>Nästa steg
Dessa diagnostiska verktyg hjälper dig att inspektera telemetrin från din app:

* [Metric explorer](../../azure-monitor/app/metrics-explorer.md)
* [Sökutforskaren](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – kraftfullt frågespråk](../../azure-monitor/log-query/get-started-portal.md)

Smart identifieringar är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade måttaviseringar](../../azure-monitor/app/alerts.md)
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
