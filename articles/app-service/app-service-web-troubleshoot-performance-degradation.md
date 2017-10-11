---
title: "Långsam web app i App Service | Microsoft Docs"
description: "Den här artikeln hjälper dig att felsöka problem med långsam web app prestanda i Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "Web app prestanda, långsam app app som är långsam"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Felsöka problem med långsam web app prestanda i Azure App Service
Den här artikeln hjälper dig att felsöka problem med långsam web app prestanda i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **Get Support**.

## <a name="symptom"></a>Symtom
När du bläddrar webbprogrammet sidor belastningen långsamt och ibland timeout.

## <a name="cause"></a>Orsak
Det här problemet beror ofta på nivån programproblem som:

* nätverksbegäranden tar lång tid
* program-kod eller database-frågor som är ineffektiv
* program med hög minne/processor
* programmet kraschar på grund av ett undantag

## <a name="troubleshooting-steps"></a>Felsökningssteg
Felsökning kan delas in i tre olika uppgifter i sekventiell ordning:

1. [Observera och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service Web Apps](/services/app-service/web/) ger olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure publicizes varje gång en tjänst avbrott eller prestanda försämras. Du kan spåra hälsotillståndet för tjänsten på den [Azure-portalen](https://portal.azure.com/). Mer information finns i [spåra tjänstens hälsa](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Övervaka ditt webbprogram
Det här alternativet kan du ta reda på om ditt program är har problem. I din webbapps blad klickar du på den **begäranden och fel** panelen. Den **mått** bladet visar alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för webbappen

* Genomsnittlig minne arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminnet för minne
* Begäranden

![övervaka prestanda för web app](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Mer information finns i:

* [Övervakaren Web Apps i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Övervaka status på slutpunkt
Om du kör ditt webbprogram i den **Standard** prisnivån, Web Apps kan du övervaka två slutpunkter från tre geografiska platser.

Slutpunktsövervakning konfigurerar webbtester från fördelade platser som testar svarstid och upptid för webbadresser. Testet utför en HTTP GET-åtgärd på Webbadressen för att fastställa svarstid och drifttid från varje plats. Varje konfigurerad plats kör ett test var femte minut.

Drifttid kontrolleras med hjälp av HTTP-svarskoder och svarstid mäts i millisekunder. En övervakning testet misslyckas om HTTP-svarskoden är större än eller lika med 400 eller om svaret tar mer än 30 sekunder. En slutpunkt anses vara tillgänglig om övervakning testerna lyckas från angivna platser.

Om du vill konfigurera, se [övervaka appar i Azure App Service](web-sites-monitor.md).

Se även [hålla Azure-webbplatser in plus Slutpunktsövervakning - med Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) en video om slutpunktsövervakning.

#### <a name="application-performance-monitoring-using-extensions"></a>Övervakning av programprestanda med tillägg
Du kan också övervaka programmets prestanda genom att använda *plats tillägg*.

Varje App Service webbapp tillhandahåller en utökningsbar management-slutpunkt som du kan använda en kraftfull uppsättning av verktyg som distribueras som webbplatstillägg. Tillägg är: 

- Källan kod redigerare som [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Hanteringsverktyg för anslutna resurser, till exempel en MySQL-databas som är ansluten till ett webbprogram.

[Azure Application Insights](/services/application-insights/) och [New Relic](/marketplace/partners/newrelic/newrelic/) finns två webbplatstillägg som är tillgängliga för prestandaövervakning. För att använda New Relic måste installera du en agent vid körning. Om du vill använda Azure Application Insights du återskapar din kod med en SDK och du kan också installera ett tillägg som ger åtkomst till ytterligare data. SDK kan du skriva kod för att övervaka användningen och prestanda för din app i detalj.

Om du vill använda Application Insights finns [övervaka prestanda i webbprogram](../application-insights/app-insights-web-monitor-performance.md).

Om du vill använda New Relic finns [nya Relic prestanda programhantering på Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in data
Web Apps miljön innehåller diagnostikfunktion för att logga information från både webbservern och webbprogrammet. Informationen är indelade i web serverdiagnostik- och programdiagnostik.

#### <a name="enable-web-server-diagnostics"></a>Aktivera web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -information om felet för HTTP-statuskoder som indikerar att en (statuskod 400 eller högre). Detta kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.
* **Kunde inte begäran spårning** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och tidsåtgång i varje komponent. Detta kan vara användbart om du vill förbättra app eller isolera vad som orsakar ett specifikt HTTP-fel.
* **Web Server-loggning** -Information om HTTP-transaktioner med W3C utökat loggfilsformat. Detta är användbart när du fastställer övergripande web app mätvärden, till exempel antalet förfrågningar som hanteras eller hur många förfrågningar som kommer från en specifik IP-adress.

#### <a name="enable-application-diagnostics"></a>Aktivera programdiagnostik
Det finns flera alternativ för att samla in prestandadata för programmet från Web Apps profil programmet live från Visual Studio eller ändra din programkod för att logga mer information och spår. Du kan välja alternativ baserat på verktyg du hur mycket åtkomst till programmet och vad du har sett från övervakningen.

##### <a name="use-application-insights-profiler"></a>Använda Application Insights Profiler
Du kan aktivera Application Insights profileraren att börja samla in spårningar detaljerad prestanda. Du kan komma åt spårningar fångas upp till fem dagar sedan när du behöver undersöka problem har inträffat i förflutna. Du kan välja det här alternativet så länge som du har åtkomst till webbappen Application Insights-resurs på Azure-portalen.

Application Insights Profiler innehåller statistik om svarstiden för varje webbtjänst-anrop och spår som visar vilka kodrad orsakas långsamt svar. Ibland App Service-appen är långsam eftersom vissa kod inte skrivs i en performant sätt. Exempel är sekventiella kod som kan köras i parallella och oönskat databasen Lås contentions. Om du tar bort dessa flaskhalsar i koden ökar appens prestanda, men de är svåra att upptäcka utan att ställa in avancerade spårningar och loggar. Spårningssessioner samlas in av Application Insights Profiler kan identifiera rader med kod som långsammare programmet och lösa denna utmaning för Apptjänst-appar.

 Mer information finns i [Profiling live Azure-webbappar med Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Använd Remote profilering
I Azure App Service Web Apps API Apps och WebJobs kan vara från en fjärrdator i listan. Välj det här alternativet om du har åtkomst till webbresursen för appen och du vet hur du återskapa problemet eller om du vet exakt tidsintervallet prestandaproblem händer.

Remote Profiling är användbart om CPU-användningen för processen är hög och processen körs långsammare än förväntat eller svarstiden för HTTP-begäranden är högre än normalt, du via fjärranslutning kan profilen processen och få anrop-stackar CPU samplingsfrekvensen för att analysera processen aktivitet och koden varm sökvägar.

Mer information finns i [Remote profilering stöd i Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ställ in diagnostikspår manuellt
Om du har åtkomst till web-programmets källkod kan du samla in information som produceras av ett webbprogram i Application diagnostics. ASP.NET-program kan använda den `System.Diagnostics.Trace` klassen för att logga information till diagnostik programloggen. Men måste du ändra koden och distribuera ditt program. Den här metoden rekommenderas om din app körs på en testmiljö.

Detaljerade instruktioner om hur du konfigurerar ditt program för loggning finns [aktivera diagnostikloggning för web apps i Azure App Service](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Använda portalen för Azure App Service-stöd
Web Apps ger dig möjlighet att felsöka problem med ditt webbprogram genom att titta på http-loggar, händelseloggar, process Dumpar och mycket mer. Du kan komma åt den här informationen med hjälp av vår portal Support vid **http://&lt;appens namn >.scm.azurewebsites.net/Support**

Portal för Azure App Service-stöd ger tre separata flikar som stöd för ett vanligt scenario för felsökning tre steg:

1. Se aktuella beteende
2. Analysera genom att samla in diagnostikinformation och köra de inbyggda analyzers
3. Minimera

Om problemet sker just nu, klickar du på **analysera** > **diagnostik** > **diagnostisera nu** för att skapa en diagnostisk session du, som samlar in HTTP loggar, loggar i Loggboken, minne Dumpar, PHP felloggarna och PHP bearbeta rapporten.

När data samlas in, support portal körs en analys på data och ger dig en HTML-rapport.

Om du vill hämta data, som standard lagras den i mappen D:\home\data\DaaS.

Mer information om support-portal Azure App Service finns [nya uppdateringar till stöd för plats-tillägg för Azure Websites](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Använd Kudu-Felsökningskonsolen
Web Apps levereras med en Felsökningskonsolen som du kan använda för felsökning, utforska, ladda upp filer, samt JSON-slutpunkter för att hämta information om din miljö. Den här konsolen kallas den *Kudu konsolen* eller *SCM instrumentpanelen* för ditt webbprogram.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;appens namn >.scm.azurewebsites.net/**.

Några av de saker som Kudu tillhandahåller är:

* inställningar för ditt program
* loggström
* diagnostiska dump
* Felsöka konsolen där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan användbar funktion i Kudu är att, om ditt program är att första chans undantag, kan du använda Kudu och verktyget SysInternals Procdump att skapa minne Dumpar. Dessa minnesdumpar är ögonblicksbilder av processen och ofta kan hjälpa dig att felsöka mer komplicerade problem med ditt webbprogram.

Mer information om funktionerna i Kudu finns [Azure webbplatser Team Services-verktyg som du bör känna till om](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Åtgärda problemet
#### <a name="scale-the-web-app"></a>Skala webbprogrammet
I Azure App Service kan för bättre prestanda och genomflöde, du justera skalan där du kör programmet. Skala upp ett webbprogram omfattar två relaterade åtgärder: ändra din programtjänstplan till en högre prisnivå och konfigurera vissa inställningar när du har växlat till högre prisnivå.

Läs mer om att skala [skala en webbapp i Azure App Service](web-sites-scale.md).

Dessutom kan du välja att köra programmet på mer än en instans. Skala ut inte bara ger mer bearbetningskapacitet, men ger dig även vissa delar av feltolerans. Andra instanser fortsätter att betjäna förfrågningar om processen kraschar på en instans.

Du kan ange skalning till manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd säkert att AutoHeal
Säkert att AutoHeal återanvänder arbetsprocessen för din app baserat på dina inställningar (till exempel konfigurationsändringar begäranden, minnesbaserade gränser eller den tid som krävs för att utföra en begäran). I de flesta fall, är omarbetning av processen det snabbaste sättet att återställa från ett problem. Även om du kan alltid starta om webbprogrammet från direkt i Azure-portalen, fungerar säkert att AutoHeal automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot-web.config för ditt webbprogram. De här inställningarna fungerar på samma sätt, även om programmet inte är en .net-app.

Mer information finns i [automatisk återställning Azure webbplatser](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Starta om webbprogrammet
Starta om är ofta det enklaste sättet att återställa engångsproblem. På den [Azure-portalen](https://portal.azure.com/), på bladet för ditt webbprogram har du alternativ för att stoppa eller starta om din app.

 ![Starta om webbprogram för att lösa prestandaproblem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Du kan också hantera ditt webbprogram med hjälp av Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).
