---
title: Felsöka prestandaförsämring
description: Ta reda på hur du felsöker problem med långsamma appprestanda i Azure App Service, inklusive övervakning av appbeteende, insamling av data och minska problemet.
tags: top-support-issue
keywords: webbappprestanda, långsam app, app långsam
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688304"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Felsöka problem med långsamma appprestanda i Azure App Service
Den här artikeln hjälper dig att felsöka problem med långsamma appprestanda i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**.

## <a name="symptom"></a>Symptom
När du bläddrar i appen läses sidorna in långsamt och ibland timeout.

## <a name="cause"></a>Orsak
Det här problemet orsakas ofta av problem på programnivå, till exempel:

* nätverksförfrågningar tar lång tid
* programkod eller databasfrågor är ineffektiva
* program med hög minne/CPU
* programmet kraschar på grund av ett undantag

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Felsökning kan delas in i tre olika uppgifter, i sekventiell ordning:

1. [Observera och övervaka programbeteende](#observe)
2. [Samla in data](#collect)
3. [Minska problemet](#mitigate)

[App Service](overview.md) ger dig olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka applikationsbeteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure offentliggör varje gång det sker ett avbrott i tjänsten eller prestandaförsämring. Du kan spåra tjänstens hälsotillstånd på [Azure-portalen](https://portal.azure.com/). Mer information finns i [Spåra tjänstens hälsotillstånd](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Övervaka din app
Med det här alternativet kan du ta reda på om ditt program har några problem. Klicka på panelen **Begäranden och fel** i appens blad. Bladet **Mått** visar alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för din app är

* Genomsnittligt arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminne
* Begäranden

![övervaka appens prestanda](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka appar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Övervaka webbslutpunktsstatus
Om du kör appen på **prisnivån Standard** kan du med App Service övervaka två slutpunkter från tre geografiska platser.

Slutpunktsövervakning konfigurerar webbtester från geodistribuerade platser som testar svarstid och drifttid för webbadresser. Testet utför en HTTP GET-åtgärd på webb-URL:en för att bestämma svarstid och drifttid från varje plats. Varje konfigurerad plats kör ett test var femte minut.

Drifttiden övervakas med hjälp av HTTP-svarskoder och svarstiden mäts i millisekunder. Ett övervakningstest misslyckas om HTTP-svarskoden är större än eller lika med 400 eller om svaret tar mer än 30 sekunder. En slutpunkt anses vara tillgänglig om dess övervakningstester lyckas från alla angivna platser.

Information om hur du konfigurerar den finns [i Övervaka appar i Azure App Service](web-sites-monitor.md).

Se även [Hålla Azure-webbplatser upp plus slutpunktsövervakning - med Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) för en video på slutpunktsövervakning.

#### <a name="application-performance-monitoring-using-extensions"></a>Övervakning av programmets prestanda med tillägg
Du kan också övervaka programmets prestanda med hjälp av ett *webbplatstillägg*.

Varje App Service-app tillhandahåller en utökningsbar hanteringsslutpunkt som gör att du kan använda en kraftfull uppsättning verktyg som distribueras som platstillägg. Tillägg inkluderar: 

- Källkodsredigerare som [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Hanteringsverktyg för anslutna resurser, till exempel en MySQL-databas som är ansluten till en app.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) är ett webbplatstillägg för prestandaövervakning som också är tillgängligt. Om du vill använda Application Insights återskapar du koden med en SDK. Du kan också installera ett tillägg som ger åtkomst till ytterligare data. Med SDK kan du skriva kod för att övervaka appens användning och prestanda mer i detalj. Mer information finns [i Övervaka prestanda i webbprogram](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in uppgifter
App Service tillhandahåller diagnostikfunktioner för loggningsinformation från både webbservern och webbprogrammet. Informationen är uppdelad i webbserverdiagnostik och programdiagnostik.

#### <a name="enable-web-server-diagnostics"></a>Aktivera diagnostik för webbserver
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** - Detaljerad felinformation för HTTP-statuskoder som indikerar ett fel (statuskod 400 eller senare). Detta kan innehålla information som kan hjälpa till att avgöra varför servern returnerade felkoden.
* **Spårning av misslyckade begäranden** - Detaljerad information om misslyckade begäranden, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Detta kan vara användbart om du försöker förbättra appens prestanda eller isolera vad som orsakar ett specifikt HTTP-fel.
* **Webbserverloggning** - Information om HTTP-transaktioner med w3c-utökat loggfilformat. Detta är användbart när du fastställer övergripande appmått, till exempel antalet hanterade begäranden eller hur många begäranden som kommer från en viss IP-adress.

#### <a name="enable-application-diagnostics"></a>Aktivera programdiagnostik
Det finns flera alternativ för att samla in programprestandadata från App Service, profilera ditt program live från Visual Studio eller ändra programkoden för att logga mer information och spårningar. Du kan välja alternativ baserat på hur mycket åtkomst du har till programmet och vad du observerade från övervakningsverktygen.

##### <a name="use-application-insights-profiler"></a>Använda Profiler för programstatistik
Du kan aktivera Application Insights Profiler för att börja samla in detaljerade prestandaspårningar. Du kan komma åt spår som tagits upp till fem dagar sedan när du behöver undersöka problem som hänt tidigare. Du kan välja det här alternativet så länge du har åtkomst till appens Application Insights-resurs på Azure-portalen.

Application Insights Profiler tillhandahåller statistik över svarstid för varje webbsamtal och spårningar som anger vilken kodrad som orsakade de långsamma svaren. Ibland är App Service-appen långsam eftersom viss kod inte skrivs på ett performant sätt. Exempel är sekventiell kod som kan köras parallellt och oönskade databaslåskonkurrensningar. Om du tar bort dessa flaskhalsar i koden ökar appens prestanda, men de är svåra att identifiera utan att ställa in avancerade spårningar och loggar. Spårningarna som samlas in av Application Insights Profiler hjälper till att identifiera de kodrader som saktar ner programmet och övervinner den här utmaningen för App Service-appar.

 Mer information finns [i Profilering av liveappar i Azure App Service med Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Använda fjärrprofilering
I Azure App Service kan webbappar, API-appar, mobila bakåtslut och WebJobs fjärrprofileras. Välj det här alternativet om du har tillgång till appresursen och du vet hur du återskapar problemet, eller om du vet det exakta tidsintervallet som prestandaproblemet inträffar.

Fjärrprofilering är användbart om CPU-användningen av processen är hög och processen körs långsammare än förväntat, eller svarstiden för HTTP-begäranden är högre än normalt, kan du fjärrprofilera din process och få CPU-samplingsanropsstaplarna för att analysera processen aktivitet och kod heta sökvägar.

Mer information finns [i Support för fjärrprofilering i Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ställ in diagnostikspårningar manuellt
Om du har tillgång till källkoden för webbprogrammet kan du samla in information som produceras av ett webbprogram. ASP.NET program kan använda `System.Diagnostics.Trace` klassen för att logga information till programdiagnostikloggen. Du måste dock ändra koden och distribuera om programmet. Den här metoden rekommenderas om appen körs på en testmiljö.

Detaljerade instruktioner om hur du konfigurerar ditt program för loggning finns [i Aktivera diagnostikloggning för appar i Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Använda diagnostikverktyget
App Service ger en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan att någon konfiguration krävs. När du stöter på problem med din app, visar diagnostikverktyget vad som är fel för att guida dig till rätt information för att enklare och snabbare felsöka och lösa problemet.

Om du vill komma åt App Service-diagnostik navigerar du till appens tjänstapp eller App Service-miljö i [Azure-portalen](https://portal.azure.com). I den vänstra navigeringen, klicka på **Diagnostisera och lösa problem**.

#### <a name="use-the-kudu-debug-console"></a>Använda Kudu-felsökningskonsolen
App Service levereras med en felsökningskonsol som du kan använda för felsökning, utforska, ladda upp filer samt JSON-slutpunkter för att få information om din miljö. Den här konsolen kallas *Kudu-konsolen* eller *SCM-instrumentpanelen* för din app.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;Ditt appnamn>.scm.azurewebsites.net/**.

Några av de saker som Kudu ger är:

* miljöinställningar för ditt program
* loggström
* diagnostisk dump
* felsökningskonsol där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan användbar funktion i Kudu är att om ditt program kastar första chansen undantag, kan du använda Kudu och SysInternals verktyget Procdump för att skapa minnesdumpar. Dessa minnesdumpar är ögonblicksbilder av processen och kan ofta hjälpa dig att felsöka mer komplicerade problem med din app.

Mer information om funktioner som är tillgängliga i Kudu finns i [Azure DevOps-verktyg som du bör känna till om](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Minska problemet
#### <a name="scale-the-app"></a>Skala appen
I Azure App Service, för ökad prestanda och dataflöde, kan du justera den skala som du kör ditt program. Att skala upp en app innebär två relaterade åtgärder: att ändra apptjänstplanen till en högre prisnivå och konfigurera vissa inställningar när du har växlat till den högre prisnivån.

Mer information om skalning finns [i Skala en app i Azure App Service](manage-scale-up.md).

Dessutom kan du välja att köra programmet på mer än en instans. Skala ut inte bara ger dig mer bearbetningskapacitet, men ger dig också en viss mängd feltolerans. Om processen går ner på en instans fortsätter de andra instanserna att betjäna begäranden.

Du kan ställa in skalningen som manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd AutoHeal
AutoHeal återvinner arbetsprocessen för din app baserat på inställningar du väljer (som konfigurationsändringar, begäranden, minnesbaserade gränser eller den tid som krävs för att utföra en begäran). För det mesta, återvinna processen är det snabbaste sättet att återhämta sig från ett problem. Även om du alltid kan starta om appen direkt i Azure-portalen, gör AutoHeal det automatiskt för dig. Allt du behöver göra är att lägga till några utlösare i roten web.config för din app. Dessa inställningar skulle fungera på samma sätt även om ditt program inte är en .NET-app.

Mer information finns i Automatisk läkning av [Azure-webbplatser](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Starta om appen
Omstart är ofta det enklaste sättet att återställa från engångsproblem. På [Azure-portalen](https://portal.azure.com/)har du möjlighet att stoppa eller starta om appen på appens blad.

 ![starta om appen för att lösa prestandaproblem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Du kan också hantera din app med Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).
