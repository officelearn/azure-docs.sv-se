---
title: Smart identifiering – fel avvikelser, i Application Insights | Microsoft Docs
description: Varnar dig om ovanliga ändringar av antalet misslyckade förfrågningar till din webbapp och ger diagnostisk analys. Ingen konfiguration krävs.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820585"
---
# <a name="smart-detection---failure-anomalies"></a>Smart identifiering – fel avvikelser
[Application Insights](../../azure-monitor/app/app-insights-overview.md) automatiskt meddelar dig i nära real tid om din webbapp upplever en onormal ökning av antalet misslyckade förfrågningar. Den identifierar en ovanlig ökning i frekvensen av HTTP-begäranden eller beroende anrop som rapporteras som misslyckade. För förfrågningar är misslyckade förfrågningar vanligt vis de med svars koderna 400 eller högre. För att hjälpa dig att prioritering och diagnostisera problemet finns en analys av egenskaperna hos felen och relaterad telemetri i meddelandet. Det finns också länkar till Application Insights Portal för ytterligare diagnos. Funktionen behöver inte konfigureras eller konfigureras eftersom den använder Machine Learning-algoritmer för att förutsäga den normala felfrekvensen.

Den här funktionen fungerar för alla webb program, som finns i molnet eller på dina egna servrar, som genererar begäran eller beroende telemetri, till exempel om du har en arbets roll som anropar [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) eller [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

När du har konfigurerat [Application Insights för ditt projekt](../../azure-monitor/app/app-insights-overview.md)och angett att din app genererar en viss minsta mängd telemetri, tar det en smart identifiering av fel avvikelser 24 timmar att lära sig om den normala appens beteende, innan den växlas på och kan skicka aviseringar.

Här är ett exempel på en avisering.

![Exempel på Smart identifierings avisering som visar kluster analys kring fel](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Som standard får du ett kortare format e-post än det här exemplet. Men du kan [Växla till det här detaljerade formatet](#configure-alerts).
>
>

Observera att det står:

* Felfrekvensen jämfört med normalt appens beteende.
* Hur många användare påverkas – så att du vet hur mycket du behöver oroa dig.
* Ett egenskaps mönster som är kopplat till felen. I det här exemplet finns det en viss svarskod, ett namn på begäran (åtgärd) och en app-version. Det visar genast var du börjar titta på koden. Andra möjligheter kan vara en speciell webbläsare eller ett klient operativ system.
* Undantag, logg spårningar och beroende fel (databaser eller andra externa komponenter) som verkar vara associerade med de felbara felen.
* Länkar direkt till relevanta sökningar i Application Insights.

## <a name="failure-anomalies-v2"></a>Fel avvikelser v2
En ny version av varnings regeln för fel avvikelser är nu tillgänglig. Den här nya versionen körs på den nya Azure Alerting-plattformen och introducerar en rad olika förbättringar jämfört med den befintliga versionen.

### <a name="whats-new-in-this-version"></a>Vad är nytt i den här versionen?
- Snabbare identifiering av problem
- En omfattande uppsättning åtgärder – varnings regeln skapas med en tillhör ande [Åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) med namnet "Application Insights Smart identifiering" som innehåller e-post-och webhook-åtgärder och kan utökas för att utlösa ytterligare åtgärder när aviseringen utlöses.
- Mer fokuserade aviseringar – e-postmeddelanden som skickas från den här varnings regeln skickas nu som standard till användare som är kopplade till prenumerationens övervaknings läsare och övervaknings deltagar roller. Mer [information finns här.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)
- Enklare konfiguration via ARM-mallar – se exempel [här](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Vanliga aviserings schema stöd – meddelanden som skickas från den här aviserings regeln följer [vanliga aviserings scheman](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Enhetlig e-postmall – e-postaviseringar från den här varnings regeln har ett konsekvent utseende & känner till andra aviserings typer. Med den här ändringen är alternativet för att få fel avvikelser om aviseringar med detaljerad diagnostikinformation inte längre tillgängligt.

### <a name="how-do-i-get-the-new-version"></a>Hur gör jag för att hämta den nya versionen?
- Nyskapade Application Insightss resurser tillhandahålls nu med den nya versionen av aviserings regeln för fel avvikelser.
- Befintliga Application Insights-resurser med den klassiska versionen av aviserings regeln för fel avvikelser får den nya versionen när deras värd prenumeration migreras till den nya varnings plattformen som en del av den [klassiska aviserings processen](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> Den nya versionen av varnings regeln för fel avvikelser är fortfarande kostnads fri. Dessutom är de åtgärder för e-post och webhook som utlösts av den associerade åtgärds gruppen Application Insights Smart avkänning också tillgängliga.
> 
> 

## <a name="benefits-of-smart-detection"></a>Förmåner för smart identifiering
Vanliga [mått aviseringar](../../azure-monitor/app/alerts.md) visar att det kan finnas ett problem. Men smart identifiering startar diagnostiskt arbete åt dig, och utför en massa analyser som du annars skulle behöva göra själv. Du får de resultat som du kan paketera, vilket hjälper dig att snabbt komma till orsaken till problemet.

## <a name="how-it-works"></a>Så här fungerar det
Smart identifiering övervakar den telemetri som tas emot från din app och särskilt fel frekvensen. Den här regeln räknar antalet begär Anden för vilka egenskapen `Successful request` är falsk och antalet beroende anrop som `Successful call`-egenskapen är falsk för. Som standard `Successful request == (resultCode < 400)` (om du inte har skrivit anpassad kod för att [filtrera](../../azure-monitor/app/api-filtering-sampling.md#filtering) eller generera egna [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) -anrop) för förfrågningar. 

Appens prestanda har ett typiskt beteende mönster. Vissa förfrågningar eller beroende anrop är svårare att Miss lyckas än andra. och den övergripande felfrekvensen kan gå upp när belastningen ökar. Smart identifiering använder Machine Learning för att hitta dessa avvikelser.

När telemetri kommer till Application Insights från din webbapp jämför Smart identifiering det aktuella beteendet med de mönster som visas under de senaste dagarna. Om en onormal ökning av felfrekvensen observeras genom jämförelse med tidigare prestanda utlöses en analys.

När en analys utlöses utför tjänsten en kluster analys på den misslyckade begäran, för att försöka identifiera ett mönster med värden som karaktäriserar felen. I exemplet ovan har analysen identifierat att de flesta felen är en viss resultat kod, namn på begäran, server-URL-värd och roll instans. Analysen har som kontrast identifierat att egenskapen för klientens operativ system är distribuerad över flera värden och att den inte visas i listan.

När din tjänst instrumenteras med dessa telemetri, söker Analyzer efter ett undantag och ett beroende fel som är associerat med begär anden i klustret. det har identifierats tillsammans med ett exempel på alla spårnings loggar som är kopplade till dessa förfrågningar.

Den resulterande analysen skickas till dig som avisering, om du inte har konfigurerat den inte till.

Precis som [aviseringarna som du anger manuellt](../../azure-monitor/app/alerts.md)kan du kontrol lera aviseringens status och konfigurera den på bladet aviseringar på din Application Insights resurs. Men till skillnad från andra aviseringar behöver du inte konfigurera eller konfigurera Smart identifiering. Om du vill kan du inaktivera den eller ändra dess mål-e-postadresser.

### <a name="alert-logic-details"></a>Information om aviserings logik

Aviseringarna utlöses av vår leverantörsspecifika Machine Learning-algoritm så att vi inte kan dela den exakta implementerings informationen. Med detta, förstår vi att du ibland behöver veta mer om hur den underliggande logiken fungerar. De primära faktorer som utvärderas för att avgöra om en avisering ska utlösas är: 

* Analys av misslyckad procent andel begär Anden/beroenden i en rullande tids period på 20 minuter.
* En jämförelse av antalet misslyckade procent av de senaste 20 minuterna i priset under de senaste 40 minuterna och de senaste sju dagarna och letar efter betydande avvikelser som överstiger X-gånger som standard avvikelsen.
* Använder en anpassad gräns för minsta antal misslyckade procent, vilket varierar beroende på appens volym för begär Anden/beroenden.

## <a name="configure-alerts"></a>Konfigurera varningar
Du kan inaktivera Smart identifiering, ändra e-postmottagare, skapa en webhook eller välja mer detaljerade aviserings meddelanden.

Öppna sidan aviseringar. Fel avvikelser inkluderas tillsammans med eventuella aviseringar som du har angett manuellt, och du kan se om det är i aviserings läget för närvarande.

![På sidan Översikt klickar du på aviserings panelen. Eller på någon av mått sidan klickar du på knappen aviseringar.](./media/proactive-failure-diagnostics/021.png)

Klicka på aviseringen för att konfigurera den.

![Konfiguration](./media/proactive-failure-diagnostics/032.png)

Observera att du kan inaktivera Smart identifiering, men du kan inte ta bort den (eller skapa en annan).

#### <a name="detailed-alerts"></a>Detaljerade aviseringar
Om du väljer "Hämta mer detaljerad diagnostik" kommer e-postmeddelandet att innehålla mer diagnostikinformation. Ibland kan du diagnostisera problemet direkt från data i e-postmeddelandet.

Det finns en liten risk för att den mer detaljerade aviseringen kan innehålla känslig information, eftersom den innehåller undantags-och spårnings meddelanden. Detta sker dock bara om din kod kan tillåta känslig information i dessa meddelanden.

## <a name="triaging-and-diagnosing-an-alert"></a>Sorterar och diagnostisera en avisering
En varning anger att en onormal ökning av frekvensen för misslyckade begär Anden upptäcktes. Det är troligt att det finns problem med din app eller dess miljö.

I procent av begär Anden och antalet användare som påverkas kan du bestämma hur brådskande problemet är. I exemplet ovan jämförs fel frekvensen på 22,5% med en normal taxa på 1%, vilket innebär att något är skadat. Å andra sidan påverkades bara 11 användare. Om det vore din app kan du utvärdera hur allvarligt det är.

I många fall kommer du att kunna diagnostisera problemet snabbt från namn, undantag, beroende fel och spårnings data som tillhandahålls.

Det finns andra led trådar. Till exempel är beroende fel frekvensen i det här exemplet samma som i undantags frekvensen (89,3%). Detta föreslår att undantaget uppstår direkt från beroende fel – vilket ger dig en tydlig uppfattning om var du ska börja titta på koden.

Länkarna i varje avsnitt tar dig direkt till en [Sök sida](../../azure-monitor/app/diagnostic-search.md) som filtrerats efter relevanta begär Anden, undantag, beroenden eller spårningar för att undersöka ytterligare. Du kan också öppna [Azure Portal](https://portal.azure.com), navigera till Application Insights resurs för din app och öppna bladet med problem.

I det här exemplet öppnas länken Application Insights Sök på bladet "Visa beroenden om felet". Den visar SQL-instruktionen som har ett exempel på rotor saken: NULL-värden tillhandahölls i obligatoriska fält och godkändes inte i verifieringen under Spara-åtgärden.

![Diagnostiksökning](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Granska nya aviseringar

Klicka på **Smart identifiering** för att komma till den senaste aviseringen:

![Aviserings Sammanfattning](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Vad är skillnaden...
Smart identifiering av fel avvikelser kompletterar andra liknande, men distinkta funktioner i Application Insights.

* [Mått varningar](../../azure-monitor/app/alerts.md) anges av dig och kan övervaka en mängd olika mått, till exempel processor användning, begär ande frekvens, sid inläsnings tider och så vidare. Du kan använda dem för att varna dig, till exempel om du behöver lägga till fler resurser. Som kontrast täcker Smart identifiering av fel avvikelser ett litet antal kritiska mått (för närvarande endast misslyckade begär Anden), utformade för att meddela dig i nära real tid när webbappens misslyckade begär Anden ökar avsevärt jämfört med webbappens normalt beteende.

    Smart identifiering justerar automatiskt tröskelvärdet som svar på rådande villkor.

    Smart identifiering startar diagnostiken åt dig.
* [Smart identifiering av prestanda avvikelser](proactive-performance-diagnostics.md) använder också Machine Intelligence för att identifiera ovanliga mönster i dina mått och ingen konfiguration krävs av dig. Men till skillnad från Smart identifiering av fel avvikelser är syftet med Smart identifiering av prestanda avvikelser att hitta segment i användnings grenen som kan vara dåligt Betjänad, till exempel genom vissa sidor i en speciell typ av webbläsare. Analysen utförs dagligen och om ett resultat hittas är det troligt vis mycket mindre brådskande än en avisering. I motsats sker analysen av fel avvikelser kontinuerligt på inkommande telemetri och du får ett meddelande inom några minuter om Server fel frekvensen är större än förväntat.

## <a name="if-you-receive-a-smart-detection-alert"></a>Om du får en avisering om Smart identifiering
*Varför har jag fått den här aviseringen?*

* Vi har identifierat en onormal ökning av antalet misslyckade begär Anden jämfört med den normala bas linjen för föregående period. Efter analysen av fel och tillhör ande telemetri tror vi att det finns ett problem som du bör titta på.

*Betyder meddelandet att jag definitivt har ett problem?*

* Vi försöker Varna vid program avbrott eller försämring, men bara du kan helt förstå semantiken och påverkan på appen eller användarna.

*Så kommer du att kunna titta på mina data?*

* Nej. Tjänsten är helt automatisk. Endast du får meddelanden. Dina data är [privata](../../azure-monitor/app/data-retention-privacy.md).

*Måste jag prenumerera på den här aviseringen?*

* Nej. Varje program som skickar begär ande telemetri har aviserings regeln för smart identifiering.

*Kan jag avbryta prenumerationen eller hämta meddelanden som skickas till mina kollegor i stället?*

* Ja, i varnings regler klickar du på regeln för smart identifiering för att konfigurera den. Du kan inaktivera aviseringen eller ändra mottagare för aviseringen.

*Jag förlorade e-postmeddelandet. Var hittar jag meddelanden i portalen?*

* I aktivitets loggarna. I Azure öppnar du Application Insights resursen för din app och väljer sedan aktivitets loggar.

*Några av aviseringarna är om kända problem och jag vill inte ta emot dem.*

* Vi har undervisningen av aviseringar i vår efter släpning.

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att inspektera Telemetrin från din app:

* [Metric Explorer](../../azure-monitor/app/metrics-explorer.md)
* [Sök Utforskaren](../../azure-monitor/app/diagnostic-search.md)
* [Analys-kraftfullt frågespråk](../../azure-monitor/log-query/get-started-portal.md)

Smart identifiering är helt automatisk. Men kanske vill du konfigurera ytterligare aviseringar?

* [Manuellt konfigurerade mått varningar](../../azure-monitor/app/alerts.md)
* [Webb test för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
